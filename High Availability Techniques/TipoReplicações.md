## Tipos de replicação no MySQL

No MySQL, existem três tipos principais de replicação:

### 1. Replicação baseada em instruções (Statement-Based Replication - SBR)
Nessa modalidade, as instruções SQL executadas no servidor primário (source) são enviadas para os servidores replicados (replicas), que as executam. Esse método pode ser menos eficiente em situações onde uma mesma query gera resultados diferentes em diferentes ambientes.

### 2. Replicação baseada em row (Row-Based Replication - RBR)
Em vez de enviar a instrução SQL, o MySQL replica as linhas alteradas. Isso garante que as alterações sejam idênticas nos servidores primário e replicados, independentemente do ambiente ou condições de execução. No entanto, pode resultar em maior volume de dados sendo transferido, especialmente em grandes atualizações.

### 3. Replicação híbrida (Mixed-Based Replication - MBR)
Este modo combina os dois anteriores, utilizando a replicação baseada em instruções por padrão e mudando para replicação baseada em linhas quando necessário. O MySQL decide automaticamente qual método usar com base na query executada.

Esses métodos são usados para garantir que os dados em um servidor secundário sejam idênticos aos dados no servidor primário.

## Configuração dos Tipos de Replicação no MySQL

### 1. Replicação baseada em instruções (Statement-Based Replication - SBR)

Para configurar a replicação baseada em instruções, adicione ou modifique a seguinte linha no arquivo de configuração do MySQL (`my.cnf` ou `my.ini`):

```bash
binlog_format = STATEMENT
```
### 2. Replicação baseada em row (Row-Based Replication - RBR)
Para configurar a replicação baseada em linhas, edite o arquivo de configuração:

```bash
binlog_format = ROW
```

### 3. Replicação híbrida (Mixed-Based Replication - MBR)
Para configurar a replicação baseada em linhas, edite o arquivo de configuração:

```bash
binlog_format = MIXED
```

Em todos os casos é necessário reiniciar a instância.
```bash
systemctl restart mysqld
```
