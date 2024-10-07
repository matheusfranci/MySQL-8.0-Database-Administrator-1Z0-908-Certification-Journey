***Dropando o metadata schema***
```JS
dba.dropMetadataSchema();
```

***Recriando o cluster***
```JS
var cluster = dba.createCluster('cluster');
```

***Adicionando o nó secundário***
```JS
var cluster = dba.getCluster();
cluster.addInstance('clusteradmin@192.168.1.244:3306');
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
