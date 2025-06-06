```sql
SELECT
    variable_name,
    variable_value,
    CASE variable_name
        WHEN 'innodb_buffer_pool_size' THEN
            'Tamanho do cache de dados do InnoDB. Deve ser ~70-80% da RAM em produção.'
        WHEN 'query_cache_size' THEN
            'Obsoleto em MySQL 5.7+ e removido no 8.0. Deve ser 0. Se estiver ativo, pode causar lentidão por locks.'
        WHEN 'max_connections' THEN
            'Número máximo de conexões simultâneas permitidas. Muito alto consome RAM. Muito baixo gera erros de conexão.'
        WHEN 'tmp_table_size' THEN
            'Tamanho máximo das tabelas temporárias em memória. Se exceder, vai para o disco (lento).'
        WHEN 'max_heap_table_size' THEN
            'Tamanho máximo de tabelas HEAP (em memória). Relevante junto com tmp_table_size.'
        WHEN 'innodb_log_file_size' THEN
            'Tamanho do log redo do InnoDB. Afeta desempenho de escrita e recuperação após crash.'
        WHEN 'slow_query_log' THEN
            'Ativa ou não o log de queries lentas. Deve estar ON em produção para análise de performance.'
        WHEN 'slow_query_log_file' THEN
            'Arquivo onde as queries lentas são armazenadas.'
        WHEN 'long_query_time' THEN
            'Tempo (em segundos) que define uma "query lenta". Normalmente definido como 1 ou menos.'
        WHEN 'innodb_flush_log_at_trx_commit' THEN
            'Define a segurança do InnoDB nos commits. Valor 1 = mais seguro, valor 2 ou 0 = melhor performance.'
        WHEN 'innodb_stats_auto_recalc' THEN
            'Recalcula estatísticas automaticamente após muitas mudanças. Deve estar ON para bom plano de execução.'
        ELSE 'N/A'
    END AS descricao,
    CASE variable_name
        WHEN 'innodb_buffer_pool_size' THEN
            CASE
                WHEN variable_value < 1073741824 THEN 'Aumente para 70-80% da RAM disponível (produção)'
                ELSE 'OK'
            END
        WHEN 'query_cache_size' THEN
            CASE
                WHEN variable_value = 0 THEN 'OK'
                ELSE 'Desative o query_cache (obsoleto e prejudicial)'
            END
        WHEN 'max_connections' THEN
            CASE
                WHEN variable_value > 1000 THEN 'Avalie se precisa de tantas conexões simultâneas'
                WHEN variable_value < 100 THEN 'Pode ser muito baixo em sistemas com muitos usuários'
                ELSE 'OK'
            END
        WHEN 'tmp_table_size' THEN
            CASE
                WHEN variable_value < 67108864 THEN 'Aumente para pelo menos 64MB (67108864)'
                ELSE 'OK'
            END
        WHEN 'max_heap_table_size' THEN
            CASE
                WHEN variable_value < 67108864 THEN 'Aumente para pelo menos 64MB (67108864)'
                ELSE 'OK'
            END
        WHEN 'innodb_log_file_size' THEN
            CASE
                WHEN variable_value < 134217728 THEN 'Aumente para pelo menos 128MB (134217728)'
                ELSE 'OK'
            END
        WHEN 'slow_query_log' THEN
            CASE
                WHEN variable_value = 'ON' THEN 'OK'
                ELSE 'Ative para monitorar queries lentas'
            END
        WHEN 'long_query_time' THEN
            CASE
                WHEN variable_value > 2 THEN 'Considere reduzir para 1 ou menos para capturar mais queries'
                ELSE 'OK'
            END
        WHEN 'innodb_flush_log_at_trx_commit' THEN
            CASE
                WHEN variable_value = 1 THEN 'OK (seguro)'
                WHEN variable_value = 2 THEN 'Melhor performance, mas risco de perda em crash'
                ELSE 'Use 1 em produção ou 2 se aceitar risco controlado'
            END
        WHEN 'innodb_stats_auto_recalc' THEN
            CASE
                WHEN variable_value = 'ON' THEN 'OK'
                ELSE 'Ative para manter estatísticas atualizadas'
            END
        ELSE 'Sem ação definida'
    END AS acao_sugerida
FROM
    performance_schema.global_variables
WHERE
    variable_name IN (
        'innodb_buffer_pool_size',
        'query_cache_size',
        'max_connections',
        'tmp_table_size',
        'max_heap_table_size',
        'innodb_log_file_size',
        'slow_query_log',
        'slow_query_log_file',
        'long_query_time',
        'innodb_flush_log_at_trx_commit',
        'innodb_stats_auto_recalc'
    );
```

---

### O Que Essa Query Faz?

Esta query SQL é projetada para **obter e analisar as configurações globais chave de um servidor MySQL ou MariaDB**, oferecendo uma **descrição** para cada uma e uma **sugestão de ação** com base em seus valores atuais. É uma ferramenta muito útil para administradores de banco de dados e desenvolvedores que buscam otimizar o desempenho do servidor.

### Detalhamento da Query

1.  **`SELECT variable_name, variable_value`**:
    * Seleciona o **nome da variável** de configuração (ex: `innodb_buffer_pool_size`) e seu **valor atual**.

2.  **`CASE variable_name WHEN '...' THEN '...' END AS descricao`**:
    * Cria uma coluna chamada `descricao` que fornece uma **explicação concisa sobre o que cada variável de configuração faz** e sua importância para o desempenho do banco de dados. Por exemplo, para `innodb_buffer_pool_size`, ela explica que é o "Tamanho do cache de dados do InnoDB" e que deve ser "~70-80% da RAM em produção".

3.  **`CASE variable_name WHEN '...' THEN CASE ... END AS acao_sugerida`**:
    * Esta é a parte mais dinâmica. Ela cria uma coluna chamada `acao_sugerida` que, para cada variável, oferece uma **recomendação com base no seu valor atual**.
    * Por exemplo:
        * Se `innodb_buffer_pool_size` for menor que 1GB (1073741824 bytes), a sugestão é "Aumente para 70-80% da RAM disponível (produção)".
        * Se `query_cache_size` for diferente de 0, a sugestão é "Desative o query\_cache (obsoleto e prejudicial)".
        * Para `slow_query_log`, se estiver "OFF", sugere "Ative para monitorar queries lentas".
        * Para `innodb_flush_log_at_trx_commit`, ela explica os trade-offs entre segurança (valor 1) e performance (valor 0 ou 2).

4.  **`FROM performance_schema.global_variables`**:
    * Os dados são buscados da visão `performance_schema.global_variables`. Esta visão contém todas as variáveis de sistema globais que o servidor MySQL/MariaDB está usando atualmente.

5.  **`WHERE variable_name IN (...)`**:
    * Esta cláusula filtra as variáveis, mostrando apenas um conjunto específico que é considerado crítico para a **saúde e o desempenho do banco de dados**. Isso evita que a saída seja poluída com centenas de variáveis menos relevantes para uma análise rápida de otimização.

Em suma, essa query é uma **ferramenta de auditoria de configurações** que ajuda a entender o estado atual do seu servidor de banco de dados e a identificar rapidamente áreas onde ajustes nas configurações podem levar a melhorias significativas de performance e estabilidade.
