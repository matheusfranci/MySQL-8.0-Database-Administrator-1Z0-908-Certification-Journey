# Connection Control Plugin - MySQL

O **Connection Control Plugin** no MySQL é uma ferramenta essencial para limitar o número de tentativas de conexão mal sucedidas, ajudando a proteger o servidor contra ataques de força bruta e negação de serviço (DoS). Ele monitora e controla o número de tentativas de conexão de cada IP e aplica bloqueios temporários quando limites configurados são excedidos.

## Funcionalidades principais

- **Limite de tentativas falhadas**: O plugin permite definir um número máximo de tentativas de conexão falhadas antes que um IP seja bloqueado.
- **Tempo de bloqueio**: Após exceder o limite de falhas, o IP é bloqueado temporariamente, impedindo novas conexões por um período determinado.
  
## Variáveis importantes

- **`connection_control_failed_connections_threshold`**: Define o número de tentativas de conexão falhadas antes de bloquear o IP.
- **`connection_control_max_connection_delay`**: Configura o tempo máximo de bloqueio para novas conexões de um IP que foi bloqueado.

## Instalação

***Instalando o plugin connection control***
```ini
[mysqld]
plugin-load-add=connection_control.so
```

***Alternativamente temos esse comando que pode ser executado em tempo de execução***
```SQL
INSTALL PLUGIN CONNECTION_CONTROL
  SONAME 'connection_control.so';
INSTALL PLUGIN CONNECTION_CONTROL_FAILED_LOGIN_ATTEMPTS
  SONAME 'connection_control.so';
```

***Caso queira assegurar de que a instância não será iniciada sem esses plugins ou de que os mesmos não serão removidos para adicionar mais dois parâmetros no my.cnf***
```ini
[mysqld]
plugin-load-add=connection_control.so
connection-control=FORCE_PLUS_PERMANENT
connection-control-failed-login-attempts=FORCE_PLUS_PERMANENT
```

***Validando a instalação***
```SQL
SELECT PLUGIN_NAME, PLUGIN_STATUS
FROM INFORMATION_SCHEMA.PLUGINS
WHERE PLUGIN_NAME LIKE 'connection%';
```

***Adicionando as variáveis de bloqueio de IP***
```ini
[mysqld]
connection_control_failed_connections_threshold=4 #Define o número de tentativas de conexão falhadas antes de bloquear o IP.
connection_control_min_connection_delay=2000 #Configura o tempo máximo de bloqueio para novas conexões de um IP que foi bloqueado.
```

***Para definir e persistir as variáveis ​​em tempo de execução, use estas instruções:***
```SQL
SET PERSIST connection_control_failed_connections_threshold = 4;
SET PERSIST connection_control_min_connection_delay = 2000;
```

## Links de apoio
https://dev.mysql.com/doc/refman/8.4/en/connection-control.html
https://dev.mysql.com/doc/refman/8.4/en/connection-control-installation.html
https://dev.mysql.com/doc/refman/8.4/en/connection-control-variables.html
