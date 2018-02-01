---
title: "Guia de início rápido – Azure Time Series Insights Explorer | Microsoft Docs"
description: "Este guia de início rápido mostra como começar a usar o Azure Time Series Insights Explorer no navegador da Web para visualizar grandes volumes de dados de IoT. Conheça os principais recursos em um ambiente de demonstração."
services: time-series-insights
ms.service: time-series-insights
author: MarkMcGeeAtAquent
ms.author: v-mamcge
manager: jhubbard
editor: MarkMcGeeAtAquent, jasonwhowell, kfile, MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc
ms.date: 11/15/2017
ms.openlocfilehash: 3bad625e598d4249496849d3be020be083f8da7f
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2018
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Guia de início rápido: Explorar o Azure Time Series Insights
Este guia de início rápido mostra como começar a usar o Azure Time Series Insights Explorer em um ambiente de demonstração gratuita. Você aprenderá a usar o navegador da Web para visualizar grandes volumes de dados de IoT e conhecerá os principais recursos do Time Series Insights Explorer. 

O Azure Time Series Insights é um serviço totalmente gerenciado de análise, armazenamento e visualização que facilita a exploração e análise de bilhões de eventos de IoT simultaneamente. Ele oferece uma visão global dos dados, permitindo que você valide rapidamente sua solução IoT e evite o tempo de inatividade dispendioso para dispositivos críticos, ajudando você a descobrir tendências ocultas, detectar anomalias e realizar análises de causa raiz quase em tempo real.  Caso esteja criando um aplicativo que precisa armazenar ou consultar dados de série temporal, desenvolva-o usando as APIs REST do Time Series Insights.

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="explore-time-series-insights-explorer-in-a-demo-environment"></a>Descubra o Time Series Insights Explorer em um ambiente de demonstração

1. No navegador, navegue para [https://insights.timeseries.azure.com/demo](https://insights.timeseries.azure.com/demo). 

2. Se solicitado, faça logon no Time Series Insights Explorer usando suas credenciais da conta do Azure. 
 
3. A página de tour rápido do Time Series Insights será exibida. Clique em **Avançar** para iniciar o tour rápido.

   ![Clique em Avançar](media/quickstart/quickstart1.png)

4. O **Painel de seleção de tempo** será exibido. Use esse painel para selecionar um período a ser visualizado.

   ![Painel de seleção de tempo](media/quickstart/quickstart2.png)

5. Clique e arraste na região e, em seguida, clique no botão **Pesquisar**.
 
   ![Selecionar um período](media/quickstart/quickstart3.png) 

   O Time Series Insights exibe uma visualização de gráfico para o período especificado. Realize várias ações internas com o gráfico de linhas, como filtragem, anexação, classificação e empilhamento. 

   Para retornar ao **Painel de seleção de tempo**, clique na seta para baixo, conforme mostrado:

   ![Gráfico](media/quickstart/quickstart4.png)

6. Clique em **Adicionar** no **Painel de termos** para adicionar um novo termo de pesquisa.

   ![Adicionar item](media/quickstart/quickstart5.png)

7. No gráfico, você pode selecionar uma região, clicar com o botão direito do mouse na região e selecionar **Explorar Eventos**.
 
   ![Explorar Eventos](media/quickstart/quickstart6.png)

   Uma grade dos dados brutos é exibida na região que está sendo explorada:

   ![Exibição de grade](media/quickstart/quickstart7.png)

8. Edite os termos para alterar os valores no gráfico e adicione outro termo para fazer uma correlação cruzada entre diferentes tipos de valores:

   ![Adicionar um termo](media/quickstart/quickstart8.png)

9. Insira um termo de filtro na caixa **Filtrar série...**  para a filtragem de série ad hoc. Para o guia de início rápido, insira **Station5** para fazer uma correlação cruzada entre a temperatura e a pressão dessa estação.
 
   ![Filtrar série](media/quickstart/quickstart9.png)

Depois de concluir o guia de início rápido, faça experiências com o conjunto de dados de exemplo para criar visualizações diferentes. 

### <a name="next-steps"></a>Próximas etapas
Você está pronto para criar seu próprio ambiente do Time Series Insights:
> [!div class="nextstepaction"]
> [Planejar o ambiente do Time Series Insights](time-series-insights-environment-planning.md)
