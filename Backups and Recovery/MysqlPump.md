***1- Backup de um único banco:***
```bash
mysqlpump --user=SEU_USUARIO --password=SUA_SENHA --output-dir=/caminho/para/backup --include-databases=NOME_DO_BANCO --default-parallelism=4
```

***2- Restore de um único banco:***
```bash
mysqlpump --user=SEU_USUARIO --password=SUA_SENHA --directory=/caminho/para/backup --import
```

***3- Backup da instância inteira basta remover o --include-databases=NOME_DO_BANCO***
```bash
mysqlpump --user=SEU_USUARIO --password=SUA_SENHA --output-dir=/caminho/para/backup --default-parallelism=4 --compress --skip-definer
```

***4- Restore, nesse caso não há alteração porque ele busca o que está no --directory***
```bash
mysqlpump --user=SEU_USUARIO --password=SUA_SENHA --directory=/caminho/para/backup --import
```

***Observações:***
Na release note do Mysql 8.0 na página 26 há uma nota dizendo que ele seria depreciado

"Important Change: Since MySQL provides other means of performing database dumps and backups
with the same or additional functionality, including mysqldump and MySQL Shell Utilities, the
mysqlpump client utility program has become redundant, and is now deprecated. Invocation of this
program now produces a warning. You should keep in mind that mysqlpump is subject to removal in
a future version of MySQL, and move applications depending on it to another solution, such as those
mentioned previously. (WL #15652)"

***Referência***
https://dev.mysql.com/doc/refman/8.0/en/mysqlpump.html
https://downloads.mysql.com/docs/mysql-8.0-relnotes-en.pdf
