<properties
   pageTitle="Usar rótulos para consultas de instrumento no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para usar rótulos para consultas de instrumento no SQL Data Warehouse do Azure para desenvolvimento de soluções."
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
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Usar rótulos para consultas de instrumento no SQL Data Warehouse
O SQL Data Warehouse oferece suporte a um conceito chamado rótulos de consulta. Antes de entrar em qualquer profundidade, vamos examinar um exemplo:

	```
	SELECT *
	FROM sys.tables
	OPTION (LABEL = 'My Query Label')
	;
	```

Essa última linha marca a cadeia de caracteres ‘Meu Rótulo de Consulta’ à consulta. Isso é particularmente útil, pois o rótulo é capaz de executar consultas por meio de DMVs. Isso fornece um mecanismo para rastrear consultas de problemas e também para ajudar a identificar o andamento por meio de uma execução ETL.

Uma boa convenção de nomenclatura ajuda muito aqui. Por exemplo, algo como ‘ PROJECT : PROCEDURE : STATEMENT : COMMENT’ ajudaria para identificar exclusivamente a consulta entre todos os códigos no controle de origem.

Para pesquisar por rótulo, você pode usar a consulta a seguir que usa os modos de exibição de gerenciamento dinâmico:

	```
	SELECT  *
	FROM    sys.dm_pdw_exec_requests r
	WHERE   r.[label] = 'My Query Label'
	;
	``` 

> [AZURE.NOTE]É essencial encapsular colchetes ou aspas duplas em torno da palavra do rótulo ao consultar. Rótulo é uma palavra reservada e causará um erro se não for delimitada.


## Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Visão geral do desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO1-->