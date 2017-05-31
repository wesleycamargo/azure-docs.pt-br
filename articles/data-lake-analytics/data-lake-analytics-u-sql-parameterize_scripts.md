---
title: Parametrizar scripts U-SQL no Azure Data Lake Analytics | Microsoft Docs
description: Saiba como parametrizar scripts U-SQL no Azure Data Lake Analytics.
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
ms.date: 05/10/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 6318fa8b14d8bb7d650522e2d96a5b1417afe3df
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="parameterizing-u-sql-scripts"></a>Parametrizando scripts U-SQL

Ao trabalhar com scripts U-SQL, é conveniente parametrizá-los. Os parâmetros permitem que o corpo principal permaneça o mesmo e ainda controlam a execução do script passando valores de parâmetros separados. Um cenário típico envolve um script que processa dados em um intervalo de tempo e um desenvolvedor pode desejar parametrizar o script em uma data de início e uma data de término. 

Há duas maneiras nas quais os scripts são parametrizados:
* O script permite a adição segura de parâmetros no início do script. O U-SQL dá suporte a esse método.
* Uma API de Envio de trabalho permite que os chamadores enviem parâmetros. O U-SQL não dá suporte a esse método.

## <a name="example-scripts"></a>Scripts de exemplo

Este é um script inicial simples:

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog
    TO "/output/data.csv"
    USING Outputters.Csv();
```    
   
Nesse cenário, parametrizamos a ``Region`` adicionando uma instrução `DECLARE EXTERNAL`.

```
DECLARE EXTERNAL @TargetRegion = "en-us";

@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog
    TO "/output/data.csv"
    USING Outputters.Csv();
```    

Ao ser executado, esse script usa como padrão a filtragem apenas das linhas da região `en-us`. Até agora, definimos um parâmetro no script. A palavra-chave `EXTERNAL` indica que alguém pode redefinir o valor `@TargetRegion` precedendo uma instrução DECLARE que define o parâmetro `@TargetRegion`.

O script a seguir mostra a aparência do script parametrizado quando uma pessoa forneceu um valor para o parâmetro. Esse script agora filtra as linhas que têm a região `en-gb`.

```
DECLARE @TargetRegion = "en-gb";

DECLARE EXTERNAL @TargetRegion = "en-us";

@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog
    TO "/output/data.csv"
    USING Outputters.Csv();
```    


## <a name="next-steps"></a>Próximas etapas
* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Monitorar e solucionar problemas em trabalhos do Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

