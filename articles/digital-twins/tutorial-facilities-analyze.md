---
title: Analisar eventos de configuração de Gêmeos Digitais do Azure | Microsoft Docs
description: Saiba como visualizar e analisar eventos dos espaços de Gêmeos Digitais do Azure, com o Azure Time Series Insights, usando as etapas neste tutorial.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 1366cafe5d2c526e86905c108b9c7b865aac8f69
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323140"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-using-time-series-insights"></a>Tutorial: Visualizar e analisar eventos dos espaços de Gêmeos Digitais do Azure usando o Time Series Insights

Depois de ter implantado a instância de Gêmeos Digitais do Azure, provisionado seus espaços e implementado a função personalizada para monitorar condições específicas, você pode visualizar os eventos e os dados provenientes de seus espaços em busca de tendências e anomalias. 

No [primeiro tutorial](tutorial-facilities-setup.md), você configurou o grafo espacial de um prédio imaginário, com uma sala contendo os sensores de movimento, dióxido de carbono e temperatura. No [segundo tutorial](tutorial-facilities-udf.md), você provisionou o grafo e uma função definida pelo usuário. A função monitora esses valores de sensor e dispara notificações para obter as condições corretas, isto é, a sala está vazia e os níveis de temperatura e dióxido de carbono estão normais. Este tutorial mostra como você pode integrar as notificações e os dados provenientes da configuração dos Gêmeos Digitais com o Azure Time Series Insights. Assim, você pode visualizar os valores do sensor com o passar do tempo em busca de tendências, como qual sala está sendo mais usada, quais são as horas mais ocupadas do dia e assim por diante. Você também pode detectar anomalias, como quais salas ficam mais abafadas ou quentes ou se alguma área do seu prédio está enviando valores de temperatura consistentemente altos, indicando que o ar-condicionado está com defeito.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Transmitir dados usando os Hubs de Eventos
> * Analisar com o Time Series Insights

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você [configurou](tutorial-facilities-setup.md) e [provisionou](tutorial-facilities-udf.md) sua configuração dos Gêmeos Digitais do Azure. Antes de prosseguir, certifique-se de que você tenha:
- Uma [conta do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma instância de Gêmeos Digitais em execução.
- Os [exemplos de C# dos Gêmeos Digitais](https://github.com/Azure-Samples/digital-twins-samples-csharp) baixados e extraídos do seu computador de trabalho.
- [SDK do .NET Core versão 2.1.403 ou superior](https://www.microsoft.com/net/download) no computador de desenvolvimento para executar o exemplo. Execute `dotnet --version` para verificar se a versão correta está instalada. 


## <a name="stream-data-using-event-hubs"></a>Transmitir dados usando os Hubs de Eventos
O serviço [Hubs de Eventos](../event-hubs/event-hubs-about.md) permite que você crie um pipeline para transmitir os dados. Esta seção mostra como criar seu hub de eventos como o conector entre os Gêmeos Digitais e as instâncias do TSI.

### <a name="create-an-event-hub"></a>Criar um Hub de Evento

1. Entre no [Portal do Azure](https://portal.azure.com).

1. No painel de navegação à esquerda, clique em **Criar um recurso**. 

1. Pesquise e selecione **Hubs de Eventos**. Clique em **Criar**.

1. Insira um **Nome** para o namespace do Hubs de Eventos, escolha *Standard* como **Tipo de preço**, sua **Assinatura**, o **Grupo de recursos** usado para sua instância de Gêmeos Digitais e o **Local**. Clique em **Criar**. 

1. Uma vez implantado, navegue até o namespace *implantação* dos Hubs de Eventos e clique no namespace sob **RECURSO**.

    ![Namespace do Hub de Eventos](./media/tutorial-facilities-analyze/open-event-hub-ns.png)


1. No painel **Visão geral** do namespace dos Hubs de Eventos, clique no botão **Hub de Eventos** na parte superior. 
    ![Hub de Evento](./media/tutorial-facilities-analyze/create-event-hub.png)

1. Insira um **Nome** para o seu hub de eventos e clique em **Criar**. Uma vez implantado, ele aparecerá no painel dos **Hubs de Eventos** do namespace de Hubs de Eventos com um **STATUS** *Ativo*. Clique no hub de eventos para abrir seu painel **Visão geral**.

1. Clique no botão **Grupo de consumidores** na parte superior e digite um nome como *tsievents* para o grupo de consumidores. Clique em **Criar**.
    ![Grupo de consumidores do Hub de Eventos](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)

   Depois de criado, o grupo de consumidores aparecerá na lista, na parte inferior do painel **Visão geral** do hub de eventos. 

1. Abra o painel **Políticas de acesso compartilhadas** do hub de eventos e clique no botão **Adicionar**. **Crie** uma política denominada *ManageSend* e verifique se todas as caixas de seleção estão marcadas. 

    ![Cadeia de conexão do Hub de Eventos](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)

1. Abra a política *ManageSend* que você criou e copie os valores da **Cadeia de conexão – chave primária** e **Cadeia de conexão – chave secundária** para um arquivo temporário. Você precisará desses valores para criar um ponto de extremidade para o hub de eventos na próxima seção.

### <a name="create-endpoint-for-the-event-hub"></a>Criar ponto de extremidade para o hub de eventos

1. Na janela de comando, verifique se você está na pasta **_occupancy quickstart\src** do exemplo dos Gêmeos Digitais.

1. Abra o arquivo **_actions\createEndpoints.yaml_** no editor. Substitua o conteúdo pelo seguinte:

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    ```

1. Substitua os espaços reservados `Primary_connection_string_for_your_event_hub` pelo valor da **Cadeia de conexão – chave primária** para o hub de eventos. Verifique se o formato dessa cadeia de conexão está da seguinte maneira:
```
Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
```

1. Substitua os espaços reservados `Secondary_connection_string_for_your_event_hub` pelo valor da **Cadeia de conexão – chave secundária** para o hub de eventos. Verifique se o formato dessa cadeia de conexão está da seguinte maneira: 
```
Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
```

1. Substitua os espaços reservados `Name_of_your_Event_Hubs_namespace` pelo nome do seu namespace de Hubs de Eventos.

    > [!IMPORTANT]
    > Insira todos os valores sem aspas. Verifique se há pelo menos um caractere de espaço após os dois-pontos no arquivo *YAML*. Você também pode validar o conteúdo do arquivo *YAML* usando qualquer validador YAML online como [essa ferramenta](https://onlineyamltools.com/validate-yaml).


1. Salve e feche o arquivo. Execute o seguinte comando na janela de comando e entre com sua conta do Azure quando solicitado.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```
   
   Ele cria dois pontos de extremidade para seu Hub de Eventos.

   ![Pontos de extremidade para os Hubs de Eventos](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)

## <a name="analyze-with-time-series-insights"></a>Analisar com o Time Series Insights

1. No painel de navegação esquerdo do [portal do Azure](https://portal.azure.com), clique em **Criar um recurso**. 

1. Pesquise e selecione um novo recurso do **Time Series Insights**. Clique em **Criar**.

1. Insira um **Nome** para a instância do Time Series Insights e selecione a **Assinatura**. Selecione o **Grupo de recursos** usado para sua instância de Gêmeos Digitais e seu **Local**. Clique em **Criar**.

    ![Criar o TSI](./media/tutorial-facilities-analyze/create-tsi.png)

1. Depois de implantado, abra o ambiente do Time Series Insights e abra seu painel **Fontes de evento**. Clique no botão **Adicionar** na parte superior para adicionar um grupo de consumidores.

1. No painel **Nova origem do evento**, insira um **Nome** e verifique se os outros valores são selecionados corretamente. Selecione *ManageSend* como o **Nome de política do hub de eventos**, depois selecione o *grupo de consumidores* criado na seção anterior, como o **Grupo de consumidores do hub de eventos**. Clique em **Criar**.

    ![Fonte do evento do TSI](./media/tutorial-facilities-analyze/tsi-event-source.png)

1. Abra o painel **Visão geral** para seu ambiente Time Series Insights e clique no botão **Ir para o ambiente** na parte superior. Se você receber um *aviso de acesso a dados* e depois abrir o painel **Políticas de acesso de dados** da instância do TSI, clique em **Adicionar**, selecione **Colaborador** como a função e selecione o usuário apropriado.

1. O botão **Ir para o ambiente** abre o [Explorer do Time Series Insights](../time-series-insights/time-series-insights-explorer.md). Se ele não mostrar eventos, simule eventos de dispositivo. Para isso, navegue até o projeto **_conectividade do dispositivo_** da amostra de Gêmeos Digitais e execute `dotnet run`.

1. Depois de alguns eventos simulados serem gerados, volte para o explorer do Time Series Insights e clique no botão Atualizar na parte superior. Você deve ver gráficos analíticos sendo criados para seus dados de sensor simulado. 

    ![Explorer do TSI](./media/tutorial-facilities-analyze/tsi-explorer.png)

1. No explorer do Time Series, é possível gerar gráficos e mapas de calor para diferentes eventos e dados de suas salas, sensores e outros recursos. No lado esquerdo, clique nas caixas de lista suspensa denominadas **MEDIDA** e **DIVIDIR POR** para criar suas próprias visualizações. Por exemplo, selecione *Eventos* como a **MEDIDA** e *DigitalTwins-SensorHardwareId* para **DIVIDIR POR** para gerar um mapa de calor para cada um dos sensores, semelhante à imagem a seguir:

    ![Explorer do TSI](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser parar de explorar os Gêmeos Digitais do Azure além desse ponto, fique à vontade para excluir os recursos criados neste tutorial:

1. No menu à esquerda no [portal do Azure](http://portal.azure.com), clique em **Todos os recursos**, selecione o grupo de recursos dos Gêmeos Digitais e **exclua-os**.
2. Se precisar, você também pode excluir os aplicativos de exemplo no seu computador de trabalho. 


## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo para saber mais sobre grafos de inteligência espacial e modelos de objeto nos Gêmeos Digitais do Azure. 
> [!div class="nextstepaction"]
> [Como entender modelos de objeto de Gêmeos Digitais e grafos de inteligência espacial](concepts-objectmodel-spatialgraph.md)

