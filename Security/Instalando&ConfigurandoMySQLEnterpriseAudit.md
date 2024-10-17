***Localize o arquivo audit_log_filter_linux_install.sql***
```bash
audit_log_filter_linux_install.sql
```

***Execute-o realizado upload para o mysql database***
```bash
mysql -u root -p mysql < /usr/share/mysql-9.0/audit_log_filter_linux_install.sql
```

***Validando a ativação da auditoria***
```SQL
SELECT PLUGIN_NAME, PLUGIN_STATUS
FROM INFORMATION_SCHEMA.PLUGINS
WHERE PLUGIN_NAME LIKE 'audit%';
```

***Para evitar que o plug-in seja removido em tempo de execução, adicione a opção --audit-log no my.cnf***
```ini
audit-log=FORCE_PLUS_PERMANENT
```

***Reinicie o servidor para aplicar a alteração de configuração:***
```bash
systemctl restart mysqld
```

***Alterando o audit_log_format para json, adicione no my.cnf***
```ini
[mysqld]
audit_log_format=JSON
```

***Reinicie o servidor para aplicar a alteração de configuração:***
```bash
systemctl restart mysqld
```

*** Valide a alteração***
```SQL
SHOW VARIABLES LIKE 'audit_log_format';
```

### Funções de Leitura de Log de Auditoria

1. **`audit_log_read_bookmark()`**:
   - **Descrição**: Cria um marcador no log de auditoria, permitindo que você leia os logs a partir de um ponto específico.
   - **Uso**: Usado para rastrear onde a leitura dos logs parou, útil para leituras incrementais.
```SQL
SELECT audit_log_read_bookmark();
```

2. **`audit_log_read(audit_log_read_bookmark())`**:
   - **Descrição**: Lê as entradas de log de auditoria começando a partir do marcador gerado pela função `audit_log_read_bookmark()`.
   - **Uso**: Lê apenas as novas entradas no log desde o último marcador.
```SQL
SELECT audit_log_read(audit_log_read_bookmark());
```

***Links de apoio***
https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-audit.html
https://dev.mysql.com/doc/refman/5.7/en/audit-log-reference.html#sysvar_audit_log_file
***Localize o arquivo audit_log_filter_linux_install.sql***
```bash
audit_log_filter_linux_install.sql
```

***Execute-o realizado upload para o mysql database***
```bash
mysql -u root -p mysql < /usr/share/mysql-9.0/audit_log_filter_linux_install.sql
```

***Validando a ativação da auditoria***
```SQL
SELECT PLUGIN_NAME, PLUGIN_STATUS
FROM INFORMATION_SCHEMA.PLUGINS
WHERE PLUGIN_NAME LIKE 'audit%';
```

***Para evitar que o plug-in seja removido em tempo de execução, adicione a opção --audit-log no my.cnf***
```ini
audit-log=FORCE_PLUS_PERMANENT
```

***Reinicie o servidor para aplicar a alteração de configuração:***
```bash
systemctl restart mysqld
```

***Alterando o audit_log_format para json, adicione no my.cnf***
```ini
[mysqld]
audit_log_format=JSON
```

***Reinicie o servidor para aplicar a alteração de configuração:***
```bash
systemctl restart mysqld
```

*** Valide a alteração***
```SQL
SHOW VARIABLES LIKE 'audit_log_format';
```

### Funções de Leitura de Log de Auditoria

1. **`audit_log_read_bookmark()`**:
   - **Descrição**: Cria um marcador no log de auditoria, permitindo que você leia os logs a partir de um ponto específico.
   - **Uso**: Usado para rastrear onde a leitura dos logs parou, útil para leituras incrementais.
```SQL
SELECT audit_log_read_bookmark();
```

2. **`audit_log_read(audit_log_read_bookmark())`**:
   - **Descrição**: Lê as entradas de log de auditoria começando a partir do marcador gerado pela função `audit_log_read_bookmark()`.
   - **Uso**: Lê apenas as novas entradas no log desde o último marcador.
```SQL
SELECT audit_log_read(audit_log_read_bookmark());
```

***Links de apoio***
https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-audit.html
https://dev.mysql.com/doc/refman/5.7/en/audit-log-reference.html#sysvar_audit_log_file
