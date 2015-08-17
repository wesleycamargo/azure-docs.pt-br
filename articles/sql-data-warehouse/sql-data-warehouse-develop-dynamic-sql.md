<properties
   pageTitle="SQL dinâmico no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para usar SQL dinâmico no SQL Data Warehouse do Azure para desenvolvimento de soluções."
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

# SQL dinâmico no SQL Data Warehouse
Ao desenvolver o código do aplicativo para o SQL Data Warehouse talvez você tenha que precisar usar sql dinâmico para ajudar a fornecer soluções flexíveis, genéricas e modulares. No entanto, o SQL Data Warehouse não oferece suporte a tipos de dados blob no momento. Isso pode limitar o tamanho de suas sequências de caracteres como tipos de blob que incluem tipos varchar(max) e nvarchar(max). Você pode achar que você usou esses tipos no código do aplicativo ao criar cadeias de caracteres muito grandes de código SQL dinâmico, que você precisa executar.

Nessas situações, você precisará quebrar o código em partes e use a instrução EXEC em seu lugar.

Um exemplo simplificado está disponível abaixo:

```
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se a cadeia de caracteres não for particularmente longa, você pode usar [sp\_executesql][] normalmente.


## Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Visão geral do desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp\_executesql]: https://msdn.microsoft.com/pt-br/library/ms188001.aspx

<!--Other Web references-->

<!---HONumber=August15_HO6-->