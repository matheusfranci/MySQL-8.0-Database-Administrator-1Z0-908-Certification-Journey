***Criando e atribuindo o permissionamento correto aos arquivos e pasta criada (Master)***
```bash
mkdir /var/log/mysql/
sudo chown -R mysql:mysql /var/log/mysql/
sudo touch /var/log/mysql/mysql-bin.log
sudo chmod 660 /var/log/mysql/mysql-bin.log
```

***Configurando o my.cnf (Master)***
```ini
[mysqld]
server-id = 1
log_bin = /var/log/mysql/mysql-bin.log
binlog_do_db = nome_do_seu_banco_de_dados # Especifica o DB que será replicado
```

***Reiniciando o serviço do mysql (Master)***
```bash
sudo systemctl restart mysqld
```

***Criação e atribuição de permissão para usuário de replicação (Master)***
```SQL
CREATE USER 'replicador'@'%' IDENTIFIED BY '********';
GRANT REPLICATION SLAVE ON *.* TO 'replicador'@'%';
FLUSH PRIVILEGES;
```

***Obtendo a posição do log primário (Master)***
```SQL
SET GLOBAL read_only=ON; -- Travar as tabelas para garantir consistência
SHOW VARIABLES LIKE '%read_only%'; -- Validar se está read only
FLUSH TABLES; --Limpe tabelas e logs para garantir que todas as alterações sejam gravadas no disco
FLUSH LOGS; 
SHOW MASTER STATUS; --OBS Na versão 9 usa-se o comando SHOW BINARY LOG STATUS;
```

***Fazendo backup lógico (Master)***
```bash
mysqldump -u adminuser -p --all-databases --master-data > dumpfile.sql
```

***Copiando para a replica (Master)
```bash
scp /user/dumpfile.sql user@192.168.1.244:/user
```

***Restauração (Replica)***
```bash
mysql -u user -p < /user/dumpfile.sql
```

***Criando e atribuindo o permissionamento correto aos arquivos e pasta criada(Replica)***
```bash
mkdir /var/log/mysql/
sudo chown -R mysql:mysql /var/log/mysql/
sudo touch /var/log/mysql/mysql-relay-bin.log
sudo chmod 660 /var/log/mysql/mysql-relay-bin.log

***Configurando o my.cnf (Replica)***
```ini
[mysqld]
server-id = 2
relay_log = /var/log/mysql/mysql-relay-bin.log
```

***Reinicie o serviço do mysql (Replica)***
```bash
systemctl restart mysqld
```

***Voltando para read write (Master)***
```SQL
SET GLOBAL read_only=OFF; -- Travar as tabelas para garantir consistência
SHOW VARIABLES LIKE '%read_only%'; -- Validando
```

***Execute de dentro do mysql (Replica)***
```SQL
CHANGE REPLICATION SOURCE TO SOURCE_HOST = '192.168.1.254', -- IP do primário
    SOURCE_USER = 'replicador', -- Usuário criado no primário
    SOURCE_PASSWORD = '*********',
    SOURCE_LOG_FILE = 'mysql-bin.000023', -- Pega essa informação executando o comando SHOW BINARY LOG STATUS no primário, coluna File
    SOURCE_LOG_POS = 158, -- Pega essa informação executando o comando SHOW BINARY LOG STATUS no primário, coluna Position
	SOURCE_SSL = 1;  -- Esta linha ativa o uso de SSL, é necessária caso o usuário use o plugin caching_sha2_password que é default no mysql 9
```
	
***Verifica o status da replicação (Replica)***
```SQL
SHOW REPLICA STATUS\G
```

***Validando a replicação (MASTER)***
```SQL
SHOW REPLICAS;
```

***Testando a replicação (MASTER)***
-- Criação de tabela
```SQL
CREATE TABLE test_replication (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

-- Criação da procedure de teste***
```SQL
DELIMITER $$

CREATE PROCEDURE insert_test_data()
BEGIN
    DECLARE i INT DEFAULT 0;
    WHILE i < 100 DO
        INSERT INTO test_replication (name) VALUES (CONCAT('Name ', i + 1));
        SET i = i + 1;
    END WHILE;
END$$

DELIMITER ;
```

-- Executando a proc
```SQL
CALL insert_test_data();
```

-- Teste com o select (Replica)
```SQL
SELECT * FROM test_replication;
```
***Documentações***
https://dev.mysql.com/doc/refman/9.0/en/replication-howto-additionalslaves.html
https://dev.mysql.com/doc/refman/8.4/en/replication-administration-status.html
https://www.digitalocean.com/community/tutorials/how-to-set-up-replication-in-mysql
https://reintech.io/blog/configuring-master-slave-mysql-replication-rocky-linux-9
