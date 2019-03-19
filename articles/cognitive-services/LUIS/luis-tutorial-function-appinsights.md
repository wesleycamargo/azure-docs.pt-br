---
title: Application Insights, Node.js
titleSuffix: Azure Cognitive Services
description: Crie um bot integrado com um aplicativo do LUIS e o Application Insights usando Node.js.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 1c44d2e41d37a9236ee6d6936c349acf5ca5e44c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098756"
---
# <a name="add-luis-results-to-application-insights-and-azure-functions"></a>Adicionar resultados do LUIS às funções do Application Insights e do Azure
Este tutorial adiciona informações de solicitação e resposta do LUIS ao armazenamento de dados telemétricos do [Application Insights](https://azure.microsoft.com/services/application-insights/). Quando você tiver esses dados, poderá consultá-los com a linguagem de Kusto ou com o Power BI para analisar, agregar e relatar intenções e entidades do enunciado em tempo real. Esta análise ajuda a determinar se você deve adicionar ou editar as intenções e as entidades do seu aplicativo LUIS.

O bot é criado com o Bot Framework 3.x e o bot de aplicativo Web do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar a biblioteca do Application Insights a um bot de aplicativo Web
> * Capturar e enviar resultados da consulta LUIS para o Application Insights
> * Veja o Application Insights para as principais intenções, pontuações e declarações

## <a name="prerequisites"></a>Pré-requisitos

* O bot de aplicativo Web do LUIS do **[tutorial anterior](luis-nodejs-tutorial-build-bot-framework-sample.md)** com o Application Insights ativado. 

> [!Tip]
> Se você ainda não tiver uma assinatura, poderá se inscrever para uma [conta gratuita](https://azure.microsoft.com/free/).

Todo o código neste tutorial está disponível no [repositório do GitHub Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/nodejs) e cada linha associada a este tutorial é comentada com `//APPINSIGHT:`. 

## <a name="web-app-bot-with-luis"></a>Bot do aplicativo Web com LUIS
Este tutorial presume que você tem um código semelhante ao seguinte ou que você concluiu o [outro tutorial](luis-nodejs-tutorial-build-bot-framework-sample.md): 

   [!code-javascript[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/nodejs/app.js "Web app bot with LUIS")]

## <a name="add-application-insights-library-to-web-app-bot"></a>Adicionar a biblioteca do Application Insights a bot de aplicativo Web
No momento, o serviço do Application Insights, usado nesse bot de aplicativo Web, coleta telemetria de estado geral para o bot. Ele não coleta informações de solicitação e resposta do LUIS de que você precisa para verificar e corrigir suas intenções e entidades. 

Para capturar a solicitação e a resposta do LUIS, o bot do aplicativo Web precisa ter o pacote NPM do **[Application Insights](https://www.npmjs.com/package/applicationinsights)** instalado e configurado no arquivo **app.js**. Os manipuladores de intenção da caixa de diálogo precisam enviar a solicitação do LUIS e as informações de resposta ao Application Insights. 

1. No portal do Azure, no serviço de bot do aplicativo Web, selecione **Compilar** na seção **Gerenciamento de Bot**. 

    ![No portal do Azure, no serviço de bot do aplicativo Web, selecione “Compilar” na seção “Gerenciamento de Bot”.](./media/luis-tutorial-appinsights/build.png)

2. Uma nova guia do navegador é aberta com o Editor de Serviço de Aplicativo. Selecione o nome do aplicativo na barra superior e selecione **Abrir o Console do Kudu**. 

    ![Selecione o nome do aplicativo na barra superior e selecione “Abrir o Console do Kudu”.](./media/luis-tutorial-appinsights/kudu-console.png)

3. No console, insira o seguinte comando para instalar o Application Insights e os pacotes Underscore:

    ```console
    cd site\wwwroot && npm install applicationinsights && npm install underscore
    ```

    ![Use comandos npm para instalar o Application Insights e os pacotes Underscore](./media/luis-tutorial-appinsights/npm-install.png)

    Aguarde a instalação dos pacotes:

    ```console
    luisbot@1.0.0 D:\home\site\wwwroot
    `-- applicationinsights@1.0.1 
      +-- diagnostic-channel@0.2.0 
      +-- diagnostic-channel-publishers@0.2.1 
      `-- zone.js@0.7.6 
    
    npm WARN luisbot@1.0.0 No repository field.
    luisbot@1.0.0 D:\home\site\wwwroot
    +-- botbuilder-azure@3.0.4
    | `-- azure-storage@1.4.0
    |   `-- underscore@1.4.4 
    `-- underscore@1.8.3 
    ```

    Você concluiu a guia do navegador do console do Kudu.

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Capturar e enviar resultados da consulta LUIS para o Application Insights
1. Na guia do navegador Editor de Serviço de Aplicativo, abra o arquivo **app.js**.

2. Adicione as seguintes bibliotecas NPM nas linhas `requires` existentes:

   [!code-javascript[Add NPM packages to app.js](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=12-16 "Add NPM packages to app.js")]

3. Crie o objeto Application Insights e use a configuração de aplicativo de bot do aplicativo Web **BotDevInsightsKey**: 

   [!code-javascript[Create the Application Insights object](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=68-80 "Create the Application Insights object")]

4. Adicione a função **appInsightsLog**:

   [!code-javascript[Add the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=82-109 "Add the appInsightsLog function")]

    A última linha da função é o local em que os dados são adicionados ao Application Insights. O nome do evento é **LUIS-results**, um nome exclusivo diferente de quaisquer outros dados telemétricos coletados por este bot do aplicativo Web. 

5. Use a função **appInsightsLog**. Você pode adicioná-lo a todas as caixas de diálogo de intenção:

   [!code-javascript[Use the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=117-118 "Use the appInsightsLog function")]

6. Para testar seu bot de aplicativo Web, use o recurso **Testar no Webchat**. Você não deve nenhuma diferença porque todo o trabalho está no Application Insights, e não nas respostas de bot.

## <a name="view-luis-entries-in-application-insights"></a>Exibir entradas do LUIS no Application Insights
Abra o Application Insights para ver as entradas do LUIS. 

1. No portal, selecione **Todos os recursos** e, em seguida, filtre pelo nome do bot do aplicativo Web. Clique no recurso com o tipo **Application Insights**. O ícone para o Application Insights é uma lâmpada. 

    ! [[Pesquisa para o app insights no Portal do Azure](./media/luis-tutorial-appinsights/search-for-app-insights.png)

2. Quando o recurso se abrir, clique no ícone **Pesquisar** da lupa no painel bem à direita. Um novo painel à direita é exibido. Dependendo de quantos dados telemétricos são encontrados, o painel poderá levar um segundo para ser exibido. Pesquise `LUIS-results` e pressione Enter no teclado. A lista é limitada a apenas os resultados da consulta do LUIS adicionados com este tutorial.

    ![Filtrar para dependências](./media/luis-tutorial-appinsights/app-insights-filter.png)

3. Selecione a entrada superior. Uma nova janela exibe dados mais detalhados, incluindo os dados personalizados para a consulta do LUIS mais à direita. Os dados incluem a intenção principal e sua pontuação.

    ![Detalhes de dependência](./media/luis-tutorial-appinsights/app-insights-detail.png)

    Quando terminar, selecione o **X** no canto superior direito para voltar à lista de itens de dependência. 


> [!Tip]
> Se você quiser salvar a lista de dependências e retornar a ela mais tarde, clique em **…Mais** e em **Salvar favorito**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Consulte o Application Insights para intenções, pontuações e enunciados
O Application Insights oferece a capacidade de consultar os dados com a linguagem [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics), além de exportar para [PowerBI](https://powerbi.microsoft.com). 

1. Clique em **Análise** na parte superior da lista de dependência acima da caixa de filtro. 

    ![Botão Analytics](./media/luis-tutorial-appinsights/analytics-button.png)

2. Uma nova janela é aberta com uma janela de consulta na parte superior e uma janela de tabela de dados abaixo. Se você tiver usado bancos de dados antes, essa organização será familiar. A consulta inclui todos os itens nas últimas 24 horas começando com o nome `LUIS-results`. A coluna **CustomDimensions** tem os resultados da consulta do LUIS como pares de nome/valor.

    ![Janela de consulta de análise](./media/luis-tutorial-appinsights/analytics-query-window.png)

3. Para extrair as principais intenções, pontuações e enunciados, adicione o seguinte logo acima da última linha na janela de consulta:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_intent_intent)
    | extend score = todouble(customDimensions.LUIS_intent_score)
    | extend utterance = tostring(customDimensions.LUIS_text)
    ```

4. Execute a consulta. Role bem para a direita na tabela de dados. As novas colunas de topIntent, pontuação e enunciado estão disponíveis. Clique na coluna topIntent para classificar.

    ![Principal intenção de análise](./media/luis-tutorial-appinsights/app-insights-top-intent.png)


Saiba mais sobre a [Linguagem de consulta Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) ou [exporte os dados para o PowerBi](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Próximas etapas

Outras informações que você talvez queira adicionar aos dados do Application Insights incluem ID do aplicativo, ID de versão, data da última alteração do modelo, data do último treinamento, data da última publicação. Esses valores podem ser recuperados da URL do ponto de extremidade (ID do aplicativo e ID de versão) ou de uma chamada à [API de criação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) e então definidos nas configurações do bot do aplicativo Web e capturados de lá.  

Se você estiver usando a mesma assinatura do ponto de extremidade para mais de um aplicativo LUIS, também deverá incluir a ID da assinatura e uma propriedade informando que ela é uma chave compartilhada. 

> [!div class="nextstepaction"]
> [Saiba mais sobre declarações de exemplo](luis-how-to-add-example-utterances.md)
