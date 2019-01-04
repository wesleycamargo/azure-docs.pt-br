---
title: Bot – Node.js – v3
titleSuffix: Azure Cognitive Services
description: Crie um bot integrado ao aplicativo LUIS usando o Bot Framework.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: e73f22a2da9e8b8b3bd70f4a0231ea7932fc7a86
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436935"
---
# <a name="luis-bot-in-nodejs-with-the-bot-framework-3x-and-the-azure-web-app-bot"></a>Bot do LUIS em Node.js com o Bot Framework 3.x e o bot de aplicativo Web do Azure

Usando Node.js, crie um chat bot integrado com reconhecimento vocal (LUIS). Este bot de bate-papo usa o domínio de HomeAutomation predefinido para implementar rapidamente uma solução de bot. O bot é criado com o Bot Framework 3.x e o bot de aplicativo Web do Azure.

## <a name="prerequisite"></a>Pré-requisito

Antes de criar o bot, siga as etapas em [Criar um aplicativo](./luis-get-started-create-app.md) para criar o aplicativo LUIS que ele usa.

O bot responde às intenções do domínio HomeAutomation que estão no aplicativo LUIS. Para cada uma dessas intenções, o aplicativo LUIS fornece uma intenção que o mapeia. O bot fornece uma caixa de diálogo que manipula a intenção detectada pelo LUIS.

| Intenção | Declaração de exemplo | Funcionalidade do bot |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Ligue as luzes. | O bot invoca o `TurnOnDialog` quando o `HomeAutomation.TurnOn` é detectado. Essa caixa de diálogo é onde você invocaria um serviço IoT para ligar um dispositivo e informar ao usuário que o dispositivo foi ligado. |
| HomeAutomation.TurnOff | Desligar as luzes do quarto. | O bot invoca o `TurnOffDialog` quando o `HomeAutomation.TurnOff` é detectado. Essa caixa de diálogo em que você invocaria um serviço IoT para desligar um dispositivo e informar ao usuário que o dispositivo foi desligado. |


## <a name="create-a-language-understanding-bot-with-bot-service"></a>Criar um bot de Reconhecimento vocal com o serviço de bot

1. No [portal do Azure](https://portal.azure.com), selecione **Criar** novo recurso na folha de menu e selecione **Ver tudo**.

    ![Criar novo recurso](./media/luis-tutorial-node-bot/bot-service-creation.png)

2. Na caixa de pesquisa, pesquise **Bot do aplicativo Web**. 

    ![Criar novo recurso](./media/luis-tutorial-node-bot/bot-service-selection.png)

3. Na folha **Serviço de bot**, forneça as informações necessárias e selecione **Criar**. Isso cria e implanta o serviço de bot e o aplicativo LUIS no Azure. Se desejar usar a [preparação da fala](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), examine os [requisitos da região](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming) antes de criar seu bot. 
    * Defina **Nome do aplicativo** como o nome do bot. O nome é usado como o subdomínio quando o bot é implantado na nuvem (por exemplo, mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * Selecione a assinatura, o [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), o plano do serviço de aplicativo e o [local](https://azure.microsoft.com/regions/).
    * Para **Modelo de bot**, selecione:
        * **SDK v3**
        * **Node.js**
        * **Reconhecimento vocal**
    * Selecione o **local do aplicativo LUIS**. Essa é a [região][LUIS] de criação na qual o aplicativo é criado.
    * Marque a caixa de seleção de confirmação para o aviso legal. Os termos do aviso legal estão abaixo da caixa de seleção.

    ![Folha Serviço de bot](./media/luis-tutorial-node-bot/bot-service-setting-callout-template.png)


4. Confirme que o serviço de bot foi implantado.
    * Selecione Notificações (o ícone de sino localizado ao longo da borda superior do portal do Azure). A notificação será alterada de **Implantação iniciada** para **Implantação bem-sucedida**.
    * Depois que a notificação muda para **Implantação bem-sucedida**, selecione **Ir para o recurso** nessa notificação.

## <a name="try-the-default-bot"></a>Experimente o bot padrão

Confirme que o bot foi implantado marcando **Notificações**. As notificações mudarão de **Implantação em andamento...** para **Implantação bem-sucedida**. Selecione o botão **Ir para recurso** para abrir a folha de recursos do bot.

<!-- this step isn't supposed to be necessary -->
## <a name="install-npm-resources"></a>Instalar recursos NPM
Instale os pacotes NPM com as seguintes etapas:

1. Selecione **Criar** na seção **Gerenciamento de bot** do bot do aplicativo Web. 

2. Uma nova e segunda janela do navegador é aberta. Selecione **Abrir editor de código online**.

3. Na barra de navegação superior, selecione o nome do bot do aplicativo Web `homeautomationluisbot`. 

4. Na lista suspensa, selecione **Abrir o console do Kudu**.

5. Uma nova janela do navegador é aberta. No console, insira o seguinte comando:

    ```console
    cd site\wwwroot && npm install
    ```

    Aguarde a conclusão do processo de instalação. Retornar para a primeira janela do navegador. 

## <a name="test-in-web-chat"></a>Testar no Webchat
Depois que o bot está registrado, selecione **Teste no Webchat** para abrir o painel do Webchat. Digite "hello" no Webchat.

  ![Testar o bot no Webchat](./media/luis-tutorial-node-bot/bot-service-web-chat.png)

O bot responde dizendo "Você atingiu a Saudação. Você disse: Olá". Isso confirma que o bot recebeu sua mensagem e a passou para um aplicativo LUIS padrão que ele criou. Este aplicativo LUIS padrão detectou uma intenção de Saudação. Na próxima etapa, você conectará o bot ao aplicativo LUIS criado anteriormente, em vez do aplicativo LUIS padrão.

## <a name="connect-your-luis-app-to-the-bot"></a>Conecte seu aplicativo LUIS ao bot

Abra **Configuração de aplicativo** na primeira janela do navegador e edite o campo **LuisAppId** para conter a ID do aplicativo do seu aplicativo LUIS.

  ![Atualize a ID do aplicativo LUIS no Azure](./media/luis-tutorial-node-bot/bot-service-app-id.png)

Se não tiver a ID do aplicativo LUIS, faça logon no site do [LUIS](luis-reference-regions.md) usando a mesma conta que você usa para fazer logon no Azure. Selecione **Meus aplicativos**. 

1. Localize o aplicativo LUIS que você criou anteriormente, que contém as intenções e entidades do domínio HomeAutomation.

2. Na página **Configurações** do aplicativo LUIS, localize e copie a ID do aplicativo.

3. Se você não treinou o aplicativo, selecione o botão **Treinar** na parte superior direita para treiná-lo.

4. Se você não publicou o aplicativo, selecione **PUBLICAR** na barra de navegação superior para abrir a página **Publicar**. Selecione o slot de produção e o botão **Publicar**.

## <a name="modify-the-bot-code"></a>Modificar o código do bot

Acesse a segunda janela do navegador se ela ainda estiver aberta ou, na primeira janela do navegador, selecione **Criar** e selecione **Abrir editor de código online**.

   ![Abrir editor de código online](./media/luis-tutorial-node-bot/bot-service-build.png)

No editor de código, abra `app.js`. Ele contém o seguinte código:

```nodejs
/*-----------------------------------------------------------------------------
A simple Language Understanding (LUIS) bot for the Microsoft Bot Framework. 
-----------------------------------------------------------------------------*/

var restify = require('restify');
var builder = require('botbuilder');
var botbuilder_azure = require("botbuilder-azure");

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat connector for communicating with the Bot Framework Service
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword,
    openIdMetadata: process.env.BotOpenIdMetadata 
});

// Listen for messages from users 
server.post('/api/messages', connector.listen());

/*----------------------------------------------------------------------------------------
* Bot Storage: This is a great spot to register the private state storage for your bot. 
* We provide adapters for Azure Table, CosmosDb, SQL Azure, or you can implement your own!
* For samples and documentation, see: https://github.com/Microsoft/BotBuilder-Azure
* ---------------------------------------------------------------------------------------- */

var tableName = 'botdata';
var azureTableClient = new botbuilder_azure.AzureTableClient(tableName, process.env['AzureWebJobsStorage']);
var tableStorage = new botbuilder_azure.AzureBotStorage({ gzipData: false }, azureTableClient);

// Create your bot with a function to receive messages from the user
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send('You reached the default message handler. You said \'%s\'.', session.message.text);
});

bot.set('storage', tableStorage);

// Make sure you add code to validate these fields
var luisAppId = process.env.LuisAppId;
var luisAPIKey = process.env.LuisAPIKey;
var luisAPIHostName = process.env.LuisAPIHostName || 'westus.api.cognitive.microsoft.com';

const LuisModelUrl = 'https://' + luisAPIHostName + '/luis/v2.0/apps/' + luisAppId + '?subscription-key=' + luisAPIKey;

// Create a recognizer that gets intents from LUIS, and add it to the bot
var recognizer = new builder.LuisRecognizer(LuisModelUrl);
bot.recognizer(recognizer);

// Add a dialog for each intent that the LUIS app recognizes.
// See https://docs.microsoft.com/bot-framework/nodejs/bot-builder-nodejs-recognize-intent-luis 
bot.dialog('GreetingDialog',
    (session) => {
        session.send('You reached the Greeting intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Greeting'
})

bot.dialog('HelpDialog',
    (session) => {
        session.send('You reached the Help intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Help'
})

bot.dialog('CancelDialog',
    (session) => {
        session.send('You reached the Cancel intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Cancel'
})
```

As intenções existentes no app.js são ignoradas. É possível deixá-las como estão. 

## <a name="add-a-dialog-that-matches-homeautomationturnon"></a>Adicionar uma caixa de diálogo que corresponde a HomeAutomation.TurnOn

Copie o código a seguir e adicione-o ao `app.js`.

```nodejs
bot.dialog('TurnOn',
    (session) => {
        session.send('You reached the TurnOn intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOn'
})
```

A opção [corresponde a][matches] no [triggerAction][triggerAction] anexado à caixa de diálogo especifica o nome da intenção. O reconhecedor executa cada vez que o bot recebe uma declaração do usuário. Se a intenção da mais alta pontuação que ele detecta corresponder a um `triggerAction` associado a uma caixa de diálogo, o bot invocará essa caixa de diálogo.

## <a name="add-a-dialog-that-matches-homeautomationturnoff"></a>Adicionar uma caixa de diálogo que corresponde a HomeAutomation.TurnOff

Copie o código a seguir e adicione-o ao `app.js`.

```nodejs
bot.dialog('TurnOff',
    (session) => {
        session.send('You reached the TurnOff intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOff'
})
```
## <a name="test-the-bot"></a>Testar o bot

No portal do Azure, selecione **Testar no Webchat** para testar o bot. Experimente digitar mensagens como "Ligue as luzes" e "desligue meu aquecedor" para invocar as intenções que você adicionou a ele.
   ![Testar o bot HomeAutomation no Webchat](./media/luis-tutorial-node-bot/bot-service-chat-results.png)

> [!TIP]
> Se você achar que seu bot nem sempre reconhece a intenção ou entidades corretas, melhore o desempenho do seu aplicativo LUIS dando mais declarações de exemplo para treiná-lo. É possível treinar novamente seu aplicativo LUIS sem nenhuma modificação no código do seu bot. Confira [Add example utterances](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) (Adicionar declarações de exemplo) e [treinar e testar seu aplicativo LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test).

## <a name="learn-more-about-bot-framework"></a>Saiba mais sobre o Bot Framework
Saiba mais sobre o [Bot Framework](https://dev.botframework.com/) e os SDKs [3.x](https://github.com/Microsoft/BotBuilder) e [4.x](https://github.com/Microsoft/botbuilder-js).

## <a name="next-steps"></a>Próximas etapas

<!-- From trying the bot, you can see that the recognizer can trigger interruption of the currently active dialog. Allowing and handling interruptions is a flexible design that accounts for what users really do. Learn more about the various actions you can associate with a recognized intent.--> É possível tentar adicionar outras intenções, como Ajudar, Cancelar e Saudação ao aplicativo LUIS. Depois, adicione caixas de diálogo para as novas intenções e teste-as usando o bot. 

<!-- 
> [!NOTE] 
> The default LUIS app that the template created contains example utterances for Cancel, Greeting, and Help intents. In the list of apps, find the app that begins with the name specified in **App name** in the **Bot Service** blade when you created the Bot Service. -->

> [!div class="nextstepaction"]
> [Adicionar intenções](./luis-how-to-add-intents.md)
> [Preparação da fala](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


[intentDialog]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html

[intentDialog_matches]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html#matches 

[NotesSample]: https://github.com/Microsoft/BotFramework-Samples/tree/master/docs-samples/Node/basics-naturalLanguage

[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[confirmPrompt]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#confirmprompt

[waterfall]: bot-builder-nodejs-dialog-manage-conversation-flow.md#manage-conversation-flow-with-a-waterfall

[session_userData]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html#userdata

[EntityRecognizer_findEntity]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#findentity

[matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#matches

[LUISAzureDocs]: https://docs.microsoft.com/azure/cognitive-services/LUIS/Home

[Dialog]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html

[IntentRecognizerSetOptions]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentrecognizersetoptions.html

[LuisRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer

[LUISConcepts]: https://docs.botframework.com/node/builder/guides/understanding-natural-language/

[DisambiguationSample]: https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/feature-onDisambiguateRoute

[IDisambiguateRouteHandler]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.idisambiguateroutehandler.html

[RegExpRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.regexprecognizer.html

[AlarmBot]: https://github.com/Microsoft/BotBuilder/blob/master/Node/examples/basics-naturalLanguage/app.js

[LUISBotSample]: https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/intelligence-LUIS

[UniversalBot]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.universalbot.html


<!-- Old Links -->
[GitHub-BotFramework-Emulator-Download]: https://aka.ms/bot-framework-emulator
[GitHub-LUIS-Samples]: https://github.com/Microsoft/LUIS-Samples
[GitHub-LUIS-Samples-node-hotel-bot]: https://github.com/Microsoft/LUIS-Samples/tree/master/bot-integration-samples/hotel-finder/nodejs
[NodeJs]: https://nodejs.org/
[BFPortal]: https://dev.botframework.com/
[RegisterInstructions]: https://docs.microsoft.com/bot-framework/portal-register-bot
[BotFramework]: https://docs.microsoft.com/bot-framework/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions

