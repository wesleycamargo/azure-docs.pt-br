---
title: Conectar-se com o Yammer do Aplicativo Lógico do Azure | Microsoft Docs
description: Automatize tarefas e fluxos de trabalho que monitoram, postam e gerenciam mensagens, feeds e muito mais no Yammer usando o Aplicativo Lógico do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: ca2d28f3438fd166fa282488206662c95777bf3b
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233320"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Monitore e gerencie sua conta do Yammer usando o Aplicativo Lógico do Azure

Com o Aplicativo Lógico do Azure e o conector do Yammer, você pode criar fluxos de trabalho e tarefas automatizadas que monitoram e gerenciam mensagens, feeds e mais em sua conta do Yammer, bem como outras ações, por exemplo:

* Monitorar quando novas mensagens aparecem em feeds e grupos seguidos.
* Obter detalhes de usuários, mensagens, grupos e redes, entre outros.
* Postar e curtir mensagens.

Você pode usar gatilhos que obtêm respostas de sua conta do Yammer e disponibilizam a saída para outras ações. Você pode usar ações que executam tarefas com sua conta do Yammer. Também é possível fazer com que outras ações usem a saída das ações do Yammer. Por exemplo, quando novas mensagens aparecem em feeds ou grupos, é possível compartilhar essas mensagens com o conector do Slack. Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

* Suas credenciais de usuário e conta do Yammer

   Suas credenciais autorizam o aplicativo lógico a criar uma conexão e acessar sua conta do Yammer.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico em que você deseja acessar sua conta do Yammer. Para iniciar com um gatilho do Yammer, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação do Yammer, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho de **Recorrência**.

## <a name="connect-to-yammer"></a>Conectar-se com o Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Escolha um caminho: 

   * Para aplicativos lógicos em branco, na caixa de pesquisa, insira "yammer" como filtro. 
   Na lista de gatilhos, selecione o gatilho desejado. 

     -ou-

   * Para aplicativos lógicos existentes: 
   
     * Na última etapa em que você deseja adicionar uma ação, escolha **Nova etapa**. 

       -ou-

     * Entre as etapas em que você deseja adicionar uma ação, mova o ponteiro sobre a seta entre as etapas. 
     Escolha o sinal de adição (**+**) que aparece e, em seguida, selecione **Adicionar uma ação**.
     
       Na caixa de pesquisa, insira "yammer" como filtro. 
       Na lista de ações, selecione a ação desejada.

1. Se for solicitado que você entre no Yammer, entre agora para que possa permitir o acesso.

1. Forneça os detalhes necessários para o gatilho ou a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/yammer/) do conector.

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)