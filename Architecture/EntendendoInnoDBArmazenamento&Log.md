# InnoDB: Como os Dados e Logs São Armazenados

O InnoDB é o mecanismo de armazenamento transacional padrão do MySQL, conhecido por garantir integridade referencial e oferecer suporte a transações ACID. Este resumo apresenta uma visão geral de como o InnoDB armazena seus dados e logs.

## Estrutura de Armazenamento de Dados

### 1. **Arquivos de Dados InnoDB**
   - **Tabelaspaces:** Os dados InnoDB são armazenados em tablespaces. Existem dois tipos principais:
     - **Sistema (system tablespace):** Armazena tabelas, índices, metadados do sistema e segmentos undo.
     - **Individuais (file-per-table):** Quando ativada a opção `innodb_file_per_table`, cada tabela tem seu próprio tablespace (`.ibd`), que contém dados e índices dessa tabela.
   - **Arquivos `.ibd`:** Armazenam dados da tabela e seus índices. Cada arquivo representa uma tabela quando a opção `innodb_file_per_table` está ativada.

### 2. **Páginas e Extents**
   - **Página (Page):** A unidade básica de armazenamento no InnoDB, geralmente de 16 KB.
   - **Extent:** Um conjunto de páginas. Cada extent contém 64 páginas (1 MB).

### 3. **Clustered Indexes**
   - As tabelas InnoDB são armazenadas em **índices clusterizados**. Isso significa que os dados da tabela são armazenados em ordem na árvore B+ baseada na chave primária.

### 4. **Armazenamento de Dados em Linhas**
   - Cada linha é armazenada no índice clusterizado. Se uma linha for muito grande, os dados podem ser armazenados fora da página principal com uma referência na página de índice.
   - **Tipos de Dados Grandes:** Dados `TEXT` e `BLOB` são armazenados em páginas fora da árvore B+ principal e referenciados a partir da entrada no índice.

## Armazenamento de Logs

### 1. **Redo Logs**
   - **Arquivos de Redo Log** (`ib_logfile0`, `ib_logfile1`, etc.) registram alterações em dados antes de serem confirmadas no tablespace, garantindo a recuperação em caso de falha.
   - **Buffer de Redo Log:** As alterações são inicialmente armazenadas no buffer de redo log na memória antes de serem gravadas no disco.

### 2. **Undo Logs**
   - O InnoDB mantém **Undo Logs** para permitir a reversão de transações. Os segmentos Undo estão armazenados no tablespace do sistema e contêm as versões anteriores das linhas modificadas pelas transações.
   - As **transações longas** podem gerar grandes volumes de dados de undo, e é importante otimizar sua limpeza.

### 3. **Arquivos de Log Binário**
   - Estes logs são opcionais e podem ser ativados para capturar todas as alterações executadas no servidor MySQL. Eles são usados para replicação e recuperação ponto-a-ponto.

## Fluxo de Armazenamento e Recuperação de Dados

### 1. **Gravação de Dados**
   - Quando uma transação é iniciada, o InnoDB grava as alterações no buffer de memória e nos redo logs.
   - Uma transação é confirmada após as alterações serem garantidas no redo log.
   - Dados são eventualmente gravados nos arquivos de dados (flush).

### 2. **Recuperação**
   - Em caso de falha, o InnoDB aplica as entradas dos redo logs não confirmadas no tablespace de dados para garantir a consistência.

## Consultas Relacionadas

Para verificar informações de tablespaces e logs no seu banco de dados, você pode usar algumas consultas SQL úteis:

### Consultar o Tamanho dos Tablespaces
```sql
SELECT table_schema, table_name, 
       ROUND((data_length + index_length) / 1024 / 1024, 2) AS total_mb
FROM information_schema.tables
WHERE engine = 'InnoDB';
```

### Verificar o Status dos Redo Logs
```sql
SHOW ENGINE INNODB STATUS\G
```

### Verificar Parâmetros Relacionados ao InnoDB
```sql
SHOW VARIABLES LIKE 'innodb%';
```

***Referência   
https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html
