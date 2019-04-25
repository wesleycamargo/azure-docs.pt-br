---
title: Conectar-se a qualquer ponto de extremidade HTTP com Aplicativos Lógicos do Azure | Microsoft Docs
description: Automatize tarefas e fluxos de trabalho que se comunicam com qualquer ponto de extremidade HTTP usando os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 01da06ca55199989a3a27012bec101580f5ef853
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60447533"
---
# <a name="call-http-or-https-endpoints-with-azure-logic-apps"></a>Chamar pontos de extremidade HTTP ou HTTPS com os Aplicativos Lógicos do Azure

Com o Aplicativo Lógico do Azure e o conector do protocolo HTTP, você pode automatizar os fluxos de trabalho que se comunicam com qualquer ponto de extremidade HTTP ou HTTPS criando aplicativos lógicos. Por exemplo, você pode monitorar o ponto de extremidade de serviço para seu site. Quando ocorre um evento naquele ponto de extremidade, como o seu site ficar inativo, o evento dispara o fluxo de trabalho do aplicativo lógico e executa as ações especificadas. 

Você pode usar o gatilho HTTP como a primeira etapa no seu fluxo de trabalho de verificação ou *sondagem* de um ponto de extremidade regularmente. Em cada verificação, o gatilho envia uma chamada ou *solicitação* ao ponto de extremidade. A resposta do ponto de extremidade determina se o fluxo de trabalho do aplicativo lógico é executado. O gatilho passa todo o conteúdo da resposta para as ações no aplicativo lógico. 

Você pode usar a ação HTTP como qualquer outra etapa no fluxo de trabalho para chamar o ponto de extremidade quando quiser. A resposta do ponto de extremidade determina como as ações restantes do fluxo de trabalho são executadas.

Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

* A URL do ponto de extremidade de destino que você deseja chamar 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja chamar o ponto de extremidade de destino. Para começar com o gatilho HTTP [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar a ação HTTP, inicie seu aplicativo lógico com um gatilho.

## <a name="add-http-trigger"></a>Adicionar gatilho HTTP

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico em branco no Designer de Aplicativo Lógico se ele ainda não estiver aberto.

1. Na caixa de pesquisa, insira "http" como o filtro. Na lista de gatilhos, selecione o gatilho desejado **HTTP**. 

   ![Selecionar o gatilho HTTP](./media/connectors-native-http/select-http-trigger.png)

1. Forneça os [parâmetros e valores do gatilho HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) que deseja incluir na chamada para o ponto de extremidade de destino. Configure a recorrência para a frequência com que você deseja que o gatilho para verificar o ponto de extremidade de destino.

   ![Insira os parâmetros do gatilho HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Para obter mais informações sobre o gatilho HTTP, os parâmetros e os valores, consulte [Referência de tipos de gatilho e ação](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger).

1. Continue criando o fluxo de trabalho do aplicativo lógico com as ações que são executadas quando o gatilho é acionado.

## <a name="add-http-action"></a>Adicionar ação HTTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Na última etapa em que você deseja adicionar uma ação HTTP, escolha **Nova etapa**. 

   Neste exemplo, o aplicativo lógico começa com o gatilho HTTP como a primeira etapa.

1. Na caixa de pesquisa, insira "http" como o filtro. Na lista de ações, selecione a ação **HTTP**.

   ![Selecionar a ação HTTP](./media/connectors-native-http/select-http-action.png)

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. 
   Escolha o sinal de adição (**+**) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Forneça os [parâmetros e valores da ação HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) que deseja incluir na chamada para o ponto de extremidade de destino. 

   ![Inserir os parâmetros da ação HTTP](./media/connectors-native-http/http-action-parameters.png)

1. Quando terminar, certifique-se de salvar o aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer. 

## <a name="authentication"></a>Authentication

Para definir a autenticação, escolha **Mostrar opções avançadas** dentro da ação ou do gatilho. Para obter mais informações sobre os tipos de autenticação disponíveis para ações e gatilhos HTTP, consulte [Referência de tipos de gatilho e ação](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
