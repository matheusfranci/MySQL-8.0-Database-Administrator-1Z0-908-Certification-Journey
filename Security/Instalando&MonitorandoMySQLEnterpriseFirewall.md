***Localize o arquivo linux_install_firewall.sql***
```bash
locate linux_install_firewall.sql
```

***Execute-o realizado upload para o mysql database***
```bash
mysql -u root -p  mysql < /usr/share/mysql-9.0/linux_install_firewall.sql
```

***O MySQL Enterprise Firewall não funciona junto com o query cache, valide os parâmetros***
```SQL
SHOW VARIABLES LIKE 'query_cache_type';
SHOW VARIABLES LIKE 'query_cache_size';
```

***Verificando se o firewall mode está ativo***
```SQL
SHOW GLOBAL VARIABLES LIKE 'mysql_firewall_mode';
```

***Caso não esteja, segue o work around. Adicione o parâmetro no my.cnf e reinicie a instância***
```ini
[mysqld]
mysql_firewall_mode=ON
```

```bash
systemctl restart mysqld
```

***Monitorando a atividade do firewall
```SQL
SHOW GLOBAL STATUS LIKE 'Firewall%';
```

***Links auxiliares***
https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-firewall.html
https://dev.mysql.com/doc/refman/5.7/en/firewall-usage.html
