---
title: Bot de LUIS com C# – Tutorial – Bot de aplicativo Web – SDK de Bot Framework 4.0
titleSuffix: Azure Cognitive Services
description: Usando C#, crie um bot de bate-papo integrado com reconhecimento vocal (LUIS). Este chat bot usa o aplicativo de Recursos Humanos para implementar rapidamente uma solução de bot. O bot é criado com o Bot Framework versão 4 e o bot de aplicativo Web do Azure.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/25/2018
ms.author: diberry
ms.openlocfilehash: f8350d46fecff726dd9f591fe3df0272f556b3e7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47168089"
---
# <a name="tutorial-luis-bot-in-c"></a>Tutorial: bot de LUIS em C#
Usando o C#, é possível criar um chat bot integrado com LUIS (reconhecimento vocal). Este bot usa o aplicativo HomeAutomation para implementar uma solução de bot. O bot é criado o [bot do aplicativo Web](https://docs.microsoft.com/azure/bot-service/) do Azure com o [Bot Framework versão](https://github.com/Microsoft/botbuilder-js) v4.

**Neste tutorial, você aprenderá como:**

> [!div class="checklist"]
> * Criar um bot de aplicativo Web. Esse processo cria um aplicativo LUIS para você.
> * Adicionar um domínio predefinido ao novo modelo do LUIS
> * Baixar o projeto criado pelo serviço de bot da Web
> * Iniciar o bot e o emulador localmente em seu computador
> * Modificar o código do bot para novas intenções do LUIS
> * Exibir resultados de expressão no bot

## <a name="prerequisites"></a>Pré-requisitos

* [Emulador de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-web-app-bot"></a>Criar bot de aplicativo Web

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar novo recurso**.

2. Na caixa de pesquisa, pesquise e selecione **Bot do aplicativo Web**. Selecione **Criar**.

3. Em **Serviço de bot**, forneça as informações necessárias:

    |Configuração|Finalidade|Configuração sugerida|
    |--|--|--|
    |Nome do bot|Nome do recurso|`luis-csharp-bot-` + `<your-name>`, por exemplo, `luis-csharp-bot-johnsmith`|
    |Assinatura|Assinatura na qual criar o bot.|Sua assinatura primária.
    |Grupo de recursos|Grupo lógico de recursos do Azure|Crie um grupo para armazenar todos os recursos usados com este bot, dê um nome ao grupo `luis-csharp-bot-resource-group`.|
    |Local padrão|Região do Azure – não precisa ser a mesmo que a região de publicação ou criação do LUIS.|`westus`|
    |Tipo de preço|Usado para os limites e a cobrança da solicitação de serviço.|`F0` é o tipo gratuito.
    |Nome do aplicativo|O nome é usado como o subdomínio quando seu bot é implantado na nuvem (por exemplo, humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, por exemplo, `luis-csharp-bot-johnsmith`|
    |Modelo de bot|Configurações do Bot framework – consulte a próxima tabela|
    |Local do aplicativo LUIS|Deve ser o mesmo que a região do recurso do LUIS|`westus`|

4. Nas **Configurações de modelo de bot**, selecione o seguinte e escolha o botão **Selecionar** nessas configurações:

    |Configuração|Finalidade|Seleção|
    |--|--|--|
    |Versão do SDK|Versão do Bot framework|**SDK v4**|
    |Linguagem do SDK|Linguagem de programação do bot|**C#**|
    |Bot de eco/básico|Tipo de bot|**Bot básico**|
    
5. Selecione **Criar**. Isso cria e implanta o serviço de bot no Azure. Parte deste processo cria um aplicativo LUIS chamado `luis-csharp-bot-XXXX`. Esse nome é baseado no nome do bot e do aplicativo na seção anterior.

    [ ![Criar bot de aplicativo Web](./media/bfv4-csharp/create-web-app-service.png) ](./media/bfv4-csharp/create-web-app-service.png#lightbox)

6. Deixe esta guia do navegador aberta. Para quaisquer etapas com o portal do LUIS, abra uma nova guia do navegador. Passe para a próxima seção quando o novo serviço de bot for implantado.

## <a name="add-prebuilt-domain-to-model"></a>Adicionar domínio predefinido ao modelo
Parte da implantação do serviço de bot cria um aplicativo LUIS com intenções e declarações de exemplo. O bot fornece mapeamento de intenções para o novo aplicativo LUIS para as seguintes intenções: 

|Intenções do LUIS do bot básico|exemplo de enunciado|
|--|--|
|Cancelar|`stop`|
|Saudação|`hello`|
|Ajuda|`help`|
|Nenhum|Nada fora do domínio do aplicativo.|

Adicione o aplicativo HomeAutomation predefinido ao modelo para lidar com declarações como: `Turn off the living room lights`

1. Acesse o portal do [LUIS](https://www.luis.ai) e entre.
2. Na página **Meus aplicativos**, selecione a coluna **Data de Criação** para classificar pela data em que o aplicativo foi criado. O serviço de bot do Azure criou um aplicativo na seção anterior. Seu nome é `luis-csharp-bot-` + `<your-name>` + 4 caracteres aleatórios.
3. Abra o aplicativo e selecione a seção **Build** na navegação superior.
4. Na navegação esquerda, selecione **Domínios Predefinidos**.
5. Selecione o domínio **HomeAutomation** escolhendo **Adicionar domínio** em seu cartão.
6. Selecione **Treinar** no menu superior direito.
7. Selecione **Publicar** no menu superior direito. 

    O aplicativo criado pelo serviço de Bot do Azure agora tem novas intenções:

    |Novas intenções de bot básico|exemplo de enunciado|
    |--|--|
    |HomeAutomation.TurnOn|`turn the fan to high`
    |HomeAutomation.TurnOff|`turn off ac please`|

## <a name="download-the-web-app-bot"></a>Baixe o bot do aplicativo Web 
Para desenvolver o código de bot do aplicativo Web, baixe o código e use em seu computador local. 

1. No portal do Azure, ainda no recurso de bot do aplicativo Web, selecione as **Configurações do Aplicativo** e copie os valores de **botFilePath** e **botFileSecret**. É necessário adicioná-los a um arquivo de ambiente mais tarde. 

2. No portal do Azure, selecione **Build** na seção **Gerenciamento de bot**. 

3. Selecione **Baixar código-fonte do bot**. 

    [ ![Baixar o código-fonte do bot do aplicativo Web para bot básico](../../../includes/media/cognitive-services-luis/bfv4/download-code.png) ](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

4. Quando o código-fonte for compactado, uma mensagem fornecerá um link para baixar o código. Selecione o link. 

5. Salve o arquivo zip em seu computador local e extraia os arquivos. Abra o projeto. 

6. Abra o arquivo bot.cs e procure `_services.LuisServices`. É aí que a declaração do usuário inserida no bot é enviada para o LUIS.

    ```csharp
    /// <summary>
    /// Run every turn of the conversation. Handles orchestration of messages.
    /// </summary>
    /// <param name="turnContext">Bot Turn Context.</param>
    /// <param name="cancellationToken">Task CancellationToken.</param>
    /// <returns>A <see cref="Task"/> representing the asynchronous operation.</returns>
    public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    {
        var activity = turnContext.Activity;

        if (activity.Type == ActivityTypes.Message)
        {
            // Perform a call to LUIS to retrieve results for the current activity message.
            var luisResults = await _services.LuisServices[LuisConfiguration].RecognizeAsync(turnContext, cancellationToken).ConfigureAwait(false);

            // If any entities were updated, treat as interruption.
            // For example, "no my name is tony" will manifest as an update of the name to be "tony".
            var topScoringIntent = luisResults?.GetTopScoringIntent();

            var topIntent = topScoringIntent.Value.intent;
            switch (topIntent)
            {
                case GreetingIntent:
                    await turnContext.SendActivityAsync("Hello.");
                    break;
                case HelpIntent:
                    await turnContext.SendActivityAsync("Let me try to provide some help.");
                    await turnContext.SendActivityAsync("I understand greetings, being asked for help, or being asked to cancel what I am doing.");
                    break;
                case CancelIntent:
                    await turnContext.SendActivityAsync("I have nothing to cancel.");
                    break;
                case NoneIntent:
                default:
                    // Help or no intent identified, either way, let's provide some help.
                    // to the user
                    await turnContext.SendActivityAsync("I didn't understand what you just said to me.");
                    break;
            }
        }
        else if (activity.Type == ActivityTypes.ConversationUpdate)
        {
            if (activity.MembersAdded.Any())
            {
                // Iterate over all new members added to the conversation.
                foreach (var member in activity.MembersAdded)
                {
                    // Greet anyone that was not the target (recipient) of this message.
                    // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
                    if (member.Id != activity.Recipient.Id)
                    {
                        var welcomeCard = CreateAdaptiveCardAttachment();
                        var response = CreateResponse(activity, welcomeCard);
                        await turnContext.SendActivityAsync(response).ConfigureAwait(false);
                    }
                }
            }
        }

    }
    ```

    O bot envia a declaração do usuário para o LUIS e obtém os resultados. A intenção superior determina o fluxo da conversa. 


## <a name="start-the-bot"></a>Iniciar o bot
Antes de alterar qualquer código ou as configurações, verifique se o bot funciona. 

1. No Visual Studio, abra o arquivo da solução. 

2. Crie um arquivo `appsettings.json` para armazenar as variáveis de bot que o código de bot procura:

    ```JSON
    {
    "botFileSecret": "",
    "botFilePath": ""

    }
    ```

    Defina os valores das variáveis como os valores copiados das Configurações do Aplicativo do serviço de bot do Azure na Etapa 1 da seção **[Baixar o bot do aplicativo Web](#download-the-web-app-bot)**.

3. No Visual Studio, inicie o bot. Uma janela do navegador é aberta com o site do bot do aplicativo Web em `http://localhost:3978/`.

## <a name="start-the-emulator"></a>Iniciar o emulador

1. Inicie o emulador de bot.

2. No emulador de bot, selecione o arquivo *.bot na raiz do projeto. Este arquivo `.bot` inclui o ponto de extremidade de URL do bot para mensagens:

    [ ![Emulador de bot v4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png) ](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. Insira o segredo do bot copiado das Configurações do Aplicativo do serviço de bot do Azure na Etapa 1 da seção **[Baixar o bot do aplicativo Web](#download-the-web-app-bot)**. Isso permite que o emulador acesse quaisquer campos criptografados no arquivo `.bot`.

    ![Segredo do emulador de bot v4](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)

4. No emulador de bot, insira `Hello` e obtenha a resposta adequada do bot básico.

    [ ![Resposta do bot básico no emulador](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png) ](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>Modificar o código de bot 

No arquivo `BasicBot.cs`, adicione um código para lidar com as novas intenções. 

1. Na parte superior do arquivo, localize a seção **Intenções do LUIS com suporte** e adicione constantes para as intenções HomeAutomation:

    ```csharp
    // Supported LUIS Intents
    public const string GreetingIntent = "Greeting";
    public const string CancelIntent = "Cancel";
    public const string HelpIntent = "Help";
    public const string NoneIntent = "None";
    public const string TurnOnIntent = "HomeAutomation_TurnOn"; // new intent
    public const string TurnOffIntent = "HomeAutomation_TurnOff"; // new intent
    ```

    Observe que o período, `.`, entre o domínio e a intenção do aplicativo do portal do LUIS é substituído por um sublinhado, `_`. 

2. Localize o método **OnTurnAsync** que recebe a previsão do LUIS da declaração. Adicione o código na declaração de opção para retornar a resposta do LUIS para as duas intenções HomeAutomation. 

    ```csharp
    case TurnOnIntent:
        await turnContext.SendActivityAsync("TurnOn intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    case TurnOffIntent:
        await turnContext.SendActivityAsync("TurnOff intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    ```

    O bot não tem a mesma resposta exata que a solicitação de API REST do LUIS; portanto, é importante conhecer as diferenças examinando o JSON de resposta. As propriedades de texto e de intenções são as mesmas, mas os valores de propriedade de entidades foram modificados. 

    ```JSON
    {
        "$instance": {
            "HomeAutomation_Device": [
                {
                    "startIndex": 23,
                    "endIndex": 29,
                    "score": 0.9776345,
                    "text": "lights",
                    "type": "HomeAutomation.Device"
                }
            ],
            "HomeAutomation_Room": [
                {
                    "startIndex": 12,
                    "endIndex": 22,
                    "score": 0.9079433,
                    "text": "livingroom",
                    "type": "HomeAutomation.Room"
                }
            ]
        },
        "HomeAutomation_Device": [
            "lights"
        ],
        "HomeAutomation_Room": [
            "livingroom"
        ]
    }
    ```



## <a name="view-results-in-bot"></a>Exibir resultados no bot

1. No emulador de bot, insira a declaração: `Turn on the livingroom lights to 50%`

2. O bot responde com:

    ```JSON
    TurnOn intent found, JSON response: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```    ```

## Learn more about Bot Framework
Azure Bot service uses the Bot Framework SDK. Learn more about the SDK and bot framework:

* [Azure Bot Service](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4 documentation
* [Bot Builder Samples](https://github.com/Microsoft/botbuilder-samples)
* [Bot Builder SDK](https://docs.microsoft.com/en-us/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Bot Builder tools](https://github.com/Microsoft/botbuilder-tools):

## Next steps

You created an Azure bot service, copied the bot secret and `.bot` file path, downloaded the zip file of the code. You added the prebuilt HomeAutomation domain to the LUIS app created as part of the new Azure bot service, then trained and published the app again. You extracted the code project, created an environment file (`.env`), and set the bot secret and the `.bot` file path. In the bot.js file, you added code to handle the two new intents. Then you tested the bot in the bot emulator to see the LUIS response for an utterance of one of the new intents. 


> [!div class="nextstepaction"]
> [Build a custom domain in LUIS](luis-quickstart-intents-only.md)
