<properties
   pageTitle="Execução de loops no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para execução de loops do Transact_SQL e substituição de cursores no SQL Data Warehouse Azure para desenvolver soluções."
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

# Executar loops no SQL Data Warehouse
O SQL Data Warehouse oferece suporte a loop [WHILE][] para executar repetidamente blocos de instrução. Isso continuará enquanto as condições especificadas forem verdadeiras ou até que o código especificamente encerre o loop usando a palavra-chave `BREAK`. A execução de loops é particularmente útil para a substituição de cursores definidos no código SQL. Felizmente, quase todos os cursores que são escritos em código SQL são do tipo somente leitura de avanço rápido. Portanto, a execução de loops [WHILE] é uma ótima alternativa se você precisar substituir um.

## Aproveitando a execução de loops e substituindo cursores no SQL Data Warehouse
No entanto, antes de mergulhar na memória, primeiro você deve fazer a seguinte pergunta: “Esse cursor poderia ser reescrito para usar operações baseadas em conjunto?”. Em muitos casos, a resposta será “Sim” e geralmente esta será a melhor abordagem. Uma operação baseada em conjunto geralmente terá um desempenho consideravelmente mais rápido do que uma abordagem iterativa de linha por linha.

Os cursores somente leitura de avanço rápido podem ser facilmente substituídos por um constructo de looping. Veja abaixo um exemplo simples. Este exemplo de código atualiza as estatísticas para cada tabela no banco de dados. Iterando sobre as tabelas no loop somos capazes de executar cada comando em sequência.

Primeiro, crie uma tabela temporária que contém um número de linha exclusivo usado para identificar as instruções individuais:
  
```
CREATE TABLE #tbl 
WITH 
(   LOCATION     = USER_DB
,   DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) ) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Em segundo lugar, inicialize as variáveis necessárias para executar o loop:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Agora, execute um loop sobre as instruções para executar uma por vez:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Finalmente, descarte a tabela temporária criada na primeira etapa

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Visão geral do desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[WHILE]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->

<!---HONumber=Sept15_HO2-->