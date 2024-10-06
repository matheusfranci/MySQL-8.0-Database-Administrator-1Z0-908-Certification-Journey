***Habilite o gtid_mode, configure o enforce-gtid-consistency e o server_id no arquivo de configuração my.cnf***
```ini
[mysqld]
gtid_mode=ON
enforce-gtid-consistency=ON
server_id=1
```

***Reinicie o a instância***
```bash
systemctl restart mysqld
```

***Criação e concessão de privilégios ao usuário da replicação
```SQL
CREATE USER 'clusteradmin'@'%' IDENTIFIED BY '********';
GRANT ALL PRIVILEGES ON *.* TO 'clusteradmin'@'%';
GRANT CLONE_ADMIN, CONNECTION_ADMIN, CREATE USER, EXECUTE, FILE, GROUP_REPLICATION_ADMIN, PERSIST_RO_VARIABLES_ADMIN, PROCESS, RELOAD, REPLICATION CLIENT, REPLICATION SLAVE, REPLICATION_APPLIER, REPLICATION_SLAVE_ADMIN, ROLE_ADMIN, SELECT, SHUTDOWN, SYSTEM_VARIABLES_ADMIN ON *.* TO 'clusteradmin'@'%' WITH GRANT OPTION;
GRANT DELETE, INSERT, UPDATE ON mysql.* TO 'clusteradmin'@'%';
GRANT ALTER, ALTER ROUTINE, CREATE, CREATE ROUTINE, CREATE TEMPORARY TABLES, CREATE VIEW, DELETE, DROP, EVENT, EXECUTE, INDEX, INSERT, LOCK TABLES, REFERENCES, SHOW VIEW, TRIGGER, UPDATE ON mysql_innodb_cluster_metadata.* TO 'clusteradmin'@'%' WITH GRANT OPTION;
GRANT ALTER, ALTER ROUTINE, CREATE, CREATE ROUTINE, CREATE TEMPORARY TABLES, CREATE VIEW, DELETE, DROP, EVENT, EXECUTE, INDEX, INSERT, LOCK TABLES, REFERENCES, SHOW VIEW, TRIGGER, UPDATE ON mysql_innodb_cluster_metadata_bkp.* TO 'clusteradmin'@'%' WITH GRANT OPTION;
GRANT ALTER, ALTER ROUTINE, CREATE, CREATE ROUTINE, CREATE TEMPORARY TABLES, CREATE VIEW, DELETE, DROP, EVENT, EXECUTE, INDEX, INSERT, LOCK TABLES, REFERENCES, SHOW VIEW, TRIGGER, UPDATE ON mysql_innodb_cluster_metadata_previous.* TO 'clusteradmin'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

***Acesse o mysqlshell***
```bash
mysqlsh --uri clusteradmin@192.168.1.254:3306
```

***Mude para o JavaScript Mode***
```bash
\js
```

***Valide a configuração da instância***
```JS
dba.checkInstanceConfiguration('clusteradmin@192.168.1.254:3306');
```

***Crie o cluster***
```JS
var cluster = dba.createCluster('node_01');
```

***Conecte-se em outro nó e repita o procedimento adicionando os mesmos parâmetros no my.cnf alterando apenas o server_id
```ini
[mysqld]
gtid_mode=ON
enforce-gtid-consistency=ON
server_id=2
report_host = 192.168.1.244
```

***Acesse o mysqlshell***
```bash
mysqlsh --uri clusteradmin@192.168.1.254:3306
```

***Mude para o JavaScript Mode***
```bash
\js
```
***Adicione a instancia 2 ao cluster
```JS
cluster.addInstance('clusteradmin@192.168.1.244:3306');
```

```LOG
 MySQL  192.168.1.254:3306 ssl  JS > cluster.addInstance('clusteradmin@192.168.1.244:3306');

WARNING: A GTID set check of the MySQL instance at '192.168.1.244:3306' determined that it contains transactions that do not originate from the cluster, which must be discarded before it can join the cluster.

Instance '192.168.1.244:3306' has the following errant GTIDs that do not exist in the cluster:
 4b53b93e-82e5-11ef-b369-0800274dadb2:1-10,
72dda909-842b-11ef-b57f-0800274dadb2:1-62,
a4861187-8437-11ef-b19a-0800274dadb2:1-63

WARNING: Discarding these extra GTID events can either be done manually or by completely overwriting the state of '192.168.1.244:3306' with a physical snapshot from an existing cluster member. To use this method by default, set the 'recoveryMethod' option to 'clone'.

Having extra GTID events is not expected, and it is recommended to investigate this further and ensure that the data can be removed prior to choosing the clone recovery method.

Please select a recovery method [C]lone/[A]bort (default Abort): C
Validating instance configuration at 192.168.1.244:3306...

This instance reports its own address as 192.168.1.244:3306

Instance configuration is suitable.
NOTE: Group Replication will communicate with other members using '192.168.1.244:3306'. Use the localAddress option to override.

* Checking connectivity and SSL configuration...

A new instance will be added to the InnoDB Cluster. Depending on the amount of
data on the cluster this might take from a few seconds to several hours.

Adding instance to the cluster...

Monitoring recovery process of the new cluster member. Press ^C to stop monitoring and let it continue in background.
Clone based state recovery is now in progress.

NOTE: A server restart is expected to happen as part of the clone process. If the
server does not support the RESTART command or does not come back after a
while, you may need to manually start it back.

* Waiting for clone to finish...
NOTE: 192.168.1.244:3306 is being cloned from masterdb:3306
** Stage DROP DATA: Completed
** Clone Transfer
    FILE COPY  ############################################################  100%  Completed
    PAGE COPY  ############################################################  100%  Completed
    REDO COPY  ############################################################  100%  Completed

NOTE: 192.168.1.244:3306 is shutting down...

* Waiting for server restart... ready
* 192.168.1.244:3306 has restarted, waiting for clone to finish...
** Stage RESTART: Completed
* Clone process has finished: 100.46 MB transferred in about 1 second (~100.46 MB/s)

State recovery already finished for '192.168.1.244:3306'

The instance '192.168.1.244:3306' was successfully added to the cluster.
```

***Validando o funcionamento via query***
```SQL
SELECT 
    MEMBER_ID,
    MEMBER_HOST,
    MEMBER_PORT,
    MEMBER_STATE,
    MEMBER_ROLE,
    MEMBER_VERSION
FROM 
    performance_schema.replication_group_members;
```

```LOG
+--------------------------------------+---------------+-------------+--------------+-------------+----------------+
| MEMBER_ID                            | MEMBER_HOST   | MEMBER_PORT | MEMBER_STATE | MEMBER_ROLE | MEMBER_VERSION |
+--------------------------------------+---------------+-------------+--------------+-------------+----------------+
| 4b53b93e-82e5-11ef-b369-0800274dadb2 | replicadb     |        3306 | ONLINE       | SECONDARY   | 9.0.1          |
| a192ca7f-7f3d-11ef-b233-0800271784d2 | masterdb      |        3306 | ONLINE       | PRIMARY     | 9.0.1          |
+--------------------------------------+---------------+-------------+--------------+-------------+----------------+
```

***Observações
Os servidores precisam se resolver por nomes, edite o /etc/hosts

 



SET GLOBAL gtid_purged = '4b53b93e-82e5-11ef-b369-0800274dadb2:1-10:1-10,72dda909-842b-11ef-b57f-0800274dadb2:1-62,a4861187-8437-11ef-b19a-0800274dadb2:1-63';
