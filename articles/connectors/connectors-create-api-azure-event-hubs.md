---
title: "Configurar o monitoramento de eventos com Hubs de Eventos do Azure para Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Monitorar fluxos de dados para receber eventos e enviar eventos com seus aplicativos lógicos usando os Hubs de Eventos do Azure"
services: logic-apps
keywords: fluxo de dados, monitor de eventos, hubs de eventos
author: ecfan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 076f7dd11ca8c153046727861ecb755e88f32b01
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="monitor-receive-and-send-events-with-the-event-hubs-connector"></a>Monitorar, receber e enviar eventos com o conector do Hubs de Eventos

Para configurar um monitor de eventos para que seu aplicativo lógico possa detectar eventos, receber eventos e enviar eventos, conecte-se a um [Hub de Eventos do Azure](https://azure.microsoft.com/services/event-hubs) de seu aplicativo lógico. Saiba mais sobre os [Hubs de Eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md) e [como funcionam os preços para os conectores dos Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>pré-requisitos

Antes de usar o conector de Hubs de Eventos, você deve ter estes itens:

* Um [namespace dos Hubs de Eventos e Hub de Eventos do Azure](../event-hubs/event-hubs-create.md)
* Um [aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-event-hubs"></a>Conectar-se aos Hubs de Eventos do Azure

Antes de seu aplicativo lógico poder acessar qualquer serviço, você precisa criar uma [*conexão*](./connectors-overview.md) entre seu aplicativo lógico e o serviço, se ainda não tiver feito isso. Essa conexão autoriza seu aplicativo lógico a acessar os dados. Para seu aplicativo lógico acessar o Hub de Eventos, verifique suas permissões e obtenha a cadeia de conexão para seu namespace de Hubs de Eventos.

1.  Entre no [portal do Azure](https://portal.azure.com "portal do Azure"). 

2.  Acesse o *namespace* de seus Hubs de Eventos, não um Hub de Eventos específico. Na página do namespace, em **Configurações**, escolha **Políticas de acesso compartilhado**. Em **Declarações**, verifique se você tem permissões de **Gerenciamento** para esse namespace.

    ![Gerenciar permissões para seu namespace do Hub de Eventos](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. Se quiser manualmente inserir suas informações de conexão mais tarde, obtenha a cadeia de caracteres de conexão para o namespace dos Hubs de Eventos. Escolha **RootManageSharedAccessKey**. Ao lado de sua cadeia de conexão de chave primária, escolha o botão de cópia. Salve a cadeia de conexão para uso posterior.

    ![Copie a cadeia de conexão do namespace dos Hubs de Eventos](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

    > [!TIP]
    > Para confirmar se a cadeia de conexão está associada ao namespace dos seus Hubs de Eventos ou com um hub de eventos específico, verifique na cadeia de conexão o parâmetro `EntityPath`. Se você encontrar esse parâmetro, a cadeia de conexão servirá para uma "entidade" de Hub de Eventos específica e não será a cadeia de caracteres correta a ser usada com seu aplicativo lógico.

## <a name="trigger-workflow-when-your-event-hub-gets-new-events"></a>Acionar o fluxo de trabalho quando seu Hub de Eventos receber novos eventos

Um [*gatilho*](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um evento que inicia um fluxo de trabalho em seu aplicativo lógico. Para iniciar um fluxo de trabalho quando novos eventos forem enviados ao seu Hub de Eventos, execute estas etapas para adicionar o gatilho que detecta esse evento.

1. No [portal do Azure](https://portal.azure.com "portal do Azure"), acesse seu aplicativo lógico existente ou crie um aplicativo lógico em branco.

2. No Designer de Aplicativos Lógicos, digite "hubs de eventos" na caixa de pesquisa como seu filtro. Selecione esse gatilho: **quando os eventos estiverem disponíveis no Hub de Eventos**

   ![Selecionar o gatilho quando seu Hub de Eventos receber novos eventos](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

   1. Se você ainda não tiver uma conexão para o namespace de seus Hubs de Eventos, receberá uma solicitação para criar essa conexão. Dê um nome para sua conexão e selecione o namespace dos Hubs de Eventos do Azure que deseja usar.

      ![Criar conexão do Hub de Eventos](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

      Ou, para inserir manualmente a cadeia de conexão, escolha **Inserir manualmente as informações de conexão**. 
      Saiba [como localizar a cadeia de conexão](#permissions-connection-string).

   2. Agora, selecione a política de Hubs de Eventos a ser usada e, em seguida, escolha **Criar**.

      ![Criar conexão do Hub de Eventos, parte 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Selecione o Hub de Eventos para monitorar e configure o intervalo e a frequência para quando verificar o Hub de Eventos.

    ![Especifique o Hub de Evento ou grupo de consumidores](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

    > [!TIP]
    > Para selecionar, opcionalmente, um grupo de consumidores para ler eventos, escolha **Mostrar opções avançadas**.

4. Salve seu aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer.

Agora, quando seu aplicativo lógico verifica o Hub de Eventos selecionada e localiza um novo evento, o gatilho executa as ações nele para o evento encontrado.

## <a name="send-events-to-your-event-hub-from-your-logic-app"></a>Enviar eventos para seu Hub de Eventos de seu aplicativo lógico

Uma [*ação*](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma tarefa executada pelo fluxo de trabalho do aplicativo lógico. Depois de adicionar um gatilho ao seu aplicativo lógico, você poderá adicionar uma ação para executar operações com os dados gerados por esse gatilho. Para enviar um evento para seu Hub de Eventos de seu aplicativo lógico, execute estas etapas.

1. No Designer de Aplicativos Lógicos, no gatilho, escolha **Nova etapa** > **Adicionar uma ação**.

2. Insira "hubs de eventos" como filtro na caixa de pesquisa.
Selecione esta ação: **Hubs de Eventos - Enviar evento**

   ![Selecione "Hubs de Eventos - Enviar evento"](./media/connectors-create-api-azure-event-hubs/select-event-hubs-send-event-action.png)

3. Selecione o Hub de Eventos para onde enviar o evento. Em seguida, digite o conteúdo do evento e quaisquer outros detalhes.

   ![Selecione o nome do Hub de Eventos e forneça o conteúdo do evento](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

4. Salve seu aplicativo lógico.

Agora você configurou uma ação que envia eventos de seu aplicativo lógico. 

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para saber mais sobre gatilhos e ações definidos pelo arquivo Swagger e qualquer limite, revise os [detalhes conector](/connectors/eventhubs/).

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [outros conectores para Aplicativos Lógicos do Azure](../connectors/apis-list.md)