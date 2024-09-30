***1- Pare o mysql***
```bash
sudo systemctl stop mysqld
```

```bash
***2- Apagar ou mover o diretório de dados antigo***
mv /var/lib/mysql /var/lib/mysql_old
mkdir /var/lib/mysql
```

***3- Aplicsndo log do backup full***
```bash
mysqlbackup --backup-dir=/home/mat/backup_full/2024-09-30_11-22-33 --datadir=/var/lib/mysql apply-log
```

***4- Restaurando o full***
```bash
mysqlbackup --backup-dir=/home/mat/backup_full/2024-09-30_11-22-33 --datadir=/var/lib/mysql copy-back
```

***5- Ajustando as permissões***
```bash
chown -R mysql:mysql /var/lib/mysql
chmod -R 750 /var/lib/mysql
```

***6- Aplicando log do incremental***
```bash
mysqlbackup --backup-dir=/home/mat/backup_full/2024-09-30_11-22-33 --incremental-backup-dir=/home/mat/backup_incr/2024-09-30_11-30-52 --datadir=/var/lib/mysql apply-incremental-backup
```
