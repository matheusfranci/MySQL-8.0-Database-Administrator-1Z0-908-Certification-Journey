***1- Entre no mysqlsh
```bash
mysqlsh
```

***2- Conecte como root
```bash
\connect root@localhost -p
```

***3- Troque para o modo JavaScript
```bash
\js
```

***4- Faça o dump da instância inteira
```javascript
util.dumpInstance("/home/mat/util")
```

***5- Dump com options de paralelismo
```javascript
util.dumpInstance("/home/mat/util", { threads: 4});
```

***6- Retorne para o modo SQL
```bash
\SQL
```

***7- Habilitando o infile na sesssão
```SQL
SET GLOBAL local_infile = 'ON';
```

***8- Volte para o modo JavaScript
```bash
\js
```

***9- Execute o restore
```javascript
util.loadDump("/home/mat/util");
```

***Observações:***
Ele faz overwrite
Não precisei limpar o datadir ou parar o serviço do mysql
Há muitas options, inclusive de compressão e isolamento de bancos individuais, tabelas e outros objetos

***Link de apoio:***
https://dev.mysql.com/doc/mysql-shell/8.0/en/mysql-shell-utilities-dump-instance-schema.html
