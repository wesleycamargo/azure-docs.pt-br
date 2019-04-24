---
title: Conectar-se aos Hubs de eventos do Azure - aplicativos lógicos do Azure
description: Gerenciar e monitorar eventos com Hubs de Eventos do Azure e Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: a59f21478f85f238d91c01faed44d8e49cb15f0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60313388"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Monitorar, receber e enviar eventos com Hubs de Eventos do Azure e Aplicativos Lógicos do Azure

Este artigo mostra como é possível monitorar e gerenciar eventos enviados para os [Hubs de Eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md) a partir de um aplicativo lógico com o conector de Hubs de Eventos do Azure. Dessa forma, é possível aplicativos lógicos que automatizam tarefas e fluxos de trabalho para verificar, enviar e receber eventos do Hub de Eventos.

Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Para obter informações técnicas específicas do conector, consulte a <a href="https://docs.microsoft.com/connectors/eventhubs/" target="blank">Referência do conector dos Hubs de Eventos do Azure</a>.

## <a name="prerequisites"></a>Pré-requisitos

* Um [namespace dos Hubs de Eventos e Hub de Eventos do Azure](../event-hubs/event-hubs-create.md)

* O aplicativo lógico no qual você deseja acessar o Hub de Eventos. Para iniciar o aplicativo lógico com um gatilho dos Hubs de Eventos do Azure, será necessário um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Verifique as permissões e obtenha a cadeia de conexão

Para seu aplicativo lógico acessar o Hub de Eventos, verifique suas permissões e obtenha a cadeia de conexão para seu namespace de Hubs de Eventos.

1. Entre no <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>.

2. Acesse o *namespace* de seus Hubs de Eventos, não um Hub de Eventos específico. Na página do namespace, em **Configurações**, escolha **Políticas de acesso compartilhado**. Em **Declarações**, verifique se você tem permissões de **Gerenciamento** para esse namespace.

   ![Gerenciar permissões para seu namespace do Hub de Eventos](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. Se quiser manualmente inserir suas informações de conexão mais tarde, obtenha a cadeia de caracteres de conexão para o namespace dos Hubs de Eventos.

   1. Em **Política**, escolha **RootManageSharedAccessKey**.

   2. Localize a cadeia de conexão da chave primária. Escolha o botão de cópia e salve a cadeia de conexão para uso posterior.

      ![Copie a cadeia de conexão do namespace dos Hubs de Eventos](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Para confirmar se a cadeia de conexão está associada ao namespace de Hubs de Eventos ou a um hub de eventos específico, certifique-se de que a cadeia de conexão não tenha o parâmetro `EntityPath` . Se você encontrar esse parâmetro, a cadeia de conexão servirá para uma "entidade" de Hub de Eventos específica e não será a cadeia de caracteres correta a ser usada com seu aplicativo lógico.

4. Agora continue com o [Adicionar um gatilho de Hubs de Eventos](#add-trigger) ou [Adicionar uma ação de Hubs de Eventos](#add-action).

<a name="add-trigger"></a>

## <a name="add-an-event-hubs-trigger"></a>Adicionar um gatilho de Hubs de Eventos

Nos Aplicativos Lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico e inicia a execução do fluxo de trabalho do aplicativo.

Este exemplo mostra como é possível iniciar um fluxo de trabalho de aplicativo lógico quando novos eventos são enviados para o Hub de Eventos.

1. No portal do Azure ou no Visual Studio, crie um aplicativo lógico em branco, que abre o Designer de Aplicativos Lógicos. Este exemplo usa o portal do Azure.

2. Na caixa de pesquisa, insira "hubs de eventos" como filtro. Na lista de gatilhos, selecione o gatilho desejado.

   Este exemplo usa esse gatilho:

   **Hubs de eventos - quando os eventos estiverem disponíveis no Hub de eventos**

   ![Selecionar gatilho](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

3. Se for solicitado-lhe a fornecer detalhes da conexão, [crie sua conexão de Hubs de Eventos agora](#create-connection). Ou, se a conexão já existir, forneça as informações necessárias para o gatilho.

   1. Na lista **nome do Hub de Eventos**, selecione o Hub de Eventos que você quer monitorar.

      ![Especifique o Hub de Evento ou grupo de consumidores](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

   2. Selecione o intervalo e a frequência de quantas vezes você quer que o gatilho verifique o Hub de Eventos.

   3. Opcionalmente, marque algumas das opções avançadas do GATILHO, escolha **Mostrar opções avançadas**.

      ![Opções avançadas de gatilho](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-advanced.png)

      | Propriedade | Detalhes | 
      |----------|---------| 
      | Tipo de conteúdo  | Selecione o tipo de conteúdo do evento. O padrão é "application/octet-stream". |
      | Esquema de conteúdo | Insira o esquema de conteúdo em JSON para os eventos que são lidos a partir do Hub de eventos. |
      | Nome do grupo de consumidor | Insira o [nome do grupo de consumidores](../event-hubs/event-hubs-features.md#consumer-groups) do Hub de Eventos para ler os eventos. Se não especificado, será usado o grupo de consumidores padrão. |
      | Chave de participação mínima | Insira a ID de [partição](../event-hubs/event-hubs-features.md#partitions) mínima para leitura. Por padrão, todas as partições são lidas. |
      | Chave de participação máxima | Insira a ID de [participação](../event-hubs/event-hubs-features.md#partitions) para leitura. Por padrão, todas as partições são lidas. |
      | Contagem de eventos máxima | Insira um valor para o número máximo de eventos. O gatilho retorna entre um e o número de eventos especificado por essa propriedade. |
      |||

4. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

5. Agora, continue a adicionar uma ou mais ações ao aplicativo lógico para as tarefas que você deseja executar com os resultados do gatilho.

> [!NOTE]
> Todos os gatilhos de Hub de Eventos são gatilhos de *sondagem longa*, o que significa que quando um gatilho dispara, o gatilho processa todas os eventos e aguarda 30 segundos para que mais eventos apareçam no Hub de Eventos.
> Se nenhum evento for recebido em 30 segundos, a execução do gatilho será ignorada. Caso contrário, o gatilho continuará lendo os eventos até que o Hub de Eventos esteja vazio.
> A próxima pesquisa de gatilhos acontecerá com base no intervalo de recorrência que você especificar nas propriedades do gatilho.

<a name="add-action"></a>

## <a name="add-an-event-hubs-action"></a>Adicionar uma ação de Hubs de Eventos

Em Aplicativos Lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa do fluxo de trabalho que segue um gatilho ou outra ação. Para este exemplo, o aplicativo lógico começa com um gatilho de Hubs de Eventos que verifica novos eventos no Hub de Eventos.

1. No portal do Azure ou no Visual Studio, abra o aplicativo lógico no Designer de Aplicativos Lógicos. Este exemplo usa o portal do Azure.

2. No gatilho ou ação, escolha **Nova etapa** > **Adicionar uma ação**.

   Para adicionar uma ação entre etapas existentes, mova o mouse sobre a seta de conexão. 
   Escolha o sinal de adição (**+**) que aparece e, em seguida, escolha **Adicionar uma ação**.

3. Na caixa de pesquisa, insira "hubs de eventos" como filtro.
Na lista de ações, selecione a ação desejada.

   Neste exemplo, selecione esta ação: **Hubs de eventos - enviar evento**

   ![Selecione "Hubs de Eventos - Enviar evento"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

4. Se for solicitado-lhe a fornecer detalhes da conexão, [crie sua conexão de Hubs de Eventos agora](#create-connection). Ou, se a conexão já existir, forneça as informações necessárias para a ação.

   | Propriedade | Obrigatório | DESCRIÇÃO |
   |----------|----------|-------------|
   | Nome do Hub de Eventos | Sim | Selecione o Hub de Eventos para o qual você quer enviar o evento |
   | Conteúdo do evento | Não  | O conteúdo do evento que você quer enviar |
   | propriedades | Não  | As propriedades e os valores do aplicativo a serem enviados |
   ||||

   Por exemplo: 

   ![Selecione o nome do Hub de Eventos e forneça o conteúdo do evento](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

5. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Conectar ao Hub de Eventos

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Quando for solicitado a fornecer informações de conexão, forneça estes detalhes:

   | Propriedade | Necessário | Value | Descrição |
   |----------|----------|-------|-------------|
   | Nome da Conexão | Sim | <*connection-name*> | O nome para criar a conexão |
   | Namespace do Hubs de Eventos | Sim | <*event-hubs-namespace*> | Selecione o namespace do Hubs de Eventos que você quer usar. |
   |||||  

   Por exemplo: 

   ![Criar conexão do Hub de Eventos](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Para inserir manualmente a cadeia de conexão, escolha **Inserir manualmente informações de conexão**. 
   Saiba [como localizar a cadeia de conexão](#permissions-connection-string).

2. Selecione a política de Hubs de Eventos a ser usada, se ainda não estiver selecionada. Escolha **Criar**.

   ![Criar conexão do Hub de Eventos, parte 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Depois de criar a conexão, continue com o [gatilho Adicionar Hubs de Eventos](#add-trigger) ou [ação Adicionar Hubs de Eventos](#add-action).

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo de Swagger do conector, confira a [página de referência do conector](/connectors/eventhubs/).

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)