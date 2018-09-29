---
title: Bot de LUIS com C# - Bot de aplicativo Web - SDK de Bot Framework 3.0
titleSuffix: Azure Cognitive Services
description: Usando C#, crie um bot de bate-papo integrado com reconhecimento vocal (LUIS). Este bot de bate-papo usa o domínio de HomeAutomation predefinido para implementar rapidamente uma solução de bot.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 0ad95d5683ea10803c2f3986be97617051f191cf
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032267"
---
# <a name="luis-bot-in-c"></a>Bot de LUIS em C#

Usando C#, crie um bot de bate-papo integrado com reconhecimento vocal (LUIS). Este bot de bate-papo usa o domínio de HomeAutomation predefinido para implementar rapidamente uma solução de bot. O bot é criado com o Bot Framework 3.x e o bot de aplicativo Web do Azure.

## <a name="prerequisite"></a>Pré-requisito

* [Aplicativo LUIS de HomeAutomation](luis-get-started-create-app.md). As intenções do aplicativo LUIS são mapeadas para os manipuladores de diálogo do bot. 

## <a name="luis-homeautomation-intents"></a>Intenções HomeAutomation do LUIS

| Intenção | Declaração de exemplo | Funcionalidade do bot |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Ligue as luzes. | Quando a intenção do LUIS `HomeAutomation.TurnOn` for detectada, o bot invocará o manipulador da caixa de diálogo `OnIntent`. Essa caixa de diálogo é onde você chamaria um serviço IoT para ligar um dispositivo e informar ao usuário que o dispositivo foi ligado. |
| HomeAutomation.TurnOff | Desligar as luzes do quarto. | Quando a intenção do LUIS `HomeAutomation.TurnOff` for detectada, o bot invocará o manipulador da caixa de diálogo `OffIntent`. Essa caixa de diálogo é onde você chamaria um serviço IoT para desligar um dispositivo e informar ao usuário que o dispositivo foi desligado. |

## <a name="create-a-language-understanding-bot-with-bot-service"></a>Criar um bot de Reconhecimento vocal com o serviço de bot

1. No [portal do Azure](https://portal.azure.com), selecione **Criar novo recurso** no menu superior esquerdo.

    ![Criar novo recurso](./media/luis-tutorial-cscharp-web-bot/bot-service-creation.png)

2. Na caixa de pesquisa, pesquise **Bot do aplicativo Web**. 

    ![Criar novo recurso](./media/luis-tutorial-cscharp-web-bot/bot-service-selection.png)

3. Na janela Bot do aplicativo Web, clique em **Criar**.

4. Em **Serviço de bot**, forneça as informações necessárias e clique em **Criar**. Isso cria e implanta o serviço de bot e o aplicativo LUIS no Azure. Se desejar usar a [preparação da fala](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), examine os [requisitos da região](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming) antes de criar seu bot. 
    * Defina **Nome do aplicativo** como o nome do bot. O nome é usado como o subdomínio quando o bot é implantado na nuvem (por exemplo, mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * Selecione a assinatura, o [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), o plano do serviço de aplicativo e o [local](https://azure.microsoft.com/regions/).
    * Para **Modelo de bot**, selecione:
        * **SDK v3**
        * **C#**
        * **Reconhecimento vocal**
    * Selecione o **local do aplicativo LUIS**. Essa é a [região](luis-reference-regions.md) de criação na qual o aplicativo é criado.
    * Marque a caixa de seleção de confirmação para o aviso legal. Os termos do aviso legal estão abaixo da caixa de seleção.

    ![Serviço de Bot](./media/luis-tutorial-cscharp-web-bot/bot-service-setting-callout-template.png)


5. Confirme que o serviço de bot foi implantado.
    * Clique em Notificações (o ícone de sino que fica ao longo da borda superior do portal do Azure). A notificação é alterada de **Implantação iniciada** para **Implantação bem-sucedida**.
    * Depois que a notificação mudar para **Implantação bem-sucedida**, clique em **Ir para o recurso** nessa notificação.

> [!Note]
> O processo de criação desse bot do aplicativo Web também criou um novo aplicativo LUIS para você. Ele foi treinado e publicado para você. 

## <a name="try-the-default-bot"></a>Experimente o bot padrão

Confirme que o bot foi implantado marcando a caixa de seleção **Notificações**. A notificação muda de **Implantação em andamento...** para **Implantação bem-sucedida**. Clique no botão **Ir para recurso** para abrir os recursos do bot.

Depois que o bot for implantado, clique em **Testar no Webchat** para abrir o painel do Webchat. Digite "hello" no Webchat.

  ![Testar o bot no Webchat](./media/luis-tutorial-cscharp-web-bot/bot-service-web-chat.png)

O bot responde dizendo "Você atingiu a Saudação. Você disse: Olá".  Esta resposta confirma que o bot recebeu sua mensagem e a passou para um aplicativo LUIS padrão que ele criou. Este aplicativo LUIS padrão detectou uma intenção de Saudação. Na próxima etapa, você conectará o bot ao aplicativo LUIS criado anteriormente, em vez do aplicativo LUIS padrão.

## <a name="connect-your-luis-app-to-the-bot"></a>Conecte seu aplicativo LUIS ao bot

Abra **Configuração de aplicativo** e edite o campo **LuisAppId** para conter a ID do aplicativo do seu aplicativo LUIS. Se você criou seu aplicativo LUIS HomeAutomation em uma região que não seja o Oeste dos EUA, é necessário alterar o **LuisAPIHostName** também. No momento, **LuisAPIKey** está definido como sua chave de criação. Altere isso para a chave de ponto de extremidade quando o tráfego exceder a cota da camada gratuita. 

  ![Atualize a ID do aplicativo LUIS no Azure](./media/luis-tutorial-cscharp-web-bot/bot-service-app-settings.png)

> [!Note]
> Se não tiver a ID do aplicativo LUIS do [aplicativo de Automação Residencial](luis-get-started-create-app.md), faça logon no site do [LUIS](luis-reference-regions.md) usando a mesma conta que você usa para fazer logon no Azure. 
> 1. Clique em **Meus aplicativos**. 
> 2. Localize o aplicativo LUIS que você criou anteriormente, que contém as intenções e entidades do domínio HomeAutomation.
> 3. Na página **Configurações** do aplicativo LUIS, localize e copie a ID do aplicativo. Verifique se ele está [treinado](luis-interactive-test.md) e se foi [publicado](luis-how-to-publish-app.md). 

    > [!WARNING]
    > If you delete your app ID or LUIS key, the bot will stop working.

## <a name="modify-the-bot-code"></a>Modificar o código do bot

1. Clique em **Criar** e, em seguida, clique em **Abrir editor de código online**.

   ![Abrir editor de código online](./media/luis-tutorial-cscharp-web-bot/bot-service-build.png)

2. Clique com o botão direito do mouse em `build.cmd` e escolha **Executar no console** para criar o aplicativo. Há várias etapas de build que o serviço completa automaticamente para você. O build está completo quando é concluído com "Concluído com êxito".

3. No editor de código, abra `/Dialogs/BasicLuisDialog.cs`. Ele contém o seguinte código:

   [!code-csharp[Default BasicLuisDialog.cs](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/Default_BasicLuisDialog.cs "Default BasicLuisDialog.cs")]

## <a name="change-code-to-homeautomation-intents"></a>Altere o código para intenções HomeAutomation


1. Remova os três atributos e métodos de intenções para **Saudação**, **Cancelar** e **Ajuda**. Essas intenções não são usadas no domínio predefinido HomeAutomation. Certifique-se de manter o atributo e o método de intenção **None**. 

2. Adicione dependências à parte superior do arquivo, com as outras dependências:

   [!code-csharp[Dependencies](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=4-5&dedent=8 "dependencies")]

3. Adicione constantes para gerenciar cadeia de caracteres na parte superior da classe `BasicLuisDialog `:

   [!code-csharp[Add Intent and Entity Constants](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=23-32&dedent=8 "Add Intent and Entity Constants")]

4. Adicione o código para as novas intenções de `HomeAutomation.TurnOn` e `HomeAutomation.TurnOff` dentro da classe `BasicLuisDialog `:

   [!code-csharp[Add Intents](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=61-71&dedent=8 "Add Intents")]

5. Adicione o código para obter entidades encontradas pelo LUIS dentro da classe `BasicLuisDialog `:

   [!code-csharp[Collect entities](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=34-53&dedent=8 "Collect entities")]

6. Altere o método **ShowLuisResult** na classe `BasicLuisDialog ` para arredondar a pontuação, coletar as entidades e exibir a mensagem de resposta no chatbot:

   [!code-csharp[Display message in chatbot](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=73-83&dedent=8 "Display message in chatbot")]

## <a name="build-the-bot"></a>Criar o bot
No editor de código, clique com o botão direito do mouse em `build.cmd` e selecione **Executar no Console**.

![Criar bot da Web ](./media/luis-tutorial-cscharp-web-bot/bot-service-build-run-from-console.png)

O modo de exibição de código é substituído por uma janela de terminal mostrando o andamento e os resultados da compilação.

![Criar êxito do bot da Web](./media/luis-tutorial-cscharp-web-bot/bot-service-build-success.png)

> [!TIP]
> Um método alternativo para criar o bot é selecionar seu nome na barra superior azul e selecionar **Abrir console Kudu**. O console é aberto em **D:\home**. 
> 
> Altere o diretório para **D:\home\site\wwwroot** digitando: `cd site\wwwroot`
>
> Execute o script de build digitando: `build.cmd`

## <a name="test-the-bot"></a>Testar o bot

No portal do Azure, clique em **Testar no Webchat** para testar o bot. Digite mensagens como "Ligue as luzes" e "desligue meu aquecedor" para invocar as intenções que você adicionou a ele.

   ![Testar o bot HomeAutomation no Webchat](./media/luis-tutorial-cscharp-web-bot/bot-service-chat-results.png)

> [!TIP]
> É possível treinar novamente seu aplicativo LUIS sem nenhuma modificação no código do seu bot. Confira [Add example utterances](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) (Adicionar declarações de exemplo) e [treinar e testar seu aplicativo LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test). 

## <a name="download-the-bot-to-debug"></a>Baixar o bot para depurar
Se o bot não estiver funcionando, baixe o projeto para seu computador local e continue com a [depuração](https://docs.microsoft.com/bot-framework/bot-service-debug-bot#debug-a-c-bot). 

## <a name="learn-more-about-bot-framework"></a>Saiba mais sobre o Bot Framework
Saiba mais sobre o [Bot Framework](https://dev.botframework.com/) e os SDKs [3.x](https://github.com/Microsoft/BotBuilder) e [4.x](https://github.com/Microsoft/botbuilder-dotnet).

## <a name="next-steps"></a>Próximas etapas

Adicione as intenções do LUIS e as caixas de diálogo de serviço do Bot para manipular as intenções **Ajuda**, **Cancelar** e **Saudação**. Lembre-se de treinar, publicar e [criar](#build-the-bot) o bot do aplicativo Web. O LUIS e o bot devem ter as mesmas intenções.

> [!div class="nextstepaction"]
> [Adicionar intenções](./luis-how-to-add-intents.md)
> [Preparação da fala](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


<!-- Links -->
[Github-BotFramework-Emulator-Download]: https://aka.ms/bot-framework-emulator
[Github-LUIS-Samples]: https://github.com/Microsoft/LUIS-Samples
[Github-LUIS-Samples-cs-hotel-bot]: https://github.com/Microsoft/LUIS-Samples/tree/master/bot-integration-samples/hotel-finder/csharp
[Github-LUIS-Samples-cs-hotel-bot-readme]: https://github.com/Microsoft/LUIS-Samples/blob/master/bot-integration-samples/hotel-finder/csharp/README.md
[BFPortal]: https://dev.botframework.com/
[RegisterInstructions]: https://docs.microsoft.com/bot-framework/portal-register-bot
[BotFramework]: https://docs.microsoft.com/bot-framework/
[VisualStudio]: https://www.visualstudio.com/

<!-- tested on Win10 -->
