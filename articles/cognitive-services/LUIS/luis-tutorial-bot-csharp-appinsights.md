---
title: Application Insights usando C#
titleSuffix: Azure Cognitive Services
description: Crie um bot integrado com um aplicativo do LUIS e o Application Insights usando C#.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: d546951cf283cf15874b1b6d95da75549a8a93ac
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884929"
---
# <a name="add-luis-results-to-application-insights-with-a-bot-in-c"></a>Adicionar resultados do LUIS ao Application Insights com um bot em C#

Este tutorial adiciona informações de resposta do LUIS ao armazenamento de dados de telemetria do [Application Insights](https://azure.microsoft.com/services/application-insights/). Quando você tiver esses dados, poderá consultá-los com a linguagem de Kusto ou Power BI para analisar, agregar e relatar intenções e entidades da declaração em tempo real. Esta análise ajuda a determinar se você deve adicionar ou editar as intenções e as entidades do seu aplicativo LUIS.

O bot é criado com o Bot Framework 3.x e o bot de aplicativo Web do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar o Application Insights a um bot do aplicativo Web
> * Capturar e enviar os resultados de consulta do LUIS para o Application Insights
> * Veja o Application Insights para as principais intenções, pontuações e declarações

## <a name="prerequisites"></a>Pré-requisitos

* O bot de aplicativo Web do LUIS do **[tutorial anterior](luis-csharp-tutorial-build-bot-framework-sample.md)** com o Application Insights ativado.
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) instalado localmente no computador.

> [!Tip]
> Se você ainda não tiver uma assinatura, poderá se inscrever para uma [conta gratuita](https://azure.microsoft.com/free/).

Todo o código neste tutorial está disponível no [repositório do GitHub Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/csharp) e cada linha associada a este tutorial é comentada com `//LUIS Tutorial:`.

## <a name="review-luis-web-app-bot"></a>Examine o bot de aplicativo Web do LUIS

Este tutorial presume que você tem um código semelhante ao seguinte ou que você concluiu o [outro tutorial](luis-csharp-tutorial-build-bot-framework-sample.md):

   [!code-csharp[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs "Web app bot with LUIS")]

## <a name="application-insights-in-web-app-bot"></a>Application Insights em um bot do aplicativo Web

No momento, o serviço do Application Insights, adicionado como parte da criação do serviço de bot do aplicativo Web, coleta a telemetria de estado geral para o bot. Ele não coleta informações de resposta do LUIS. Para analisar e aperfeiçoar o LUIS, você precisará das informações de resposta do LUIS.  

Para capturar a resposta do LUIS, o bot do aplicativo Web precisará ter o **[Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** instalado e configurado para o projeto.

## <a name="download-web-app-bot"></a>Baixar o bot do aplicativo Web

Use o [Visual Studio 2017](https://www.visualstudio.com/downloads/) para adicionar e configurar o Application Insights para o bot do aplicativo Web. Para usar o bot do aplicativo Web no Visual Studio, baixe o código correspondente.

1. No portal do Azure, para o bot do aplicativo Web, selecione **Compilar**.

    ![Selecione Compilar no portal](./media/luis-tutorial-bot-csharp-appinsights/download-build-menu.png)

2. Selecione **Baixar o arquivo zip** e aguarde até que o arquivo esteja preparado.

    ![Baixar o arquivo zip](./media/luis-tutorial-bot-csharp-appinsights/download-link.png)

3. Selecione **Baixar o arquivo zip** na janela pop-up. Lembre-se do local no computador porque você precisará dele na próxima seção.

    ![Pop-up Baixar o arquivo zip](./media/luis-tutorial-bot-csharp-appinsights/download-popup.png)

## <a name="open-solution-in-visual-studio-2017"></a>Abrir a solução no Visual Studio 2017

1. Extraia o arquivo em uma pasta.

2. Abra o Visual Studio 2017 e abra o arquivo da solução, `Microsoft.Bot.Sample.LuisBot.sln`. Se o aviso de segurança for exibido, selecione "OK".

    ![Abrir a solução no Visual Studio 2017](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-security-warning.png)

3. O Visual Studio precisa adicionar dependências para a solução. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Referências** e selecione **Gerenciar Pacotes NuGet**.

    ![Gerenciar pacotes NuGet](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-manage-nuget-packages.png)

4. O gerenciador de pacotes do NuGet mostra uma lista de pacotes instalados. Selecione **Restaurar** na barra amarela. Aguarde o processo de restauração ser concluído.

    ![Restaurar pacotes NuGet](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-restore-packages.png)

## <a name="add-application-insights-to-the-project"></a>Adicionar Application Insights ao projeto

Instale e configure o Application Insights no Visual Studio.

1. No Visual Studio 2017, no menu superior, selecione **Projeto**, selecione **Adicionar Application Insights Telemetry...**.

2. Na janela **Configuração do Application Insights**, selecione **Iniciar Gratuitamente**

    ![Comece configurando o Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configure-app-insights.png)

3. Registre seu aplicativo com o Application Insights. Você precisará inserir suas credenciais do portal do Azure.

4. O Visual Studio adiciona o Application Insights ao projeto, exibindo o status conforme faz isso.

    ![Status do Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-adding-application-insights-to-project.png)

    Quando o processo for concluído, a **Configuração do Application Insights** mostra o status do progresso.

    ![Status do progresso do Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configured-application-insights-to-project.png)

    A opção **Habilitar coleta de rastreamento** fica vermelha, significando que não está habilitada. Este tutorial não usa o recurso.

## <a name="build-and-resolve-errors"></a>Compilar e resolver erros

1. Compile a solução selecionando o menu **Criar**, em seguida, selecione **Recompilar Solução**. Aguarde a conclusão da compilação.

2. Se a compilação falhar com erros `CS0104`, será necessário corrigi-los. Na pasta `Controllers`, no `MessagesController.cs file`, corrija o uso ambíguo do tipo `Activity` prefixando o tipo de atividade com o tipo de conector. Para fazer isso, altere o nome `Activity` nas linhas 22 e 36 de `Activity` para `Connector.Activity`. Compile a solução novamente. Não deve haver mais erros de compilação.

    A fonte completa desse arquivo é:

    [!code-csharp[MessagesController.cs file](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/MessagesController.cs "MessagesController.cs file")]

## <a name="publish-project-to-azure"></a>Publicar projeto no Azure

O pacote do **Application Insights** agora está no projeto e está configurado corretamente para suas credenciais no portal do Azure. As alterações para o projeto precisam ser publicadas no Azure.

1. No **Gerenciador de Soluções**, clique o botão direito do mouse no nome do projeto e selecione **Publicar**.

    ![Publicar projeto no portal](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish.png)

2. Na janela **Publicar**, selecione **Criar novo perfil**.

    ![Como parte da publicação, crie o novo perfil.](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-1.png)

3. Selecione **Importar perfil** e selecione **OK**.

    ![Como parte da publicação, importe o perfil](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-2.png)

4. Na janela **Importar Arquivo de Configurações de Publicação**, navegue até a pasta do projeto, navegue até a pasta `PostDeployScripts`, selecione o arquivo que termina em `.PublishSettings` e selecione `Open`. Você agora configurou a publicação para este projeto.

5. Publique seu código-fonte local no serviço do bot selecionando o botão **Publicar**. A janela **Saída** mostra o status. O restante do tutorial está concluído no portal do Azure. Feche o Visual Studio 2017.

## <a name="open-three-browser-tabs"></a>Abrir três guias do navegador

No portal do Azure, localize o bot do aplicativo Web e abra-o. As etapas a seguir usam três exibições diferentes do bot do aplicativo Web. Talvez seja mais fácil ter três guias separadas abertas no navegador:
  
>  * Testar no chat da Web
>  * Editor do código online de Compilar/Abrir -> Editor de serviço do aplicativo
>  * Editor de serviço do aplicativo/Abrir console do Kudu -> Console de diagnóstico

## <a name="modify-basicluisdialogcs-code"></a>Modifique o código de BasicLuisDialog.cs

1. Na guia do navegador **Editor de serviço do aplicativo**, abra o arquivo `BasicLuisDialog.cs`.

2. Adicione a seguinte dependência do NuGet nas linhas `using` existentes:

   [!code-csharp[Add using statement](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=11-12 "Add using statement")]

3. Adicione a função `LogToApplicationInsights`:

   [!code-csharp[Add the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=61-92 "Add the LogToApplicationInsights function")]

    A chave de instrumentação do Application Insights já está na configuração de aplicativo do bot do aplicativo Web chamado `BotDevInsightsKey`.

    A última linha da função adiciona os dados ao Application Insights. O nome do rastreamento é `LUIS`, um nome exclusivo, além de quaisquer outros dados de telemetria coletados por esse bot do aplicativo Web. Todas as propriedades também são prefixadas com `LUIS_` para você ver quais dados este tutorial adiciona comparados com as informações que são fornecidas pelo bot do aplicativo Web.

4. Chamar a função `LogToApplicationInsights` na parte superior da função `ShowLuisResult`:

   [!code-csharp[Use the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=114-115 "Use the LogToApplicationInsights function")]

## <a name="build-web-app-bot"></a>Compilar o bot do aplicativo Web

1. Compile o bot do aplicativo Web de uma das duas maneiras. O primeiro método é clicar com o botão direito em `build.cmd` no **Editor de serviço do aplicativo**, em seguida, selecione **Executar no Console**. A saída do console exibe e termina com `Finished successfully.`

2. Se isso não for concluído com êxito, você precisará abrir o console, navegar até o script e executá-lo usando as etapas a seguir. No **Editor de serviço do aplicativo**, na barra azul superior, selecione o nome do seu bot e selecione **Abrir o Console do Kudu** na lista suspensa.

    ![Abrir o console do Kudu](./media/luis-tutorial-bot-csharp-appinsights/open-kudu-console.png)

3. Na janela da console, insira o seguinte comando:

    ```console
    cd site\wwwroot && build.cmd
    ```

    Aguarde a compilação terminar com `Finished successfully.`

## <a name="test-the-web-app-bot"></a>Testar o bot do aplicativo Web

1. Para testar o bot do aplicativo Web, abra o recurso **Testar no chat da Web** no portal.

2. Insira a frase `Coffee bar on please`.  

    ![Testar o bot do aplicativo Web no chat](./media/luis-tutorial-bot-csharp-appinsights/test-in-web-chat.png)

3. Você não verá nenhuma diferença na resposta do chatbot. A alteração está enviando dados para o Application Insights, não nas respostas do bot. Insira mais algumas declarações para que haja mais dados no Application Insights:

|Declarações|
|--|
|Entregue uma pizza|
|Apague as luzes|
|Acenda a luz do hall|


## <a name="view-luis-entries-in-application-insights"></a>Exibir entradas do LUIS no Application Insights

Abra o Application Insights para ver as entradas do LUIS.

1. No portal, selecione **Todos os recursos** e, em seguida, filtre pelo nome do bot do aplicativo Web. Clique no recurso com o tipo **Application Insights**. O ícone para o Application Insights é uma lâmpada.

    ![Pesquisa para o app insights no portal do Azure](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights.png)

2. Quando o recurso se abrir, clique no ícone **Pesquisar** da lupa no painel bem à direita. Um novo painel à direita é exibido. Dependendo de quantos dados de telemetria forem encontrados, o painel poderá levar um segundo para ser exibido. Procure `LUIS`. A lista é restrita somente aos resultados de consulta do LUIS adicionados com este tutorial.

    ![Pesquisar rastreamentos](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace.png)

3. Selecione a entrada superior. Uma nova janela exibe dados mais detalhados, incluindo os dados personalizados para a consulta do LUIS mais à direita. Os dados incluem a intenção superior e sua pontuação.

    ![Analise o item de rastreamento](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace-item.png)

    Quando terminar, selecione o **X** no canto superior mais à direita para retornar para a lista de itens de dependência.

> [!Tip]
> Se você quiser salvar a lista de dependências e retornar a ela mais tarde, clique em **…Mais** e em **Salvar favorito**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Consulte o Application Insights para intenções, pontuações e enunciados

O Application Insights oferece a capacidade de consultar os dados com a linguagem [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics), além de exportar para [PowerBI](https://powerbi.microsoft.com).

1. Clique em **Análise** na parte superior da lista de dependência acima da caixa de filtro.

    ![Botão Analytics](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-analytics-button.png)

2. Uma nova janela é aberta com uma janela de consulta na parte superior e uma janela de tabela de dados abaixo. Se você tiver usado bancos de dados antes, essa organização será familiar. A consulta inclui todos os itens nas últimas 24 horas começando com o nome `LUIS`. A coluna **CustomDimensions** tem os resultados da consulta do LUIS como pares de nome/valor.

    ![Relatório de análise padrão](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-1.png)

3. Para extrair as principais intenções, pontuações e enunciados, adicione o seguinte logo acima da última linha na janela de consulta:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

4. Execute a consulta. Role bem para a direita na tabela de dados. As novas colunas de topIntent, pontuação e enunciado estão disponíveis. Clique na coluna da intenção superior para classificar.

    ![Relatório de análise personalizado](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-2.png)

Saiba mais sobre a [linguagem de consulta Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) ou [exporte os dados para o PowerBi](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi).

## <a name="learn-more-about-bot-framework"></a>Saiba mais sobre o Bot Framework

Saiba mais sobre o [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Próximas etapas

Outras informações que você talvez queira adicionar aos dados do Application Insights incluem ID do aplicativo, ID de versão, data da última alteração do modelo, data do último treinamento, data da última publicação. Esses valores podem ser recuperados da URL do ponto de extremidade (ID do aplicativo e ID de versão) ou de uma chamada à [API de criação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) e então definidos nas configurações do bot do aplicativo Web e capturados de lá.  

Se você estiver usando a mesma assinatura do ponto de extremidade para mais de um aplicativo LUIS, também deverá incluir a ID da assinatura e uma propriedade informando que ela é uma chave compartilhada.

> [!div class="nextstepaction"]
> [Saiba mais sobre declarações de exemplo](luis-how-to-add-example-utterances.md)
