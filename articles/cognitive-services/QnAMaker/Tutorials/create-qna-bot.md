---
title: Criar um bot de QnA com o Serviço de Bot do Azure – Serviços Cognitivos do Azure | Microsoft Docs
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: fc430bf3aa7cad279d7a93bb6892aa19abee3378
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109262"
---
# <a name="create-a-qna-bot-with-azure-bot-service"></a>Criar um bot de QnA com o Serviço de Bot do Azure
Este tutorial orienta você na criação de um bot de QnA com o Serviço de Bot do Azure no portal do Azure.

## <a name="prerequisite"></a>Pré-requisito
Antes de criar, siga as etapas em [Criar uma base de dados de conhecimento](../How-To/create-knowledge-base.md) para criar um serviço de QnA Maker com perguntas e respostas.

O bot responde às perguntas da base de dados de conhecimento que você criou, por meio do QnAMakerDialog.

## <a name="create-a-qna-bot"></a>Criar um bot de QnA
1. No [portal do Azure](https://portal.azure.com), selecione **Criar** novo recurso na folha de menu e selecione **Ver tudo**.

    ![criação do serviço de bot](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. Na caixa Pesquisar, procure **Bot do aplicativo Web**.

    ![seleção do serviço de bot](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. Na **Folha Serviço de bot**, forneça as informações necessárias e selecione **Criar**. Isso cria e implanta o serviço de bot com QnAMakerDialog no Azure.

    - Defina **Nome do aplicativo** como o nome do bot. O nome é usado como o subdomínio quando o bot é implantado na nuvem (por exemplo, mynotesbot.azurewebsites.net).
    - Selecione a assinatura, grupo de recursos, plano de serviço de aplicativo e local.
    - Selecione o modelo **Pergunta e resposta** (Node.js ou C#) para o campo de modelo de bot.
    - Marque a caixa de seleção de confirmação para o aviso legal. Os termos do aviso legal estão abaixo da caixa de seleção.

        ![seleção do serviço de bot](../media/qnamaker-tutorials-create-bot/bot-service-qna-template.PNG)

4. Confirme que o serviço de bot foi implantado.

    - Selecione **Notificações** (o ícone de sino que fica ao longo da borda superior do portal do Azure). A notificação será alterada de **Implantação iniciada** para **Implantação bem-sucedida**.
    - Depois que a notificação muda para **Implantação bem-sucedida**, selecione **Ir para o recurso** nessa notificação.

## <a name="chat-with-the-bot"></a>Conversar com o bot
Selecionar **Ir para o recurso** leva você até a folha de recursos do bot.

Depois que o bot está registrado, clique em **Teste no chat da Web** para abrir o painel do chat da Web. Digite "hello" no chat da Web.

![chat da Web do bot do QnA](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

O bot responde com "Defina QnAKnowledgebaseId e QnASubscriptionKey nas Configurações do aplicativo. Saiba como fazer isso em https://aka.ms/qnaabssetup". Essa resposta confirma que o bot do QnA recebeu a mensagem, mas não há nenhuma base de dados de conhecimento do QnA Maker associada a ele ainda. Faça isso na próxima etapa.

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>Conecte a sua base de dados de conhecimento do QnA Maker ao bot

1. Abra **Configurações do aplicativo** e edite os campos **QnAKnowledgebaseId**, **QnAAuthKey** e **QnAEndpointHostName** para conter os valores de sua base de dados de conhecimento do QnA Maker.

    ![configurações do aplicativo](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

2. Obtenha a ID da sua base de dados de conhecimento, a URL do host e a chave do ponto de extremidade da guia de configurações de sua base de dados de conhecimento em https://qnamaker.ai.
    - Faça logon no [QnA Maker](https://qnamaker.ai)
    - Vá até a sua base de dados de conhecimento
    - Clique na guia **Configurações**
    - **Publique** sua base de dados de conhecimento, se ainda não tiver feito isso

    ![Valores do QnA Maker](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> Se você quiser conectar a versão prévia da base de dados de conhecimento ao bot do QnA, defina o valor de **Ocp-Apim-Subscription-Key** como **QnAAuthKey**. Deixe o **QnAEndpointHostName** vazio.

## <a name="test-the-bot"></a>Testar o bot
No portal do Azure, clique em **Teste no chat da Web** para testar o bot. 

![Bot do QnA Maker](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

Seu bot do QnA agora responde de sua base de dados de conhecimento.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Integrar o QnA Maker e LUIS](./integrate-qnamaker-luis.md)

## <a name="see-also"></a>Consulte também

- [Gerencie sua base de dados de conhecimento](https://qnamaker.ai)
- [Habilite o bot em canais diferentes](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
