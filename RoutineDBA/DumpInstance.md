Criar o diretório de backup
```bash
mkdir /Backup
```

Criar o diretório de log
```bash
mkdir /Backup/LOG
```

Criar o diretório de script
```bash
mkdir /Scripts
```

Criar o script
```bash
vi DumpAllInstance.sh
```

Insira o scrit abaixo e salve
```shell
#!/bin/bash
#configurações do Banco de Dados
DB_HOST="localhost"          # Endereço do servidor MySQL
DB_USER="root"               # Usuário do MySQL
DB_PASS="230319Mf!"          # Senha do usuário MySQL
DB_PORT="3306"               # Porta do MySQL

# Configurações de Backup
BACKUP_DIR="/backup"         # Diretório onde os backups serão salvos
DATE=$(date +%d-%m-%Y)       # Data atual formatada (sem hora, minuto e segundo)
LOG_FILE="$BACKUP_DIR/LOG/backup-$DATE.log"  # Arquivo de log com a data

# Garantir que o diretório de backup existe
mkdir -p "$BACKUP_DIR/LOG"   # Criar o diretório de logs se não existir

# Função para registrar logs
log_message() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

# Início do processo de backup
log_message "Iniciando backup da instância MySQL usando MySQL Shell."

# Definir o diretório de backup para a instância inteira
BACKUP_FILE="$BACKUP_DIR/mysql-instance-$DATE"  # Arquivo de backup para toda a instância

# Executar o backup com MySQL Shell no modo JavaScript, usando threads para paralelismo
log_message "Iniciando backup da instância inteira."

mysqlsh --user=$DB_USER --password=$DB_PASS --host=$DB_HOST --port=$DB_PORT \
    --js -e "util.dumpInstance('$BACKUP_FILE', {threads: 4, ocimds: true, compatibility: ['ignore_wildcard_grants'], users: false})" >> "$LOG_FILE" 2>&1

if [[ $? -eq 0 ]]; then
    log_message "Backup da instância MySQL concluído com sucesso: $BACKUP_FILE"
else
    log_message "Erro durante o backup da instância MySQL. Verifique o log para mais detalhes."
fi

# Finalizar
log_message "Backup da instância MySQL finalizado."
```

Permita a execução do script criado
```bash
chmod +x /scripts/DumpAllInstance.sh
```

