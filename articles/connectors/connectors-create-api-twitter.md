---
title: Conectar-se com o Twitter dos Aplicativos Lógicos do Azure | Microsoft Docs
description: Automatize fluxos de trabalho e tarefas que monitoram e gerenciam tweets, além de obter dados sobre seguidores, usuários seguidos, outros usuários, linhas do tempo e mais em sua conta do Twitter usando os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 0fbd89202796cb4543dbecbeee605c9b87cc9d05
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104979"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Monitore e gerencie o Twitter usando os Aplicativos Lógicos do Azure

Com os Aplicativos Lógicos do Azure e o conector do Twitter, é possível criar fluxos de trabalho e tarefas automatizadas que monitoram e gerenciam dados importantes para você no Twitter, como tweets, seguidores, usuários e usuários seguidos, linhas do tempo e muito mais, bem como outras ações como:

* Monitorar, postar e pesquisar tweets.
* Obter dados como seguidores, usuários seguidos, linhas do tempo e muito mais.

Você pode usar gatilhos que obtêm respostas de sua conta do Twitter e disponibilizam a saída para outras ações. Você pode usar ações que executam tarefas com sua conta do Twitter. Você também pode fazer com que outras ações usem a saída das ações do Twitter. Por exemplo, quando um novo tweet com uma hashtag específica aparecer, você poderá enviar mensagens com o conector do Slack. Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

* Suas credenciais de usuário e conta do Twitter

   Suas credenciais autorizam o aplicativo lógico a criar uma conexão e acessar sua conta do Twitter.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar a conta do Twitter. Para iniciar com um gatilho do Twitter, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação do Twitter, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho de **Recorrência**.

## <a name="connect-to-twitter"></a>Conectar-se ao Twitter

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Escolha um caminho: 

   * Para aplicativos lógicos em branco, na caixa de pesquisa, insira "twitter" como filtro. 
   Na lista de gatilhos, selecione o gatilho desejado. 

     -ou-

   * Para aplicativos lógicos existentes: 
   
     * Na última etapa em que você deseja adicionar uma ação, escolha **Nova etapa**. 

       -ou-

     * Entre as etapas em que você deseja adicionar uma ação, mova o ponteiro sobre a seta entre as etapas. 
     Escolha o sinal de adição (**+**) que aparece e, em seguida, selecione **Adicionar uma ação**.
     
       Na caixa de pesquisa, digite "twitter" como filtro. 
       Na lista de ações, selecione a ação desejada.

1. Se for solicitado que você entre no Twitter, entre agora para que possa autorizar o acesso a seu aplicativo lógico.

1. Forneça os detalhes necessários para o gatilho ou a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="examples"></a>Exemplos

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Gatilho do Twitter: Quando um novo tweet é postado

Esse gatilho inicia um fluxo de trabalho do aplicativo lógico quando detecta um novo tweet, por exemplo, com a hashtag #Seattle. Sendo assim, por exemplo, quando esses tweets são encontrados, você pode adicionar um arquivo com o conteúdo dos tweets ao armazenamento, como uma conta do Dropbox, usando o conector do Dropbox. 

Opcionalmente, você pode incluir uma condição segundo a qual tweets qualificados devem vir de usuários com um número mínimo de seguidores especificado.

**Exemplo corporativo**: Você pode usar esse gatilho monitore tweets sobre sua empresa e carregar o conteúdo dos tweets para um banco de dados SQL.

### <a name="twitter-action-post-a-tweet"></a>Ação do Twitter: Publicar um tweet

Essa ação posta um tweet, mas você pode configurá-la para que o tweet tenha o conteúdo dos tweets encontrados pelo gatilho descrito anteriormente. 

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/twitterconnector/) do conector.

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
