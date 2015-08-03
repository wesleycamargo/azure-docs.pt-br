<properties
   pageTitle="Migrar seu código SQL para o SQL Data Warehouse | Microsoft Azure"
   description="Dicas para migrar seu código SQL para o SQL Data Warehouse do Azure para desenvolvimento de soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/25/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Migrar seu código SQL para o SQL Data Warehouse

Para garantir a compatibilidade de seu código com o SQL Data Warehouse, provavelmente será necessário fazer alterações em seu código base. Alguns recursos do SQL Data Warehouse também podem melhorar consideravelmente o desempenho, pois foram criados para funcionarem diretamente de uma maneira distribuída. No entanto, para manter o desempenho e a escala, alguns recursos também não estão disponíveis.

## Alterações de código Transact-SQL

A lista a seguir resume os principais recursos que não têm suporte no SQL Data Warehouse do Azure:

- Junções ANSI em atualizações
- Junções ANSI em exclusões
- instrução merge
- junções entre bancos de dados
- [instruções pivot e unpivot][]
- [cursores][]
- [SELECT..INTO][]
- INSERT..EXEC
- cláusula output
- funções definidas pelo usuário embutidas
- funções com várias instruções
- expressões de tabela comuns (CTE) recursivas
- atualizações por meio de CTEs
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

Felizmente, a maioria dessas limitações pode ser solucionada. Foram incluídas explicações nos artigos de desenvolvimento relevantes mencionados acima.

Também há algumas funções do sistema que não têm suporte. Estas são algumas das principais e que normalmente são usadas em data warehouse:

- NEWID()
- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT_BIG
- ERROR_LINE()

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
[instruções pivot e unpivot]: sql-data-warehouse-develop-pivot-unpivot.md
[cursores]: sql-data-warehouse-develop-loops.md
[SELECT..INTO]: sql-data-warehouse-develop-ctas.md
[cláusula group by com as opções rollup/cube/grouping sets]: sql-data-warehouse-develop-group-by-options.md
[níveis de aninhamento superiores a 8]: sql-data-warehouse-develop-transactions.md
[atualizando por meio de exibições]: sql-data-warehouse-develop-views.md
[uso de select para atribuição de variável]: sql-data-warehouse-develop-variable-assignment.md
[nenhum tipo de dados MAX para cadeias de caracteres SQL dinâmicas]: sql-data-warehouse-develop-dynamic-sql.md
[visão geral sobre desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO4-->