***1- Backup full***
```bash
mysqlbackup --user=root --password=230319Mf@ --host=localhost --backup-dir=/home/mat/backup_full --with-timestamp backup
```

***2- Backup incremental***
```bash
mysqlbackup --user=root --password=230319Mf@ --host=localhost --incremental --incremental-backup-dir=/home/mat/backup_incr/ --incremental-base=dir:/home/mat/backup_full/2024-09-30_11-22-33 --backup-dir=/home/mat/backup_incr/ --with-timestamp backup
```

***3- Validando o backup full***
```bash
mysqlbackup --backup-dir=/home/mat/backup_full/2024-09-30_11-22-33 validate
```

***4- Validando o backup  incremental***
```bash
mysqlbackup --backup-dir=/home/mat/backup_incr/2024-09-30_11-30-52 validate
```
