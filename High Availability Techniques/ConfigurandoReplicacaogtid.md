***No my.cnf do servidor master, adicione ou modifique as seguintes variáveis:***
```ini
[mysqld]
gtid_mode=ON
enforce_gtid_consistency=ON
log_bin=binlog
log_slave_updates=ON
binlog_format=ROW
server_id=1  # ou outro valor único para o servidor master
```

***Reinicie o servidor MySQL para aplicar as mudanças:***
```bash
sudo systemctl restart mysqld
```

***Crie um usuário de replicação:***
```SQL
CREATE USER 'replica_gtid'@'%' IDENTIFIED BY '********';
GRANT REPLICATION SLAVE ON *.* TO 'replica_gtid'@'%';
```

***Bloqueie o master para que o estado do banco de dados seja consistente:***
```SQL
SET @@GLOBAL.read_only = ON;
SHOW MASTER STATUS; --OBS Na versão 9 usa-se o comando SHOW BINARY LOG STATUS;
```

```log
mysql> SHOW BINARY LOG STATUS;
+---------------+----------+--------------+------------------+----------------------------------------+
| File          | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set                      |
+---------------+----------+--------------+------------------+----------------------------------------+
| binlog.000017 |      198 |              |                  | a192ca7f-7f3d-11ef-b233-0800271784d2:1 |
+---------------+----------+--------------+------------------+----------------------------------------+
```

***Faça umm backup do master***
```bash
mysqldump --all-databases --source-data=2 --single-transaction --flush-logs --routines --triggers --events -u root -p > backup.sql
```

***Copie para a replica***
```bash
scp /user/backup.sql root@192.168.1.244:/user
```

***Restauração (Replica)***
```bash
mysql -u user -p < /user/backup.sql
```

***No my.cnf do servidor replica, adicione ou modifique as seguintes variáveis:***
```ini
[mysqld]
gtid_mode=ON
enforce_gtid_consistency=ON
log_bin=binlog
log_slave_updates=ON
binlog_format=ROW
server_id=2
```

***Reinicie a instância de replica para aplicar as mudanças:***
```bash
sudo systemctl restart mysqld
```

*** Pare ambas as instâncias ***
```bash
systemctl stop mysqld
```

***Reinicie ambos os servidores***
```bash
reboot
```

*** Ao retornar execute na instância de replica o comando:***
```SQL
CHANGE REPLICATION SOURCE TO
SOURCE_HOST='192.168.1.254',
SOURCE_PORT=3306,
SOURCE_USER='replica_gtid',
SOURCE_PASSWORD='*******',
SOURCE_SSL = 1,
SOURCE_AUTO_POSITION=1;
```

*** Inicie a replicação***
```SQL
START REPLICA;
```

***Observação***
```log
Caso apresente o erro abaixo, favor reiniciar a replica e executar os comandos novamente:
2024-10-06T18:07:17.569179Z 9 [ERROR] [MY-010717] [Repl] Error reading replica worker configuration
2024-10-06T18:07:17.569219Z 9 [ERROR] [MY-010418] [Repl] Error creating applier metadata: Failed to create the worker metadata repository structure.
```

Solução em alguns casos:
```SQL
RESET REPLICA;

CHANGE REPLICATION SOURCE TO
SOURCE_HOST='192.168.1.254',
SOURCE_PORT=3306,
SOURCE_USER='replica_gtid',
SOURCE_PASSWORD='*******',
SOURCE_SSL = 1,
SOURCE_AUTO_POSITION=1;
```

*** Retornando para read write
```SQL
SET @@GLOBAL.read_only = OFF;
```

*** Testando a replicação

No source crie um banco, uma tabela e insira registros
```SQL
CREATE DATABASE testdb;
USE testdb;
CREATE TABLE test_table (id INT PRIMARY KEY, name VARCHAR(50));
INSERT INTO test_table (id, name) VALUES (1, 'Teste');
```

Na replica verifique se foi replicado
```SQL
USE testdb;
SELECT * FROM test_table;
```

Outra maneira é validando o hasg do gtid
```SQL
SHOW VARIABLES LIKE 'gtid_executed';
```
No ambiente de teste, os valores precisam ser iguais. No entanto, em um ambiente produtivo, onde há um grande volume de transações por minuto, é natural que a replicação esteja atrasada. Isso é especialmente verdade se o servidor replica for subdimensionado, o que pode impactar as métricas de finanças operacionais (FinOps).
