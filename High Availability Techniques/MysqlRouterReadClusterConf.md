***Acesse o arquivo de configuração do mysqlrouter, por default ele fica nesse path /etc/mysqlrouter/mysqlrouter.conf***
```ini
[routing:primary_rw]
bind_address = 0.0.0.0
bind_port = 6446
mode = read-write
max_connections = 128
destinations = 192.168.1.254:3306
routing_strategy = first-available

[routing:secondary_ro]
bind_address = 0.0.0.0
bind_port = 6447
mode = read-only
max_connections = 128
destinations = 192.168.1.244:3306
routing_strategy = first-available
```

***Reinicie o mysqlrouter***
```bash
sudo systemctl restart mysqlrouter
```

***Verifique os status do serviço***
```
sudo systemctl status mysqlrouter
```
