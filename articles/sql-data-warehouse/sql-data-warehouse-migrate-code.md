<properties
   pageTitle="Migrar seu código SQL para o SQL Data Warehouse | Microsoft Azure"
   description="Dicas para migrar seu código SQL para o SQL Data Warehouse do Azure para desenvolvimento de soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/09/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Migrar seu código SQL para o SQL Data Warehouse

Para garantir a compatibilidade de seu código com o SQL Data Warehouse, provavelmente será preciso fazer alterações ao seu código base. Alguns recursos do SQL Data Warehouse podem melhorar consideravelmente o desempenho, pois foram criados para funcionar diretamente de uma maneira distribuída. No entanto, para manter o desempenho e a escala, alguns recursos também não estão disponíveis.

## Alterações de código Transact-SQL

A lista a seguir resume os principais recursos que não têm suporte no SQL Data Warehouse do Azure. Os links levam a soluções alternativas para o recurso sem suporte:

- [Junções ANSI em atualizações][]
- [Junções ANSI em exclusões][]
- [instrução merge][]
- junções entre bancos de dados
- [cursores][]
- [SELECT..INTO][]
- [INSERT..EXEC][]
- cláusula output
- funções definidas pelo usuário embutidas
- funções com várias instruções
- [CTE (expressão de tabela comum) recursiva] (#Recursive-common-table-expressions-(CTE)
- [atualizações por meio de CTEs](#Updates-through-CTEs)
- procedimentos e funções CLR
- função $partition
- variáveis de tabela
- parâmetros de valor de tabela
- transações distribuídas
- confirmar/reverter trabalho
- salvar transação
- contextos de execução (EXECUTE AS)
- [cláusula group by com as opções rollup/cube/grouping sets][]
- [níveis de aninhamento superiores a 8][]
- [atualizando por meio de exibições][]
- [uso de select para atribuição de variável][]
- [nenhum tipo de dados MAX para cadeias de caracteres SQL dinâmicas][]

Felizmente, a maioria dessas limitações pode ser solucionada. Foram fornecidas explicações nos artigos de desenvolvimento relevantes mencionados acima.

### CTE (expressão de tabela comum) recursiva

Esse é um cenário complexo sem uma correção rápida. A CTE precisará ser dividida e manipulada em etapas. Normalmente, você pode usar um loop bastante complexo; preenchendo uma tabela temporária conforme itera sobre consultas recursivas provisórias. Depois que a tabela temporária for preenchida, você pode retornar os dados como um único conjunto de resultados. Uma abordagem semelhante foi usada para resolver `GROUP BY WITH CUBE` no artigo [Agrupar por cláusula com opções de conjuntos de rollup/cubo/agrupamento][].

### Atualizações por meio de CTEs

Se a CTE não for recursiva, você pode gravar novamente a consulta para usar subconsultas. Para CTEs recursivas, será necessário criar o conjunto de resultados pela primeira vez, conforme descrito acima. Então ingresse o conjunto de resultados final à tabela de destino e execute a atualização.

### Funções do sistema

Também há algumas funções do sistema que não têm suporte. Estas são algumas das principais e que normalmente são usadas em data warehouse:

- NEWID()
- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT\_BIG
- ERROR\_LINE()

Novamente, muitos desses problemas podem ser solucionados.

Por exemplo, o código abaixo é uma solução alternativa para a recuperação de informações de @@ROWCOUNT:

```
SELECT  SUM(row_count) AS row_count 
FROM    sys.dm_pdw_sql_requests 
WHERE   row_count <> -1 
AND     request_id IN 
                    (   SELECT TOP 1    request_id 
                        FROM            sys.dm_pdw_exec_requests 
                        WHERE           session_id = SESSION_ID() 
                        ORDER BY end_time DESC
                    )
;
``` 

## Próximas etapas
Para obter orientação sobre como desenvolver seu código, confira a [visão geral sobre desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Junções ANSI em atualizações]: sql-data-warehouse-develop-ctas.md
[Junções ANSI em exclusões]: sql-data-warehouse-develop-ctas.md
[instrução merge]: sql-data-warehouse-develop-ctas.md
[INSERT..EXEC]: sql-data-warehouse-develop-temporary-tables.md

[cursores]: sql-data-warehouse-develop-loops.md
[SELECT..INTO]: sql-data-warehouse-develop-ctas.md
[Agrupar por cláusula com opções de conjuntos de rollup/cubo/agrupamento]: sql-data-warehouse-develop-group-by-options.md
[cláusula group by com as opções rollup/cube/grouping sets]: sql-data-warehouse-develop-group-by-options.md
[níveis de aninhamento superiores a 8]: sql-data-warehouse-develop-transactions.md
[atualizando por meio de exibições]: sql-data-warehouse-develop-views.md
[uso de select para atribuição de variável]: sql-data-warehouse-develop-variable-assignment.md
[nenhum tipo de dados MAX para cadeias de caracteres SQL dinâmicas]: sql-data-warehouse-develop-dynamic-sql.md
[visão geral sobre desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=Sept15_HO2-->