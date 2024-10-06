***No my.cnf do servidor, adicione ou modifique as seguintes variáveis(Source):***
```ini
[mysqld]
gtid_mode=ON
enforce_gtid_consistency=ON
log_bin=binlog
log_slave_updates=ON
binlog_format=ROW
server_id=1  # ou outro valor único para o servidor Source
```

***Reinicie o servidor MySQL para aplicar as mudanças(Source):***
```bash
sudo systemctl restart mysqld
```

***Crie um usuário de replicação(Source):***
```SQL
CREATE USER 'replica_gtid'@'%' IDENTIFIED BY '********';
GRANT REPLICATION SLAVE ON *.* TO 'replica_gtid'@'%';
```

***Bloqueie para que o estado do banco de dados seja consistente(Source):***
```SQL
SET @@GLOBAL.read_only = ON;
SHOW Source STATUS; --OBS Na versão 9 usa-se o comando SHOW BINARY LOG STATUS;
```

```log
mysql> SHOW BINARY LOG STATUS;
+---------------+----------+--------------+------------------+----------------------------------------+
| File          | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set                      |
+---------------+----------+--------------+------------------+----------------------------------------+
| binlog.000017 |      198 |              |                  | a192ca7f-7f3d-11ef-b233-0800271784d2:1 |
+---------------+----------+--------------+------------------+----------------------------------------+
```

***Faça umm backup(Source):***
```bash
mysqldump --all-databases --source-data=2 --single-transaction --flush-logs --routines --triggers --events -u root -p > backup.sql
```

***Copie para a replica(Source):***
```bash
scp /user/backup.sql root@192.168.1.244:/user
```

***Restauração (Replica)***
```bash
mysql -u user -p < /user/backup.sql
```

***No my.cnf, adicione ou modifique as seguintes variáveis(Replica):***
```ini
[mysqld]
gtid_mode=ON
enforce_gtid_consistency=ON
log_bin=binlog
log_slave_updates=ON
binlog_format=ROW
server_id=2
```

***Reinicie a instância para aplicar as mudanças(Replica):***
```bash
sudo systemctl restart mysqld
```

*** Pare ambas as instâncias(Source e Replica):***
```bash
systemctl stop mysqld
```

***Reinicie ambos os servidores(Source e Replica):***
```bash
reboot
```

*** Ao retornar execute na instância o comandos(Replica):***
```SQL
CHANGE REPLICATION SOURCE TO
SOURCE_HOST='192.168.1.254',
SOURCE_PORT=3306,
SOURCE_USER='replica_gtid',
SOURCE_PASSWORD='*******',
SOURCE_SSL = 1,
SOURCE_AUTO_POSITION=1;
```

*** Inicie a replicaçãos(Replica):***
```SQL
START REPLICA;
```

***Observaçãos(Replica):***
```log
Caso apresente o erro abaixo, favor reiniciar a replica e executar os comandos novamente:
2024-10-06T18:07:17.569179Z 9 [ERROR] [MY-010717] [Repl] Error reading replica worker configuration
2024-10-06T18:07:17.569219Z 9 [ERROR] [MY-010418] [Repl] Error creating applier metadata: Failed to create the worker metadata repository structure.
```

Solução em alguns casos(Replica):
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

*** Retornando para read writes(Source):
```SQL
SET @@GLOBAL.read_only = OFF;
```

*** Testando a replicação(Source):

Crie um banco, uma tabela e insira registros
```SQL
CREATE DATABASE testdb;
USE testdb;
CREATE TABLE test_table (id INT PRIMARY KEY, name VARCHAR(50));
INSERT INTO test_table (id, name) VALUES (1, 'Teste');
```

Verifique se foi replicado(Replica):
```SQL
USE testdb;
SELECT * FROM test_table;
```

Outra maneira é validando o hasg do gtid(Source e Replica):
```SQL
SHOW VARIABLES LIKE 'gtid_executed';
```
No ambiente de teste, os valores precisam ser iguais. No entanto, em um ambiente produtivo, onde há um grande volume de transações por minuto, é natural que a replicação esteja atrasada. Isso é especialmente verdade se o servidor replica for subdimensionado, o que pode impactar as métricas de finanças operacionais (FinOps).

***Links:***
https://dev.mysql.com/doc/refman/8.4/en/replication-gtids-howto.html
https://airbyte.com/blog/replicating-mysql-a-look-at-the-binlog-and-gtids
