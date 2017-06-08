---
title: "Introdução ao Azure Data Lake Analytics usando o Portal do Azure | Microsoft Docs"
description: 'Saiba como usar o portal do Azure para criar uma conta do Data Lake Analytics, criar um trabalho do Data Lake Analytics usando o U-SQL e enviar o trabalho. '
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: eb85d8ef6b29605d7e26b0d2139a4a95c35141fb
ms.contentlocale: pt-br
ms.lasthandoff: 05/31/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-portal"></a>Tutorial: introdução ao Azure Data Lake Analytics usando o portal do Azure
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como usar o portal do Azure para criar contas do Azure Data Lake Analytics, definir trabalhos no [U-SQL](data-lake-analytics-u-sql-get-started.md) e enviar trabalhos ao serviço do Data Lake Analytics. Para saber mais sobre a Análise Data Lake, consulte a [Visão geral da Análise Data Lake do Azure](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter uma **assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-data-lake-analytics-account"></a>Criar conta da Análise Data Lake

Agora, você criará um Data Lake Analytics e uma conta Data Lake Store simultaneamente.  Esta etapa é simples e demora apenas 60 para concluir.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Clique em **Novo** >  **Intelligence + análise** > **Data Lake Analytics**.
3. Selecione os valores para os seguintes itens:
   * **Nome**: Nome de sua conta do Data Lake Analytics (são permitidos somente letras minúsculas e números).
   * **Assinatura**: escolha a assinatura do Azure usada para a conta da Análise.
   * **Grupo de Recursos**. Selecione um Grupo de Recursos do Azure existente ou crie um novo.
   * **Local**. Selecione um datacenter do Azure para a conta da Análise Data Lake.
   * **Data Lake Store**: siga as instruções para criar uma nova conta do Data Lake Store ou selecione uma existente. 
4. Opcionalmente, selecione um tipo de preço para sua conta Data Lake Analytics.
5. Clique em **Criar**. 

## <a name="create-and-submit-data-lake-analytics-jobs"></a>Criar e enviar trabalhos de Análise Data Lake
Depois de preparar os dados de origem, você pode começar a desenvolver um script U-SQL.  

**Para enviar um trabalho**

1. Na conta Data Lake Analytics, clique em **Novo Trabalho**.
2. Insira o **Nome do Trabalho**e o seguinte script U-SQL:

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
    TO "/Output/SearchLog-from-Data-Lake.csv"
    USING Outputters.Csv();
```


Este script U-SQL lê o arquivo de dados de origem usando **Extractors.Tsv()**, em seguida, cria um arquivo csv usando **Outputters.Csv()**.

1. Clique em **Enviar Trabalho**.   
2. Aguarde até que o status do trabalho seja alterado para **Êxito**.
3. Se o trabalho falhar, consulte [Monitorar e solucionar problemas dos trabalhos do Data Lake Analytics](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).
4. Clique na guia **Saída** e em `SearchLog-from-Data-Lake.csv`. 

## <a name="see-also"></a>Consulte também

* Para começar a desenvolver aplicativos U-SQL, consulte [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Para aprender a usar o U-SQL, veja [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para obter as tarefas de gerenciamento, confira [Gerenciar o Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-manage-use-portal.md).

