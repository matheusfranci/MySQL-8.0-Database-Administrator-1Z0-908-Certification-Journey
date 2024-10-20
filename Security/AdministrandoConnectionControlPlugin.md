# Connection Control Plugin - MySQL

O **Connection Control Plugin** no MySQL é uma ferramenta essencial para limitar o número de tentativas de conexão mal sucedidas, ajudando a proteger o servidor contra ataques de força bruta e negação de serviço (DoS). Ele monitora e controla o número de tentativas de conexão de cada IP e aplica bloqueios temporários quando limites configurados são excedidos.

## Funcionalidades principais

- **Limite de tentativas falhadas**: O plugin permite definir um número máximo de tentativas de conexão falhadas antes que um IP seja bloqueado.
- **Tempo de bloqueio**: Após exceder o limite de falhas, o IP é bloqueado temporariamente, impedindo novas conexões por um período determinado.
  
## Variáveis importantes

- **`connection_control_failed_connections_threshold`**: Define o número de tentativas de conexão falhadas antes de bloquear o IP.
- **`connection_control_max_connection_delay`**: Configura o tempo máximo de bloqueio para novas conexões de um IP que foi bloqueado.

## Instalação

