# MySQL Data Storage Overview

## 1. Storage Engines
MySQL usa *storage engines* para armazenar e gerenciar dados. Cada *storage engine* tem suas características específicas. Os dois principais são:

### InnoDB
- **Transações**: Suporta transações ACID (Atomicidade, Consistência, Isolamento, Durabilidade).
- **Chaves Estrangeiras**: Suporta *foreign keys* com *cascading* de operações.
- **Armazenamento baseado em páginas**: Usa páginas de 16KB para armazenar registros.
- **Row-level Locking**: Faz *locking* a nível de linha, permitindo alta concorrência.

### MyISAM
- **Alta Velocidade de Leitura**: É mais rápido para leituras simples, mas não suporta transações.
- **Table-level Locking**: Usa *locking* a nível de tabela, o que pode gerar concorrência limitada.
- **Compactação**: Suporta compressão de tabelas para reduzir espaço.

> Nota: InnoDB é o *storage engine* padrão a partir do MySQL 5.5, por seu suporte a transações e integridade referencial.

## 2. Tablespaces
InnoDB usa *tablespaces* para gerenciar onde os dados são armazenados. Existem dois tipos principais de *tablespaces*:

- **Sistema (System Tablespace)**: Por padrão, armazena todas as tabelas InnoDB em um único arquivo de disco (`ibdata1`), além dos logs e metadados.
- **Independente (File-per-table Tablespace)**: A partir do MySQL 5.6, as tabelas podem ser armazenadas em seus próprios arquivos (`.ibd`), facilitando o gerenciamento de espaço e backups.

## 3. Data Types
MySQL suporta diferentes tipos de dados. Alguns dos principais são:

- **Numéricos**:
  - `INT`, `BIGINT`, `FLOAT`, `DECIMAL`, etc.
- **Texto**:
  - `VARCHAR`, `TEXT`, `CHAR`, etc.
- **Datas e Horários**:
  - `DATE`, `TIME`, `DATETIME`, `TIMESTAMP`.
- **BLOBs**:
  - `BLOB`, `TINYBLOB`, `MEDIUMBLOB`, `LONGBLOB` para armazenar dados binários grandes.

## 4. Indexes
MySQL usa índices para acelerar consultas. Os índices são armazenados com base na estrutura da tabela:

- **B-tree Indexes**: Usado na maioria dos casos para InnoDB e MyISAM.
- **Full-text Indexes**: Para pesquisas em texto no MyISAM e InnoDB a partir do MySQL 5.6.
- **Spatial Indexes**: Para dados geométricos, suportados pelo InnoDB desde o MySQL 5.7.

### Exemplo de criação de índice:
```sql
CREATE INDEX idx_column_name ON table_name(column_name);
```

## 5. Data Storage Structure

### InnoDB
InnoDB usa uma estrutura chamada **clustered index** para armazenar dados. A tabela InnoDB é organizada fisicamente de acordo com a **primary key**. Isso significa que:
- **Clustered Index**: O índice primário contém os dados da tabela e define a ordem física dos registros.
- **Secondary Indexes**: Referenciam as chaves primárias em vez de armazenar os dados diretamente.

Se uma tabela InnoDB não tiver uma chave primária, o InnoDB criará uma internamente para organizar os registros.

### MyISAM
No MyISAM, os dados são armazenados de maneira mais simples, separados em dois arquivos:
- **`.MYD` (My Data)**: Contém os dados da tabela.
- **`.MYI` (My Index)**: Contém os índices associados à tabela.

MyISAM organiza os dados na ordem de inserção, diferente do InnoDB, que organiza com base na chave primária.

### Exemplo de Criação de Tabela (InnoDB):
```sql
CREATE TABLE employees (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  role VARCHAR(50)
) ENGINE=InnoDB;
```

## 6. Buffer Pool

O **Buffer Pool** é uma área de memória usada pelo InnoDB para armazenar dados e índices frequentemente acessados. Ele atua como um cache, mantendo as páginas de dados na memória para evitar leituras frequentes de disco, o que melhora o desempenho.

### Principais Características:
- **Páginas de 16KB**: O Buffer Pool armazena páginas de 16KB, que podem conter registros ou índices.
- **LRU (Least Recently Used)**: O Buffer Pool utiliza o algoritmo LRU para determinar quais páginas permanecem na memória e quais devem ser substituídas quando a memória está cheia.
- **Prefetching**: InnoDB pode pré-carregar páginas adicionais antecipadamente, dependendo dos padrões de acesso.

### Monitorando o Buffer Pool:
Você pode visualizar informações sobre o uso do Buffer Pool usando o seguinte comando:

```sql
SHOW ENGINE INNODB STATUS;
```

## 7. Logs

O InnoDB utiliza logs para garantir a recuperação e a consistência dos dados em caso de falhas. Existem dois tipos principais de logs:

### Redo Logs:
- **Finalidade**: Garantir durabilidade e recuperação após falhas. Eles armazenam as modificações realizadas nos dados antes que sejam gravadas permanentemente no disco.
- **Funcionamento**: O InnoDB grava primeiro as alterações no Redo Log e, em intervalos regulares (processo conhecido como *checkpointing*), aplica essas alterações ao *tablespace*.
- **Recuperação**: Em caso de falha, o Redo Log permite que o InnoDB refaça as operações pendentes.

### Undo Logs:
- **Finalidade**: Armazenar o estado anterior de dados modificados por transações. Permitem que o InnoDB reverta transações que ainda não foram confirmadas ou que precisem ser desfeitas.
- **Transações**: O Undo Log é essencial para o isolamento e a atomicidade das transações, garantindo que as operações possam ser desfeitas se necessário.
  
### Exemplo para verificar o status dos logs:
Você pode consultar o status dos logs usando o seguinte comando:

```sql
SHOW ENGINE INNODB STATUS;
```

## 8. Data Compression

O InnoDB oferece suporte à compressão de dados para economizar espaço em disco, especialmente em bancos de dados grandes. A compressão pode ajudar a reduzir significativamente o tamanho dos arquivos de dados, sem comprometer a integridade ou a performance do banco de dados, dependendo do cenário.

### Como Funciona a Compressão:
- A compressão reduz o tamanho físico dos dados no disco, armazenando páginas de dados comprimidas.
- O formato de linha `ROW_FORMAT=COMPRESSED` permite que o InnoDB compacte os dados das tabelas.
- As páginas de dados comprimidas podem ter tamanhos de 1KB, 2KB, 4KB ou 8KB, dependendo da quantidade de compressão aplicável aos dados.

### Benefícios da Compressão:
- **Economia de Espaço**: Redução no uso de espaço em disco, o que é útil para bancos de dados com muitos dados repetitivos ou colunas longas.
- **Melhoria no Desempenho de I/O**: Em alguns casos, a compressão pode melhorar o desempenho de leitura e gravação, pois menos dados precisam ser transferidos entre o disco e a memória.
- **Menor Latência em Leituras**: Tabelas comprimidas podem ser carregadas mais rapidamente na memória, especialmente se as páginas comprimidas forem menores que as páginas padrão de 16KB do InnoDB.

### Exemplo de Criação de Tabela com Compressão:
```sql
CREATE TABLE compressed_table (
  id INT PRIMARY KEY,
  data VARCHAR(255)
) ENGINE=InnoDB ROW_FORMAT=COMPRESSED;
```

##Considerações:
- A compressão pode melhorar o desempenho em cenários de leitura pesada, mas pode introduzir sobrecarga em sistemas com muitas gravações, já que os dados precisam ser descomprimidos e recomprimidos em cada operação.
- É importante testar o impacto da compressão em um ambiente de desenvolvimento antes de aplicá-la em produção, especialmente em sistemas com muitas transações de escrita.
