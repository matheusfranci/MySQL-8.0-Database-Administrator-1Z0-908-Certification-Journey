# MySQL Data Dictionary

O Data Dictionary no MySQL é um repositório central onde todas as informações sobre a estrutura dos objetos do banco de dados, como tabelas, índices, e visualizações, são armazenadas. A partir do MySQL 8.0, o Data Dictionary substituiu os arquivos de metadados baseados no sistema de arquivos que eram usados nas versões anteriores, garantindo maior consistência e eficiência nas operações de metadados.

## 1. Estrutura do Data Dictionary

**O Data Dictionary armazena informações sobre:**

- Tabelas e colunas
- Visualizações
- Procedimentos armazenados e funções
- Índices
- Usuários e permissões

Esses dados são organizados em tabelas de sistema que podem ser acessadas pelo usuário. O MySQL utiliza o INFORMATION_SCHEMA e performance_schema para fornecer acesso a esses dados.

## 2. Principais Tabelas do Data Dictionary
**INFORMATION_SCHEMA.TABLES**
Contém informações sobre todas as tabelas disponíveis no banco de dados.

Colunas Importantes:
- TABLE_SCHEMA: Nome do banco de dados.
- TABLE_NAME: Nome da tabela.
- TABLE_TYPE: Tipo de tabela (BASE TABLE, VIEW).

**Query para listar todas as tabelas de um banco específico:**
```SQL
SELECT TABLE_NAME, TABLE_TYPE
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_SCHEMA = 'nome_do_banco';
```

## 3. INFORMATION_SCHEMA.COLUMNS
Armazena informações detalhadas sobre as colunas de cada tabela.

Colunas Importantes:
- TABLE_SCHEMA: Nome do banco de dados.
- TABLE_NAME: Nome da tabela.
- COLUMN_NAME: Nome da coluna.
- DATA_TYPE: Tipo de dado da coluna.
- IS_NULLABLE: Se a coluna permite valores NULL.

**Query para listar as colunas de uma tabela:**
```SQL
SELECT COLUMN_NAME, DATA_TYPE, IS_NULLABLE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_SCHEMA = 'nome_do_banco'
AND TABLE_NAME = 'nome_da_tabela';
```

## 4. INFORMATION_SCHEMA.STATISTICS
Fornece informações sobre índices de tabelas.

Colunas Importantes:
- TABLE_SCHEMA: Nome do banco de dados.
- TABLE_NAME: Nome da tabela.
- INDEX_NAME: Nome do índice.
- COLUMN_NAME: Nome da coluna indexada.

**Query para listar os índices de uma tabela:**
```SQL
SELECT INDEX_NAME, COLUMN_NAME
FROM INFORMATION_SCHEMA.STATISTICS
WHERE TABLE_SCHEMA = 'nome_do_banco'
AND TABLE_NAME = 'nome_da_tabela';
```

## 5. INFORMATION_SCHEMA.VIEWS
Contém informações sobre as visualizações do banco de dados.

Colunas Importantes:
- TABLE_SCHEMA: Nome do banco de dados.
- TABLE_NAME: Nome da visualização.
- VIEW_DEFINITION: Definição SQL da visualização.

**Query para listar todas as views de um banco:**
```SQL
SELECT TABLE_NAME, VIEW_DEFINITION
FROM INFORMATION_SCHEMA.VIEWS
WHERE TABLE_SCHEMA = 'nome_do_banco';
```

## 6. INFORMATION_SCHEMA.USER_PRIVILEGES
Armazena informações sobre as permissões dos usuários.

Colunas Importantes:
- GRANTEE: O usuário que possui as permissões.
- PRIVILEGE_TYPE: Tipo de permissão concedida.

**Query para listar todas as permissões de um usuário:**
```SQL
SELECT GRANTEE, PRIVILEGE_TYPE
FROM INFORMATION_SCHEMA.USER_PRIVILEGES
WHERE GRANTEE = "'nome_do_usuario'@'host'";
```
