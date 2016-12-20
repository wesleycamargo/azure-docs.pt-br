---
title: "Migrar seu código SQL para o SQL Data Warehouse | Microsoft Docs"
description: "Dicas para migrar seu código SQL para o SQL Data Warehouse do Azure para desenvolvimento de soluções."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 19c252a3-0e41-4eec-9d3e-09a68c7e7add
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0ff5ad648d429da433170301205eafb850be5d81


---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Migrar seu código SQL para o SQL Data Warehouse
Durante a migração de seu código de outro banco de dados para o SQL Data Warehouse, provavelmente você precisará fazer alterações à sua base de código. Alguns recursos do SQL Data Warehouse podem melhorar consideravelmente o desempenho, pois foram criados para funcionar diretamente de uma maneira distribuída. No entanto, para manter o desempenho e a escala, alguns recursos também não estão disponíveis.

## <a name="common-t-sql-limitations"></a>Limitações comuns do T-SQL
A lista a seguir resume os recursos mais comuns que não têm suporte no SQL Data Warehouse do Azure. Os links levam a soluções alternativas para o recurso sem suporte:

* [Junções ANSI em atualizações][Junções ANSI em atualizações]
* [Junções ANSI em exclusões][Junções ANSI em exclusões]
* [instrução merge][instrução merge]
* junções entre bancos de dados
* [cursores][cursores]
* [SELECT..INTO][SELECT..INTO]
* [INSERT..EXEC][INSERT..EXEC]
* cláusula output
* funções definidas pelo usuário embutidas
* funções com várias instruções
* [expressões de tabela comuns](#Common-table-expressions)
* [CTE (expressão de tabela comum) recursiva] (#Recursive-common-table-expressions-(CTE)
* procedimentos e funções CLR
* função $partition
* variáveis de tabela
* parâmetros de valor de tabela
* transações distribuídas
* confirmar/reverter trabalho
* salvar transação
* contextos de execução (EXECUTE AS)
* [cláusula group by com as opções rollup/cube/grouping sets][cláusula group by com as opções rollup/cube/grouping sets]
* [níveis de aninhamento superiores a 8][níveis de aninhamento superiores a 8]
* [atualizando por meio de exibições][atualizando por meio de exibições]
* [uso de select para atribuição de variável][uso de select para atribuição de variável]
* [nenhum tipo de dados MAX para cadeias de caracteres SQL dinâmicas][nenhum tipo de dados MAX para cadeias de caracteres SQL dinâmicas]

Felizmente, a maioria dessas limitações pode ser solucionada. Foram fornecidas explicações nos artigos de desenvolvimento relevantes mencionados acima.

## <a name="supported-cte-features"></a>Recursos do CTE com suporte
Tabela CTEs (expressões comuns) têm suporte parcial no SQL Data Warehouse.  Atualmente, há suporte aos seguintes recursos de CTE:

* Uma CTE pode ser especificada em uma instrução SELECT.
* Uma CTE pode ser especificada em uma instrução CREATE VIEW.
* Uma CTE pode ser especificada em uma instrução CTAS (CREATE TABLE AS SELECT).
* Uma CTE pode ser especificada em uma instrução CRTAS (CREATE REMOTE TABLE AS SELECT).
* Uma CTE pode ser especificada em uma instrução CETAS (CREATE EXTERNAL TABLE AS SELECT).
* Uma tabela remota pode ser referenciada de uma CTE.
* Uma tabela externa pode ser referenciada de uma CTE.
* Várias definições de consulta CTE podem ser definidas em uma CTE.

## <a name="cte-limitations"></a>Limitações da CTE
As expressões de tabela comum têm algumas limitações no SQL Data Warehouse, incluindo:

* Uma CTE deve ser seguida por uma única instrução SELECT. As instruções INSERT, UPDATE, DELETE e MERGE não têm suporte.
* Não há suporte para a expressão de tabela comum que inclui referências a ela mesma (expressão de tabela comum recursiva) (veja a seção abaixo).
* Não é permitido especificar mais de uma cláusula WITH em uma CTE. Por exemplo, se CTE_query_definition contiver uma subconsulta, essa subconsulta não poderá conter uma cláusula WITH aninhada que defina outra CTE.
* Uma cláusula ORDER BY não pode ser usada em CTE_query_definition, exceto quando existe uma cláusula TOP especificada.
* Quando uma CTE é usada em uma instrução que faz parte de um lote, a instrução anterior deve ser seguida por um ponto-e-vírgula.
* Quando usado em instruções preparadas por sp_prepare, as CTEs se comportarão da mesma forma que outras instruções SELECT em PDW. No entanto, se as CTEs forem usadas como parte das CETAS preparadas por sp_prepare, o comportamento poderá ser diferente do SQL Server e de outras instruções de PDW, devido ao modo como a associação é implementada por sp_prepare. Se a instrução SELECT que faz referência à CTE estiver usando uma coluna incorreta que não existe na CTE, o sp_prepare passará sem detectar o erro, mas o erro será gerado durante sp_execute.

## <a name="recursive-ctes"></a>CTEs recursivas
As CTEs recursivas não têm suporte no SQL Data Warehouse.  A migração de CTEs recursivas pode ser praticamente completa e o melhor processo é dividi-la o em várias etapas. Normalmente, você pode usar um loop e preencher uma tabela temporária à medida que você itera sobre as consultas recursivas provisórias. Depois que a tabela temporária for preenchida, você pode retornar os dados como um único conjunto de resultados. Uma abordagem semelhante foi usada para resolver `GROUP BY WITH CUBE` no artigo [cláusula group by com as opções rollup/cube/grouping sets][cláusula group by com as opções rollup/cube/grouping sets].

## <a name="unsupported-system-functions"></a>Funções do sistema sem suporte
Também há algumas funções do sistema que não têm suporte. Estas são algumas das principais e que normalmente são usadas em data warehouse:

* NEWSEQUENTIALID()
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE()

Alguns desses problemas podem ser solucionados.

## <a name="rowcount-workaround"></a>Solução alternativa @@ROWCOUNT
Para solucionar a falta de suporte para @@ROWCOUNT,, crie um procedimento armazenado que recuperará a última contagem de linhas de sys.dm_pdw_request_steps e, em seguida, execute `EXEC LastRowCount` após uma instrução DML.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista completa de todas as instruções T-SQL com suporte, confira [Tópicos do Transact-SQL][Tópicos do Transact-SQL].

<!--Image references-->

<!--Article references-->
[Junções ANSI em atualizações]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[Junções ANSI em exclusões]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[instrução merge]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERT..EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Tópicos do Transact-SQL]: ./sql-data-warehouse-reference-tsql-statements.md

[cursores]: ./sql-data-warehouse-develop-loops.md
[SELECT..INTO]: ./sql-data-warehouse-develop-ctas.md#selectinto
[cláusula group by com as opções rollup/cube/grouping sets]: ./sql-data-warehouse-develop-group-by-options.md
[níveis de aninhamento superiores a 8]: ./sql-data-warehouse-develop-transactions.md
[atualizando por meio de exibições]: ./sql-data-warehouse-develop-views.md
[uso de select para atribuição de variável]: ./sql-data-warehouse-develop-variable-assignment.md
[nenhum tipo de dados MAX para cadeias de caracteres SQL dinâmicas]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


