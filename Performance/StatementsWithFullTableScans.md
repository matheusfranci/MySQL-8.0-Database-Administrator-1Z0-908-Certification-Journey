Claro! Aqui está a descrição da sua query com a própria query incluída:

---

```sql
SELECT *
FROM sys.statements_with_full_table_scans
WHERE db = 'prod'
ORDER BY rows_examined DESC;
```
```sql
SET @sys.statement_truncate_len = 1000;
select distinct query from sys.statements_with_full_table_scans where db = 'prod'
```

---

Essa query SQL serve para **identificar e listar consultas que estão realizando varreduras completas em tabelas** (conhecidas como *full table scans*) no seu banco de dados 'prod'.

### Como a Query Funciona

* **`SELECT *`**: Isso significa que você quer ver **todas as informações disponíveis** sobre as consultas que estão sendo analisadas. As colunas retornadas geralmente incluem detalhes como a consulta SQL em si, o usuário que a executou, o banco de dados envolvido, e métricas de desempenho.
* **`FROM sys.statements_with_full_table_scans`**: A query busca esses dados de uma **visão de sistema** chamada `sys.statements_with_full_table_scans`. Essa visão é fornecida pelo sistema de gerenciamento de banco de dados (como o MySQL 8.0, por exemplo) para expor, especificamente, as consultas que foram executadas e que exigiram a leitura completa de uma tabela. Fazer um *full table scan* é, muitas vezes, um sinal de que a consulta não está usando índices de forma eficiente ou que não há um índice adequado.
* **`WHERE db = 'prod'`**: Esta cláusula **filtra os resultados**, mostrando apenas as consultas que foram executadas no banco de dados chamado 'prod'. Isso é super útil para focar sua análise em um ambiente de produção específico, ignorando outros bancos de dados de desenvolvimento ou teste.
* **`ORDER BY rows_examined DESC`**: Por fim, os resultados são **ordenados de forma decrescente** com base na coluna `rows_examined`. Essa coluna indica a quantidade de linhas que foram "examinadas" ou lidas pelo banco de dados para executar a consulta. Ao ordenar assim, você consegue ver primeiro as consultas que foram mais custosas em termos de leitura de dados, ajudando a priorizar quais otimizações são mais importantes.

Em resumo, essa query é uma ferramenta fundamental para **identificar e otimizar gargalos de desempenho** no seu banco de dados 'prod'. Ela te ajuda a descobrir quais consultas estão demandando mais recursos de I/O, o que pode indicar a necessidade de criar novos índices ou ajustar consultas existentes para torná-las mais eficientes.
