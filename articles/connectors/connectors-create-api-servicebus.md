---
title: "Configurar o sistema de mensagens com o Barramento de Serviço do Azure para Aplicativo Lógico do Azure | Microsoft Docs"
description: "Enviar e receber mensagens com seus aplicativos lógicos usando o Barramento de Serviço do Azure"
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 02/06/2018
ms.author: ladocs
ms.openlocfilehash: e81580db17610adc6be534c9801881f9b68b14fd
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="send-and-receive-messages-with-the-azure-service-bus-connector"></a>Enviar e receber mensagens com o conector do Barramento de Serviço do Azure

Para enviar e receber mensagens com seu aplicativo lógico, conectes ao [Barramento de Serviço do Azure](https://azure.microsoft.com/services/service-bus/). Você pode executar ações como enviar para uma fila, enviar para um tópico, receber de uma fila e receber de uma assinatura. Saiba mais sobre o [Barramento de Serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md) e [como funcionam os preços para os gatilhos dos Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>pré-requisitos

Antes de poder usar o conector do Barramento de Serviço, você deve ter estes itens, que devem existir na mesma assinatura do Azure para que fiquem visíveis entre si:

* Um [namespace de Barramento de Serviço e a entidade de mensagens, como uma fila](../service-bus-messaging/service-bus-create-namespace-portal.md)
* Um [aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-service-bus"></a>Conectar-se ao Barramento de Serviço do Azure

Antes de seu aplicativo lógico poder acessar qualquer serviço, você precisa criar uma [*conexão*](./connectors-overview.md) entre seu aplicativo lógico e o serviço, se ainda não tiver feito isso. Essa conexão autoriza seu aplicativo lógico a acessar os dados. Para que seu aplicativo lógico acesse sua conta do Barramento de Serviço, verifique suas permissões.

1. Entre no [portal do Azure](https://portal.azure.com "portal do Azure"). 

2. Vá para o *namespace* do Barramento de Serviço, não para uma "entidade de mensagens" específica. Na página do namespace, em **Configurações**, escolha **Políticas de acesso compartilhado**. Em **Declarações**, verifique se você tem permissões de **Gerenciamento** para esse namespace.

   ![Gerenciar permissões para o namespace do Barramento de Serviço](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Se quiser manualmente inserir suas informações de conexão mais tarde, obtenha a cadeia de caracteres de conexão para o namespace do Barramento de Serviço. Escolha **RootManageSharedAccessKey**. Ao lado de sua cadeia de conexão de chave primária, escolha o botão de cópia. Salve a cadeia de conexão para uso posterior.

   ![Copiar a cadeia de conexão do namespace do Barramento de Serviço](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Para confirmar se a cadeia de conexão está associada ao namespace do Barramento de Serviço ou a uma entidade específica, verifique na cadeia de conexão o parâmetro `EntityPath`. Se encontrar esse parâmetro, a cadeia de conexão servirá para uma entidade específica e não será a cadeia correta a ser usada com seu aplicativo lógico.

## <a name="trigger-workflow-when-your-service-bus-gets-new-messages"></a>Acionar o fluxo de trabalho quando o Barramento de Serviço recebe novas mensagens

Um [*gatilho*](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um evento que inicia um fluxo de trabalho em seu aplicativo lógico. Para iniciar um fluxo de trabalho quando novas mensagens forem enviadas ao seu Barramento de Serviço, execute estas etapas para adicionar o gatilho que detecta essas mensagens.

1. No [portal do Azure](https://portal.azure.com "portal do Azure"), acesse seu aplicativo lógico existente ou crie um aplicativo lógico em branco.

2. No Designer de Aplicativos Lógicos, digite "barramento de serviço" na caixa de pesquisa como seu filtro. Selecione o conector **Barramento de Serviço**. 

   ![Selecionar o conector Barramento de Serviço](./media/connectors-create-api-azure-service-bus/select-service-bus-connector.png) 

3. Selecione o gatilho que você quer usar. Por exemplo, para executar um aplicativo lógico quando um novo item for enviado para uma fila de Barramento de Serviço, selecione este gatilho: **Barramento de Serviço – Quando uma mensagem é recebida em uma fila (completar automaticamente)**

   ![Selecionar um gatilho do Barramento de Serviço](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   1. Se você ainda não tiver uma conexão para o namespace do Barramento de Serviço, receberá uma solicitação para criar essa conexão. Dê um nome para sua conexão e selecione o namespace do Barramento de Serviço do Azure que deseja usar.

      ![Criar uma conexão do Barramento de Serviço](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      Ou, para inserir manualmente a cadeia de conexão, escolha **Inserir manualmente as informações de conexão**. 
      Saiba [como localizar a cadeia de conexão](#permissions-connection-string).

   2. Agora, selecione a política de Barramento de Serviço a ser usada e, em seguida, escolha **Criar**.

      ![Criar uma conexão do Barramento de Serviço, parte 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

4. Selecione a fila do Barramento de Serviço a ser usada e configure o intervalo e a frequência para quando verificar a fila.

   ![Selecionar a fila do Barramento de Serviço, configurar o intervalo de sondagem](./media/connectors-create-api-azure-service-bus/select-service-bus-queue.png)

5. Salve seu aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer.

Agora, quando seu aplicativo lógico verifica a fila selecionada e localiza uma nova mensagem, o gatilho executa as ações nele para a mensagem encontrada.

## <a name="send-messages-from-your-logic-app-to-your-service-bus"></a>Enviar mensagens de seu aplicativo lógico para o Barramento de Serviço

Uma [*ação*](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma tarefa executada pelo fluxo de trabalho do aplicativo lógico. Depois de adicionar um gatilho ao seu aplicativo lógico, você poderá adicionar uma ação para executar operações com os dados gerados por esse gatilho. Para enviar uma mensagem para sua entidade de mensagens do Barramento de Serviço, siga estas etapas.

1. No Designer de Aplicativos Lógicos, no gatilho, escolha **+ Nova etapa** > **Adicionar uma ação**.

2. Na caixa de pesquisa, insira "barramento de serviço" como filtro. Selecione este conector: **Barramento de Serviço**

   ![Selecionar o conector Barramento de Serviço](./media/connectors-create-api-azure-service-bus/select-service-bus-connector-for-action.png) 

3. Selecione esta ação: **Barramento de Serviço – Enviar mensagem**

   ![Selecione "Barramento de Serviço – Enviar mensagem"](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png)

4. Selecione a entidade de mensagens, que é o nome da fila ou do tópico, para onde enviar a mensagem. Em seguida, digite o conteúdo da mensagem e quaisquer outros detalhes.

   ![Selecione a entidade de mensagens e forneça detalhes da mensagem](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)    

5. Salve seu aplicativo lógico. 

Agora você configurou uma ação que envia mensagens de seu aplicativo lógico. 

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para saber mais sobre gatilhos e ações definidos pelo arquivo Swagger e qualquer limite, revise os [detalhes conector](/connectors/servicebus/).

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [outros conectores para Aplicativos Lógicos do Azure](../connectors/apis-list.md)