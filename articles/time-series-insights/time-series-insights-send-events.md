---
title: Eventos de envio do Azure Time Series Insights – Enviar eventos para um ambiente do Azure Time Series Insights | Microsoft Docs
description: Saiba como configurar um hub de eventos e executar um aplicativo de exemplo para efetuar push de eventos que podem ser exibidos no Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 69d16292f5b71179ee66fb5f7d6c4a6f11cbb9de
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276138"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Enviar eventos para um ambiente do Time Series Insights usando um hub de eventos

Este artigo explica como criar e configurar um hub de eventos nos Hubs de Eventos do Azure e executar um aplicativo de exemplo para efetuar push de eventos. Se você tiver um hub de eventos existente que tenha eventos no formato JSON, ignore este tutorial e exibir seu ambiente na [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Configurar um hub de eventos

1. Para saber como criar um hub de eventos, confira a [documentação dos Hubs de Eventos](https://docs.microsoft.com/azure/event-hubs/).
1. Na caixa de pesquisa, pesquise **Hubs de Eventos**. Na lista retornada, selecione **Hubs de Eventos**.
1. Selecione seu hub de eventos.
1. Quando você cria um hub de eventos, está na verdade criando um namespace de hub de eventos. Se você ainda não criou um hub de eventos dentro do namespace, crie um hub de eventos em **Entidades** no menu.  

    ![Lista de hubs de eventos][1]

1. Depois de criar um hub de eventos, selecione-o na lista de hubs de eventos.
1. No menu, em **Entidades**, selecione **Hubs de Eventos**.
1. Selecione o nome do hub de eventos para configurá-lo.
1. Em **Entidades**, selecione **Grupos de consumidores** e selecione **Grupo de Consumidores**.

    ![Criar um grupo de consumidores][2]

1. Certifique-se de criar um grupo de consumidores que é usado exclusivamente pela sua origem de evento de Análise de Séries Temporais.

    > [!IMPORTANT]
    > Verifique se esse grupo de consumidores não é usado por qualquer outro serviço (como um trabalho do Azure Stream Analytics ou outro ambiente do Time Series Insights). Se o grupo de consumidores é usado por outros serviços, as operações de leitura são prejudicadas em relação a esse ambiente e a outros serviços. Se você usar **$Default** como o grupo de consumidores, outros leitores poderão vir a reutilizar seu grupo de consumidores.

1. No menu, em **Configurações**, selecione **Políticas de acesso compartilhado** e selecione **Adicionar**.

    ![Selecionar Políticas de acesso compartilhado e selecione o botão Adicionar][3]

1. No painel **Adicionar nova política de acesso compartilhado**, crie um acesso compartilhado denominado **MySendPolicy**. Você usará essa política de acesso compartilhado para enviar eventos nos exemplos em C# mais adiante neste artigo.

    ![Na caixa Nome de política, insira MySendPolicy][4]

1. Em **Declaração**, marque a caixa de seleção **Enviar**.

## <a name="add-a-time-series-insights-instance"></a>Adicionar uma instância do Time Series Insights

A atualização do Time Series Insights usa instâncias para adicionar dados contextuais aos dados de telemetria recebidos. Os dados são unidos no momento da consulta usando uma **ID da série temporal**. A **ID da série temporal** do projeto de moinhos de exemplo que usamos mais adiante neste artigo é **Id**. Para saber mais sobre instâncias do Time Series Insight e **ID da série temporal**, consulte [Modelos do Time Series](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Criar uma origem de evento do Time Series Insights

1. Se você ainda não criou uma origem de evento, conclua as etapas para [criar uma origem de evento](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Defina um valor para `timeSeriesId`. Para saber mais sobre **ID da série temporal**, consulte [Modelos do Time Series](./time-series-insights-update-tsm.md).

### <a name="push-events"></a>Efetuar push de eventos (exemplo de moinhos)

1. Na barra de pesquisa, procure **Hubs de Eventos**. Na lista retornada, selecione **Hubs de Eventos**.

1. Selecione seu hub de eventos.

1. Vá para **Políticas de Acesso Compartilhado** > **RootManageSharedAccessKey**. Copie o valor da **Cadeia de conexão-chave primária**.

    ![Copiar o valor da cadeia de conexão da chave primária][5]

1. Vá para https://tsiclientsample.azurewebsites.net/windFarmGen.html. A URL executa dispositivos de moinho simulados.
1. Na caixa **Cadeia de Conexão do Hub de Eventos** na página da Web, cole a cadeia de conexão que você copiou em [Efetuar push de eventos](#push-events).
  
    ![Colar a cadeia de conexão da chave primária na caixa Cadeia de Conexão do Hub de Eventos][6]

1. Selecione **Clique para iniciar**. O simulador gera uma instância JSON que você pode usar diretamente.

1. Volte ao seu hub de eventos no portal do Azure. Na página **Visão Geral**, você deve ver os novos eventos recebidos pelo hub de eventos:

    ![Uma página de visão geral do hub de eventos que mostra as métricas do hub de eventos][7]

<a id="json"></a>

## <a name="supported-json-shapes"></a>Formas de JSON com suporte

### <a name="sample-1"></a>Exemplo 1

#### <a name="input"></a>Entrada

Um objeto JSON simples:

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```

#### <a name="output-one-event"></a>Saída: Um evento

|ID| timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>Exemplo 2

#### <a name="input"></a>Entrada

Uma matriz JSON com dois objetos JSON. Cada objeto JSON é convertido em um evento.

```json
[
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    },
    {
        "id":"device2",
        "timestamp":"2016-01-17T01:17:00Z"
    }
]
```

#### <a name="output-two-events"></a>Saída: Dois eventos

|ID| timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>Exemplo 3

#### <a name="input"></a>Entrada

Um objeto JSON com uma matriz JSON aninhada que contém dois objetos JSON:

```json
{
    "location":"WestUs",
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
}
```

#### <a name="output-two-events"></a>Saída: Dois eventos

A propriedade **location** é copiada para cada evento.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|Oeste dos EUA|device1|2016-01-08T01:08:00Z|
|Oeste dos EUA|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>Exemplo 4

#### <a name="input"></a>Entrada

Um objeto JSON com uma matriz JSON aninhada que contém dois objetos JSON. Essa entrada demonstra que as propriedades globais podem ser representadas pelo objeto JSON complexo.

```json
{
    "location":"WestUs",
    "manufacturer":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z",
            "data":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z",
            "data":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```

#### <a name="output-two-events"></a>Saída: Dois eventos

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|Oeste dos EUA|manufacturer1|Leste dos EUA|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|Oeste dos EUA|manufacturer1|Leste dos EUA|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exibir seu ambiente no explorer do Time Series Insights](https://insights.timeseries.azure.com)

<!-- Images -->
[1]: media/send-events/updated.png
[2]: media/send-events/consumer-group.png
[3]: media/send-events/shared-access-policy.png
[4]: media/send-events/shared-access-policy-2.png
[5]: media/send-events/sample-code-connection-string.png
[6]: media/send-events/updated_two.png
[7]: media/send-events/telemetry.png
