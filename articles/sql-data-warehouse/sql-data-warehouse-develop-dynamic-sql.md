---
title: "SQL dinâmico no SQL Data Warehouse | Microsoft Docs"
description: "Dicas para usar SQL dinâmico no SQL Data Warehouse do Azure para desenvolvimento de soluções."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: a948c2c3-3cd1-4373-90a9-79e59414b778
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6da1efc35b624e0b06693037b4f91d71f0f40eb4


---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL dinâmico no SQL Data Warehouse
Ao desenvolver o código do aplicativo para o SQL Data Warehouse, talvez seja preciso usar um sql dinâmico para ajudar a fornecer soluções flexíveis, genéricas e modulares. No momento, o SQL Data Warehouse não dá suporte a tipos de dados de blob. Isso pode limitar o tamanho de suas sequências de caracteres como tipos de blob que incluem tipos varchar(max) e nvarchar(max). Se você usou estes tipos no código do seu aplicativo ao compilar cadeias de caracteres muito grandes, você precisará dividir o código em partes e usar a instrução EXEC em seu lugar.

Um exemplo simples:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se a cadeia de caracteres for curta, você poderá usar [sp_executesql][sp_executesql] normalmente.

> [!NOTE]
> Instruções executadas como SQL dinâmico ainda estarão sujeitas a todas as regras de validação de TSQL.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [visão geral de desenvolvimento][visão geral de desenvolvimento].

<!--Image references-->

<!--Article references-->
[visão geral de desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


