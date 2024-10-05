***1- Criar um Novo Diretório de Dados***
```bash
sudo mkdir /var/lib/mysql2
sudo chown mysql:mysql /var/lib/mysql2
```

***2- Copiar e Editar o Arquivo de Configuração***
```bash
sudo cp /etc/my.cnf /etc/my2.cnf
```

***3- Edite o arquivo /etc/my2.cnf para modificar as seguintes opções:***
```ini
[mysqld]
port=3307
datadir=/var/lib/mysql2
socket=/var/lib/mysql2/mysql.sock
pid-file=/var/run/mysqld/mysqld2.pid
log-error=/var/log/mysqld2.log
```

***4- Inicializar o novo diretório de dados***
```bash
sudo mysqld --initialize --datadir=/var/lib/mysql2 --user=mysql
```

***5- Criar um Novo Arquivo de Serviço para o Systemd***
```bash
sudo cp /usr/lib/systemd/system/mysqld.service /usr/lib/systemd/system/mysqld2.service
```

***6- Edite o arquivo /usr/lib/systemd/system/mysqld2.service***
```ini
ExecStart=/usr/sbin/mysqld --defaults-file=/etc/my2.cnf
PIDFile=/var/run/mysqld/mysqld2.pid
```

***7- Recarregar os Serviços do Systemd***
```bash
sudo systemctl daemon-reload
```

***8- Criar e ajustar permissões para o arquivo de log***
```bash
sudo touch /var/log/mysqld2.log
sudo chown mysql:mysql /var/log/mysqld2.log
sudo chmod 660 /var/log/mysqld2.log
```

***9- Iniciar a Segunda Instância do MySQL***
```bash
sudo systemctl start mysqld2
sudo systemctl enable mysqld2
```

***10- Parar o cluster para mudar a senha de root***
```bash
sudo systemctl stop mysqld2
```

***11- Inicie o MySQL manualmente ignorando a tabela de permissões:***
```bash
sudo mysqld --defaults-file=/etc/my2.cnf --skip-grant-tables --user=mysql &
```

***12- Conectando ao mysql sem senha***
```bash
mysql -u root --socket=/var/lib/mysql2/mysql.sock
```

***13- Alterando a senha***
```SQL
ALTER USER 'root'@'localhost' IDENTIFIED BY 'nova_senha';
FLUSH PRIVILEGES;
```

***14- Reiniciando o cluster (necessário reiniciar a outra instância)***
```bash
sudo killall mysqld
sudo systemctl start mysqld2
```

***15- Acessar a segunda instância***
```bash
mysql -u root -p --port=3307 --socket=/var/lib/mysql2/mysql.sock
```

***16- Validando o datadir e a porta***
```SQL
SHOW VARIABLES LIKE 'datadir';
SHOW VARIABLES LIKE 'port';
```
```bash
systemctl status mysqld mysqld2
```
