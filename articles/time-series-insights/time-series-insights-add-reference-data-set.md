---
title: "Como adicionar um conjunto de dados de referência ao seu ambiente Azure Time Series Insights | Microsoft Docs"
description: "Este artigo descreve como adicionar um conjunto de dados de referência ao seu ambiente Azure Time Series Insights. É útil adicionar dados de referência em sua fonte de dados para aumentar os valores."
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 7cdcefbd0daec3b7bab59680afa1470624583c74
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Criar um conjunto de dados de referência para o seu ambiente Time Series Insights usando o Portal do Azure

Este artigo descreve como adicionar um conjunto de dados de referência ao seu ambiente Azure Time Series Insights. É útil adicionar dados de referência em sua fonte de dados para aumentar os valores.

Um conjunto de dados de referência é uma coleção de itens que são aumentados com os eventos da fonte de evento. O mecanismo de entrada da Análise de Séries Temporais une um evento da fonte de evento a um item em seu conjunto de dados de referência. Esse evento aumentado é disponibilizado para consulta. Essa junção baseia-se nas chaves definidas no conjunto de dados de referência.

## <a name="add-a-reference-data-set"></a>Adicionar um conjunto de dados de referência

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Localize seu ambiente Time Series Insights existente. Clique em **Todos os recursos** no menu à esquerda do Portal do Azure. Selecione o seu ambiente de Análise de Séries Temporais.

3. No cabeçalho **Topologia do Ambiente**, selecione **Conjunto de Dados de Referência**.

    ![Criar o conjunto de dados de referência Análises de Séries Temporais](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. Selecione **+ Adicionar** para adicionar um novo conjunto de dados de referência.

5. Especifique um **Nome do Conjunto de Dados de Referência** único.

    ![Criar o conjunto de dados de referência Análises de Séries Temporais - detalhes](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

6. Especifique o **Nome da chave** no campo vazio e selecione **Tipo**. Esse nome e tipo são usados para selecionar a propriedade correta dos eventos na fonte de eventos para adicionar aos dados de referência. 

   Por exemplo, se você fornecer o nome da chave como **DeviceId** e o tipo como **String**, então o mecanismo de entrada do Time Series Insights procura uma propriedade com o nome **DeviceId** do tipo **String** em cada evento de entrada a ser pesquisada e adicionada. Você pode fornecer mais de uma chave para unir ao evento. A correspondência de nome de chave diferencia maiúsculas de minúsculas.

7. Selecione **Criar**.

## <a name="next-steps"></a>Próximas etapas
* [Gerenciar dados de referência](time-series-insights-manage-reference-data-csharp.md) programaticamente.
* Para obter a referência completa de API, confira o documento [API de Dados de Referência](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api).
