---
title: Bot do QnA – Serviço de Bot do Azure – QnA Maker
titleSuffix: Azure Cognitive Services
description: Este tutorial orienta você na criação de um bot do QnA com o Serviço de Bot do Azure v3 no portal do Azure.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker`
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.openlocfilehash: 930d2d7279f23974e937fd0d858891d0bcaa1291
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869153"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v3"></a>Tutorial: Criar um Bot do QnA com o Serviço de Bot do Azure v3

Este tutorial orienta você na criação de um bot do QnA com o Serviço de Bot do Azure v3 no [portal do Azure](https://portal.azure.com) sem escrever qualquer código. Conectar uma base de dados de conhecimento (KB) publicada a um bot é tão simples quanto alterar as configurações de aplicativo do bot. 

> [!Note] 
> Este tópico serve para a versão 3 do SDK do Bot. Encontre a versão 4 [aqui](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs). 

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar um Serviço de Bot do Azure com o modelo do QnA Maker
> * Conversar com o bot para verificar se o código está funcionando 
> * Conectar seu KB publicado ao bot
> * Testar o bot com uma pergunta

Neste artigo, você pode usar o [serviço](../how-to/set-up-qnamaker-service-azure.md) gratuito do QnA Maker.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa ter uma base de dados de conhecimento publicada para este tutorial. Se não tiver uma, execute as etapas em [Criar uma base de dados de conhecimento](../How-To/create-knowledge-base.md) para criar um serviço do QnA Maker com perguntas e respostas.

## <a name="create-a-qna-bot"></a>Criar um bot de QnA

1. No Portal do Azure, selecione **Criar um recurso**.

    ![criação do serviço de bot](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. Na caixa Pesquisar, procure **Bot do aplicativo Web**.

    ![seleção do serviço de bot](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. Em **Serviço de bot**, forneça as informações necessárias:

    - Defina **Nome do aplicativo** como o nome do bot. O nome é usado como o subdomínio quando o bot é implantado na nuvem (por exemplo, mynotesbot.azurewebsites.net).
    - Selecione a assinatura, grupo de recursos, plano de serviço de aplicativo e local.

4. Para usar os modelos v3, selecione a versão **SDK v3** e a linguagem do SDK **C#** ou **Node.js**.

    ![configurações do SDK do bot](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. Selecione o modelo **Pergunta e Resposta** para o campo Modelo de bot e, em seguida, salve as configurações do modelo escolhendo **Selecionar**.

    ![salvar seleção de modelo de serviço de bot](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. Examine as configurações e selecione **Criar**. Isso cria e implanta o serviço de bot no Azure.

    ![Crie o bot](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. Confirme que o serviço de bot foi implantado.

    - Selecione **Notificações** (o ícone de sino que fica ao longo da borda superior do portal do Azure). A notificação será alterada de **Implantação iniciada** para **Implantação bem-sucedida**.
    - Depois que a notificação muda para **Implantação bem-sucedida**, selecione **Ir para o recurso** nessa notificação.

## <a name="chat-with-the-bot"></a>Conversar com o bot

Selecionar **Ir para o recurso** leva você até o recurso do bot.

Selecionar **Testar no Webchat** para abrir o painel do Webchat. Digite "oi" no Web Chat.

![chat da Web do bot do QnA](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

O bot responde com "Defina QnAKnowledgebaseId e QnASubscriptionKey nas Configurações do aplicativo. Essa resposta confirma que o bot do QnA recebeu a mensagem, mas não há nenhuma base de dados de conhecimento do QnA Maker associada a ele ainda. 

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>Conecte a sua base de dados de conhecimento do QnA Maker ao bot

1. Abra **Configurações do aplicativo** e edite os campos **QnAKnowledgebaseId**, **QnAAuthKey** e **QnAEndpointHostName** para conter os valores de sua base de dados de conhecimento do QnA Maker.

    ![configurações do aplicativo](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

1. Obtenha a ID da sua base de dados de conhecimento, a URL do host e a chave do ponto de extremidade da guia de configurações de sua base de dados de conhecimento no portal do QnA Maker.

    - Entre no [QnA Maker](https://qnamaker.ai)
    - Vá até a sua base de dados de conhecimento
    - Selecione a guia **Configurações**
    - **Publique** sua base de dados de conhecimento, se ainda não tiver feito isso

    ![Valores do QnA Maker](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

## <a name="test-the-bot"></a>Testar o bot

No portal do Azure, selecione **Testar no Webchat** para testar o bot. 

![Bot do QnA Maker](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

Seu bot do QnA responde de sua base de dados de conhecimento.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar com o bot deste tutorial, remova-o no portal do Azure. Os serviços do bot incluem:

* O plano do Serviço de Aplicativo
* O serviço Search
* O serviços Cognitivos
* O serviço de Aplicativo
* Opcionalmente, também pode incluir o serviço application insights e o armazenamento para os dados do application insights

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conceito: base de dados de conhecimento](../concepts/knowledge-base.md)

## <a name="see-also"></a>Consulte também

- [Gerencie sua base de dados de conhecimento](https://qnamaker.ai)
- [Habilite o bot em canais diferentes](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
