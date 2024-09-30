# Instalação do MySQL Enterprise Edition no Oracle Linux

---

***1. Download dos Binários***
- Acesse o link: [MySQL Enterprise Edition Downloads](https://www.oracle.com/mysql/technologies/mysql-enterprise-edition-downloads.html)

***2. Copiar o Arquivo para a VM***
- Será necessário copiar o arquivo baixado para a VM. Isso pode ser feito via `scp` ou através de uma interface como o MobaXterm ou WinSCP.

```bash
# Exemplo de comando SCP
C:\Users\usuario>scp C:\Users\usuario\Downloads\mysql-enterprise-9.0.1_el7_x86_64_bundle.tar usuario@***.***.*.***:/home/usuario/
# Senha do usuário
usuario@***.***.*.***'s password:
mysql-enterprise-9.0.1_el7_x86_64_bundle.tar                                          100%  954MB  68.2MB/s   00:13
```

***3. Realizar Unpack do tar importado para a VM:***
```bash
cd /home/usuario/
tar xvf mysql-enterprise-9.0.1_el7_x86_64_bundle.tar
```

***4. Adicione a chave GPG do MySQL ao seu sistema para verificar os pacotes no momento da instalação:***
```bash
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
```

***5. Instale utilitários que ajudam na configuração do repositório Yum:***
```bash
sudo yum install yum-utils
```

***6. Adicione os pacotes desenvolvidos baixados como um repositório local do Yum, aqui você deve apontar o diretório de onde você fez o unpack do tar de instalação do mysql***
```bash
sudo yum-config-manager --add file:///caminho/para/os/rpms
```

***7. Desabilite o módulo MySQL que vem com a distribuição do seu sistema operacional:***
```bash
sudo yum module disable mysql
```

***8. Instale o MySQL Server ou qualquer um dos produtos incluídos no pacote, usando um dos seguintes comandos:***
```bash
sudo yum install mysql-commercial-server
sudo yum install mysql-commercial-backup
sudo yum install mysql-connector-c++-commercial
sudo yum install mysql-connector-c++-commercial-jdbc
sudo yum install mysql-connector-j-commercial
sudo yum install mysql-connector-odbc-commercial
sudo yum install mysql-connector-odbc-commercial-setup
sudo yum install mysql-connector-python3-commercial
sudo yum install mysql-router-commercial
sudo yum install mysql-shell-commercial
```

***9. Inicie o serviço do Mysql***
```bash
systemctl start mysqld
```

***10. Uma conta superusuário 'root'@'localhost' é criada. Uma senha para o superusuário é definida e armazenada no arquivo de log de erros. Para revelá-la, use o seguinte comando para sistemas RHEL, Oracle Linux, CentOS e Fedora:***
```bash
sudo grep 'temporary password' /var/log/mysqld.log
```

***11. Acesse com a senha revelada***
```bash
mysql -uroot -p
```

***12. Altere a senha de root***
```SQL
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
```

Links de suporte:
https://docs.oracle.com/en/database/mysql/otr-installation/index.html#GUID-05192EA1-E1DE-4893-8F81-650CBE62F9A2
https://dev.mysql.com/doc/mysql-installation-excerpt/8.0/en/linux-installation-rpm.html
