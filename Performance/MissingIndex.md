```sql
SELECT
	t.TABLE_SCHEMA
	,t.TABLE_NAME
	,c.COLUMN_NAME
	,IFNULL(kcu.CONSTRAINT_NAME, 'Not indexed') AS Indexed
FROM information_schema.TABLES as t
INNER JOIN information_schema.COLUMNS as c
	ON c.TABLE_SCHEMA = t.TABLE_SCHEMA
		AND c.TABLE_NAME = t.TABLE_NAME
LEFT JOIN information_schema.KEY_COLUMN_USAGE as kcu
	ON kcu.TABLE_SCHEMA = t.TABLE_SCHEMA
		AND kcu.TABLE_NAME = t.TABLE_NAME
		AND kcu.COLUMN_NAME = c.COLUMN_NAME
		AND kcu.ORDINAL_POSITION = 1
WHERE kcu.TABLE_SCHEMA IS NULL
AND t.TABLE_SCHEMA IN ('prod');
```

---

Esta query SQL é usada para **identificar colunas não indexadas** dentro de um esquema de banco de dados específico, o 'prod'.

Aqui está um detalhamento do que cada parte da query faz:

* **`SELECT t.TABLE_SCHEMA, t.TABLE_NAME, c.COLUMN_NAME, IFNULL(kcu.CONSTRAINT_NAME, 'Not indexed') AS Indexed`**: Esta parte seleciona o esquema da tabela (`TABLE_SCHEMA`), o nome da tabela (`TABLE_NAME`), o nome da coluna (`COLUMN_NAME`) e, para a coluna `Indexed`, verifica se há um `CONSTRAINT_NAME` (indicando um índice). Se não houver, ele retorna 'Not indexed'.
* **`FROM information_schema.TABLES as t`**: Inicia a busca na tabela `information_schema.TABLES`, que contém metadados sobre todas as tabelas no banco de dados. Ela é apelidada de `t`.
* **`INNER JOIN information_schema.COLUMNS as c ON c.TABLE_SCHEMA = t.TABLE_SCHEMA AND c.TABLE_NAME = t.TABLE_NAME`**: Junta as tabelas `TABLES` e `COLUMNS` (apelidada de `c`) com base no esquema e nome da tabela. Isso garante que cada linha na saída corresponda a uma coluna em uma tabela existente.
* **`LEFT JOIN information_schema.KEY_COLUMN_USAGE as kcu ON kcu.TABLE_SCHEMA = t.TABLE_SCHEMA AND kcu.TABLE_NAME = t.TABLE_NAME AND kcu.COLUMN_NAME = c.COLUMN_NAME AND kcu.ORDINAL_POSITION = 1`**: Esta é a parte crucial para identificar índices. Ela tenta unir cada coluna com informações sobre o uso de chaves (`KEY_COLUMN_USAGE`), que contém dados sobre índices e chaves primárias/estrangeiras. O `LEFT JOIN` é importante porque ele trará todas as colunas, mesmo que não haja uma entrada correspondente em `KEY_COLUMN_USAGE` (ou seja, se a coluna não for indexada). O `ORDINAL_POSITION = 1` geralmente é usado para focar nas colunas que são a primeira parte de uma chave composta.
* **`WHERE kcu.TABLE_SCHEMA IS NULL AND t.TABLE_SCHEMA IN ('sei-prod')`**: Esta cláusula filtra os resultados:
    * **`kcu.TABLE_SCHEMA IS NULL`**: Esta condição é a chave para encontrar colunas não indexadas. Como usamos um `LEFT JOIN` com `KEY_COLUMN_USAGE`, se uma coluna não tiver um índice correspondente, o `kcu.TABLE_SCHEMA` será `NULL`.
    * **`t.TABLE_SCHEMA IN ('prod')`**: Restringe a busca apenas às tabelas dentro do esquema de banco de dados 'prod'.

Em resumo, essa query é uma ferramenta útil para **otimização de banco de dados**, ajudando a identificar áreas onde a adição de índices pode melhorar significativamente o desempenho das consultas.
