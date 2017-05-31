---
title: "Combinar conjuntos de linhas com junções U-SQL no Azure Data Lake Analytics | Microsoft Docs"
description: "Aprenda como o U-SQL usa os operadores de junção para combinar conjuntos de linhas no Azure Data Lake Analytics."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/09/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 5ffc7116812e9f18f972eac2daff7935b00891f1
ms.contentlocale: pt-br
ms.lasthandoff: 05/11/2017


---
# <a name="combine-rowsets-with-u-sql-joins"></a>Combinar conjuntos de linhas com junções U-SQL

O U-SQL fornece operadores de junção comum, como INNER JOIN, LEFT/RIGHT/FULL OUTER JOIN, SEMI JOIN, para unir não só tabelas, mas quaisquer conjuntos de linhas (mesmo aqueles gerados de arquivos).

O script a seguir une o ``@searchlog`` com um log de impressão de anúncio e fornece os anúncios para a cadeia de caracteres de consulta de determinada data.

```
@adlog =
    EXTRACT UserId int,
            Ad string,
            Clicked int
    FROM "/Samples/Data/AdsLog.tsv"
    USING Extractors.Tsv();

@join =
    SELECT a.Ad, s.Query, s.Start AS Date
    FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                    ON a.UserId == s.UserId
    WHERE a.Clicked == 1;

OUTPUT @join   
    TO "/output/Searchlog-join.csv"
    USING Outputters.Csv();
```

O U-SQL dá suporte apenas à sintaxe de junção em conformidade com ANSI: predicado Rowset1 JOIN Rowset2 ON. _Não_ há suporte para a sintaxe antiga do predicado FROM Rowset1, Rowset2 WHERE.
O predicado em JOIN deve ser uma junção de igualdade e não ter expressão. Se você quiser usar uma expressão, adicione-o à cláusula select de um rowset anterior. Se você quiser fazer uma comparação diferente, pode movê-lo para a cláusula WHERE.

### <a name="next-steps"></a>Próximas etapas
* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Usar funções da janela do U-SQL para trabalhos de análise do Azure Data Lake](data-lake-analytics-use-window-functions.md)


