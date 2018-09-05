---
title: Conectar-se ao Twilio por meio do Aplicativo Lógico do Azure | Microsoft Docs
description: Automatizar tarefas e fluxos de trabalho que gerenciam mensagens globais de SMS, MMS e IP por meio de sua conta do Twilio usando o Aplicativo Lógico do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: db7677042737ea1377af54cc02ee1c82c05435c8
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047567"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Gerenciar mensagens no Twilio com o Aplicativo Lógico do Azure

Com o Aplicativo Lógico do Azure e o conector do Twilio, você pode criar tarefas automatizadas e fluxos de trabalho que obtém, enviam e listam as mensagens do Twilio, que incluem mensagens globais de SMS, MMS e IP. Você pode usar essas ações para executar tarefas com sua conta do Twilio. Você também pode fazer com que outras ações usem a saída das ações do Twilio. Por exemplo, quando uma nova mensagem chega, você pode enviar o conteúdo da mensagem com o conector do Slack. Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

* No [Twilio](https://www.twilio.com/): 

  * A ID da conta do Twilio e o [token de autenticação](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them), que podem ser encontrados no seu painel do Twilio

    Suas credenciais autorizam o aplicativo lógico a criar uma conexão e acessar sua conta do Twilio por meio do aplicativo lógico. 
    Se você estiver usando uma conta de avaliação do Twilio, poderá enviar SMS somente para números de telefone *verificados*.

  * Um número de telefone Twilio verificado que possa enviar SMS

  * Um número de telefone Twilio verificado que possa receber SMS

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar a conta do Twilio. Para usar uma ação do Twilio, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho **Recorrência**.

## <a name="connect-to-twilio"></a>Conectar-se ao Twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Escolha um caminho: 

     * Na última etapa em que você deseja adicionar uma ação, escolha **Nova etapa**. 

       -ou-

     * Entre as etapas em que você deseja adicionar uma ação, mova o ponteiro sobre a seta entre as etapas. 
     Escolha o sinal de adição (**+**) que aparece e, em seguida, selecione **Adicionar uma ação**.
     
       Na caixa de pesquisa, insira "twilio" como o filtro. 
       Na lista de ações, selecione a ação desejada.

1. Forneça os detalhes necessários para sua conexão e, em seguida, escolha **Criar**:

   * O nome a usar para a conexão
   * A ID da sua conta do Twilio 
   * O token de acesso (autenticação) do Twilio

1. Forneça os detalhes necessários para a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/twilio/) do conector.

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)