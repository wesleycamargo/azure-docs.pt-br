---
title: "Usar rótulos para consultas de instrumento no SQL Data Warehouse | Microsoft Docs"
description: "Dicas para usar rótulos para consultas de instrumento no SQL Data Warehouse do Azure para desenvolvimento de soluções."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 44988de8-04c1-4fed-92be-e1935661a4e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5c72cd2c80d9fcee3d9340c23a629451c54c9156


---
# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>Usar rótulos para consultas de instrumento no SQL Data Warehouse
O SQL Data Warehouse oferece suporte a um conceito chamado rótulos de consulta. Antes de entrar em qualquer profundidade, vamos examinar um exemplo:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Essa última linha marca a cadeia de caracteres ‘Meu Rótulo de Consulta’ à consulta. Isso é particularmente útil, pois o rótulo é capaz de executar consultas por meio de DMVs. Isso fornece um mecanismo para rastrear consultas de problemas e também para ajudar a identificar o andamento por meio de uma execução ETL.

Uma boa convenção de nomenclatura ajuda muito aqui. Por exemplo, algo como ‘ PROJECT : PROCEDURE : STATEMENT : COMMENT’ ajudaria para identificar exclusivamente a consulta entre todos os códigos no controle de origem.

Para pesquisar por rótulo, você pode usar a consulta a seguir que usa os modos de exibição de gerenciamento dinâmico:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> É essencial encapsular colchetes ou aspas duplas em torno da palavra do rótulo ao consultar. Rótulo é uma palavra reservada e causará um erro se não for delimitada.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [visão geral de desenvolvimento][visão geral de desenvolvimento].

<!--Image references-->

<!--Article references-->
[visão geral de desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


