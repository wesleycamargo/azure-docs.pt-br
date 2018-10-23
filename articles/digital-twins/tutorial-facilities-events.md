---
title: Capturar eventos de um espaço dos Gêmeos Digitais do Azure | Microsoft Docs
description: Saiba como receber notificações de seus espaços integrando os Gêmeos Digitais do Azure ao Aplicativo Lógico do Azure usando as etapas deste tutorial.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 91dd16938efbd1e24419352f66e3238646a77e8a
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323136"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-using-logic-apps"></a>Tutorial: Receber notificações de espaços dos Gêmeos Digitais do Azure usando o Aplicativo Lógico

Depois de implantar sua instância dos Gêmeos Digitais do Azure, provisionar seus espaços e implementar funções personalizadas para monitorar condições específicas, você poderá notificar o administrador do escritório por email quando as condições monitoradas ocorrerem. 

No [primeiro tutorial](tutorial-facilities-setup.md), você configurou o grafo espacial de um prédio imaginário, com uma sala contendo sensores de movimento, dióxido de carbono e temperatura. No [segundo tutorial](tutorial-facilities-udf.md), você provisionou o grafo e uma função personalizada chamou a função definida pelo usuário para monitorar esses valores de sensor e disparar notificações quando a sala está vazia e a temperatura e o nível de dióxido de carbono estão em um patamar confortável. Este tutorial mostra como você pode integrar essas notificações ao Aplicativo Lógico do Azure para enviar emails quando uma sala assim está disponível. Um administrador do escritório pode usar essas informações para tornar a reserva de salas de reunião pelos funcionários mais eficiente. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Integrar eventos à Grade de Eventos
> * Notificar eventos com o Aplicativo Lógico
    
## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você [configurou](tutorial-facilities-setup.md) e [provisionou](tutorial-facilities-udf.md) sua configuração dos Gêmeos Digitais do Azure. Antes de prosseguir, verifique se você tem:
- Uma [conta do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma instância dos Gêmeos Digitais em execução.
- As [amostras de C# dos Gêmeos Digitais](https://github.com/Azure-Samples/digital-twins-samples-csharp) baixadas e extraídas no computador de trabalho.
- O [SDK do .NET Core versão 2.1.403 ou superior](https://www.microsoft.com/net/download) no computador de desenvolvimento para executar a amostra. Execute `dotnet --version` para verificar se a versão instalada é a correta. 
- Uma conta do Office 365 para enviar e-mails de notificação.

## <a name="integrate-events-with-event-grid"></a>Integrar eventos à Grade de Eventos 
Nesta seção, você configurará uma [Grade de Eventos](../event-grid/overview.md) para coletar eventos da instância dos Gêmeos Digitais e os redirecionar para um [manipulador de eventos](../event-grid/event-handlers.md) como o Aplicativo Lógico.

### <a name="create-event-grid-topic"></a>Criar tópico da Grade de Eventos
Os [Tópicos da Grade de Eventos](../event-grid/concepts.md#topics) fornecem uma interface para rotear os eventos gerados pela função definida pelo usuário. 

1. Entre no [Portal do Azure](https://portal.azure.com).

1. No painel de navegação à esquerda, clique em **Criar um recurso**. 

1. Pesquise e selecione **Tópico da Grade de Eventos**. Clique em **Criar**.

1. Insira um **Nome** para o tópico da grade de eventos e escolha a **Assinatura**. Selecione o **Grupo de recursos** usado ou criado para sua instância dos Gêmeos Digitais e o **Local**. Clique em **Criar**. 

    ![Criar tópico da Grade de Eventos](./media/tutorial-facilities-events/create-event-grid-topic.png)

1. Navegue até o tópico da grade de eventos a partir do grupo de recursos, clique em **Visão Geral**e copie o valor de **Ponto de Extremidade do Tópico** em um arquivo temporário. Você precisará dessa URL na seção a seguir. 

1. Clique em **Chaves de acesso**e copie a **Chave 1** e a **Chave 2** em um arquivo temporário. Você precisará desses valores para criar o ponto de extremidade na seção a seguir.

    ![Chaves da Grade de Eventos](./media/tutorial-facilities-events/event-grid-keys.png)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Criar um ponto de extremidade para o Tópico da Grade de Eventos

1. Na janela de comando, verifique se você está na pasta **_occupancy-quickstart\src_** da amostra dos Gêmeos Digitais.

1. Abra o arquivo **_actions\createEndpoints.yaml_** no editor do Visual Studio Code. Verifique se ele tem o seguinte conteúdo:

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_Event_Grid
      secondaryConnectionString: Secondary_connection_string_for_your_Event_Grid
      path: Event_Grid_Topic_Path
    ```

1. Substitua o espaço reservado `Primary_connection_string_for_your_Event_Grid` pelo valor da **Chave 1**. 

1. Substitua o espaço reservado `Secondary_connection_string_for_your_Event_Grid` pelo valor da **Chave 2**.

1. Substitua o espaço reservado `Event_Grid_Topic_Path` pelo caminho do tópico da grade de eventos. Obtenha esse caminho removendo o *https://* e os caminhos de recurso à direita da URL do **Ponto de Extremidade do Tópico**. Ele deve ficar aproximadamente com este formato: **seuNomedaGradedeEvento.seuLocal.eventgrid.azure.net**. 

    > [!IMPORTANT]
    > Insira todos os valores sem aspas. Verifique se há pelo menos um caractere de espaço após os dois-pontos no arquivo *YAML*. Você também pode validar o conteúdo do arquivo *YAML* usando qualquer validador YAML online, como [esta ferramenta](https://onlineyamltools.com/validate-yaml).

1. Salve e feche o arquivo. Na janela de comando, execute o comando a seguir e entre quando solicitado. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Este comando cria o ponto de extremidade para a Grade de Eventos. 

   ![Pontos de extremidade para a Grade de Eventos](./media/tutorial-facilities-events/dotnet-create-endpoints.png)


## <a name="notify-events-with-logic-app"></a>Notificar eventos com o Aplicativo Lógico
O serviço [Aplicativo Lógico do Azure](../logic-apps/logic-apps-overview.md) permite que você crie tarefas automatizadas para eventos recebidos de outros serviços. Nesta seção, você vai configurar aplicativos lógicos para criar notificações por email para eventos roteados a partir de seus sensores espaciais, com a ajuda de um [Tópico da Grade de Eventos](../event-grid/overview.md).

1. No painel de navegação esquerdo do [portal do Azure](https://portal.azure.com), clique em **Criar um recurso**.

1. Pesquise e selecione um novo recurso **Aplicativo Lógico**. Clique em **Criar**.

1. Insira um **nome** para o aplicativo lógico e selecione a **Assinatura**, o **Grupo de recursos** e o **Local**. Clique em **Criar**.

    ![Criar aplicativo lógico](./media/tutorial-facilities-events/create-logic-app.png)

1. Abra o Aplicativo Lógico quando for implantado e abra o painel **Designer de Aplicativo Lógico**. 

1. Selecione o gatilho **Quando um evento da Grade de Eventos ocorrer**. **Entre** no locatário com sua conta do Azure quando solicitado. Confirme para **Permitir acesso** à Grade de Eventos quando solicitado. Clique em **Continuar**.

1. Na janela **Quando um evento de recurso ocorrer (versão prévia)**, 
    1. Selecione a **Assinatura** que você usou para criar a Grade de Eventos anteriormente,

    1. Selecione **Microsoft.EventGrid.Topics** como o **Tipo de Recurso**,

    1. Selecione o recurso Grade de Eventos na caixa de listagem suspensa do **Nome do Recurso**.

    ![Criar aplicativo lógico](./media/tutorial-facilities-events/logic-app-resource-event.png)

1. Clique no botão **Nova etapa**.

1. Na janela **Escolher uma ação**,
    1. Pesquise a frase *analisar json*e selecione a ação **Analisar JSON**.

    1. Clique dentro do campo **Conteúdo** e selecione **Corpo** na lista **Conteúdo dinâmico**.

    1. Clique em **Usar amostra para gerar esquema**. Cole a carga JSON a seguir e clique em **Concluído**.

        ```JSON
        {
        "id": "32162f00-a8f1-4d37-aee2-9312aabba0fd",
        "subject": "UdfCustom",
        "data": {
          "TopologyObjectId": "20efd3a8-34cb-4d96-a502-e02bffdabb14",
          "ResourceType": "Space",
          "Payload": "\"Air quality is poor.\"",
          "CorrelationId": "32162f00-a8f1-4d37-aee2-9312aabba0fd"
        },
        "eventType": "UdfCustom",
        "eventTime": "0001-01-01T00:00:00Z",
        "dataVersion": "1.0",
        "metadataVersion": "1",
        "topic": "/subscriptions/a382ee71-b48e-4382-b6be-eec7540cf271/resourceGroups/HOL/providers/Microsoft.EventGrid/topics/DigitalTwinEventGrid"
        }
        ```
    
    Essa carga tem valores fictícios. O Aplicativo Lógico usa essa carga de exemplo para gerar um **Esquema**.
    
    ![O Aplicativo Lógico analisa JSON para a Grade de Eventos](./media/tutorial-facilities-events/logic-app-parse-json.png)

1. Clique no botão **Nova etapa**.

1. Na janela **Escolher uma ação**,
    1. Pesquise e selecione **Controle de Condição** na lista de **Ações**. 

    1. Clique dentro da primeira caixa de texto **Escolher um valor** e selecione **eventType** na lista **Conteúdo dinâmico** para a janela **Analisar JSON**.

    1. Clique na segunda caixa de texto **Escolher um valor** e digite *UdfCustom*.

    ![Aplicativo Lógico – Analisar JSON para a Grade de Eventos](./media/tutorial-facilities-events/logic-app-condition.png)

1. Na janela **Se verdadeiro**,
    1. Clique em **Adicionar uma ação** e selecione *Outlook do Office 365*.

    1. Na lista de **Ações**, selecione **Enviar um email**. Clique em **Entrar** e use suas credenciais de conta de email. Clique em **Permitir acesso** quando solicitado.

    1. Na caixa **Para**, insira a ID do email para receber notificações. Em **Assunto**, insira o texto *Notificação dos Gêmeos Digitais sobre a má qualidade do ar no espaço*e selecione **TopologyObjectId** na lista **Conteúdo dinâmico** de **Analisar JSON**.

    1. No **Corpo** da mesma janela, insira um texto semelhante a este: *Qualidade do ar ruim detectada em uma sala; a temperatura precisa ser ajustada*. Você pode elaborar mais usando elementos da lista **Conteúdo dinâmico** conforme mostrado abaixo.

    ![Aplicativo Lógico – Enviar um email](./media/tutorial-facilities-events/logic-app-send-email.png)

1. Clique no botão **Salvar** na parte superior do painel **Designer do Aplicativo Lógico**.

1. Não deixe de simular dados de sensor navegando até a pasta **_device-connectivity_** do exemplo de Gêmeo Digital em uma janela de comando e executando `dotnet run`.

Em alguns minutos, você deve começar a receber notificações por email do Aplicativo Lógico. 

   ![Aplicativo Lógico – Enviar um email](./media/tutorial-facilities-events/logic-app-notification.png)

Para interromper o recebimento desses emails, navegue até **Aplicativo Lógico** no portal e selecione o painel **Visão Geral**. Clique em **Desabilitar**.


## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser parar de explorar os Gêmeos Digitais do Azure além desse ponto, fique à vontade para excluir os recursos criados neste tutorial:

1. No menu à esquerda no [portal do Azure](http://portal.azure.com), clique em **Todos os recursos**, selecione o grupo de recursos dos Gêmeos Digitais e **Excluir**.
2. Se precisar, você também poderá excluir os aplicativos de exemplo no computador de trabalho. 


## <a name="next-steps"></a>Próximas etapas

Você pode prosseguir para o próximo tutorial para aprender a visualizar os dados de sensor, analisar tendências e detectar anomalias. 
> [!div class="nextstepaction"]
> [Tutorial: Visualizar e analisar eventos de espaços dos Gêmeos Digitais do Azure usando o Time Series Insights](tutorial-facilities-analyze.md)

Também é possível prosseguir para saber mais sobre grafos de inteligência espacial e modelos de objeto nos Gêmeos Digitais do Azure. 
> [!div class="nextstepaction"]
> [Noções básicas sobre modelos de objeto e grafos de inteligência espacial dos Gêmeos Digitais](concepts-objectmodel-spatialgraph.md)