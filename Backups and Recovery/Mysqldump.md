***1- Backup lógico padrão individual***
```bash
mysqldump -u root -p sakila > /home/mat/mysqldump_files/sakila_dump.sql
```

***2- Backup lógico com algumas options***
```bash
mysqldump -u root -p --routines --events --triggers sakila > /home/mat/mysqldump_files/sakila_dump_full.sql
```

***3- Backup lógico de todos bancos do cluster***
```bash
mysqldump -u root -p --routines --events --add-drop-database --add-drop-table --add-drop-trigger  --triggers --all-databases --compact > /home/mat/mysqldump_files/full_dump_compact_full.sql
```

***Dicionário de options***
https://dev.mysql.com/doc/refman/8.4/en/mysqldump.html
