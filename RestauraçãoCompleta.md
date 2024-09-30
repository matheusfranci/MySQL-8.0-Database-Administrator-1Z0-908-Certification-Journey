***1- Pare o mysql***
```bash
sudo systemctl stop mysqld
```

***2- Antes do restore é preciso aplicar o log ao backup. Para isso, execute o seguinte comando:***
```bash
mysqlbackup --user=root --password=230319Mf@ --host=localhost --backup-dir=/home/mat/backup/ apply-log
```

***3- Apagar ou mover o diretório de dados antigo***
```bash
mv /var/lib/mysql /var/lib/mysql_old
mkdir /var/lib/mysql
```

***4- Restaurar o backup***
```bash
mysqlbackup --backup-dir=/home/mat/backup/ --datadir=/var/lib/mysql/ copy-back
```

***5- Ajustar permissões***
```bash
chown -R mysql:mysql /var/lib/mysql
chmod -R 750 /var/lib/mysql
```

***6- Iniciar o MySQL***
```bash
sudo systemctl start mysqld
```
