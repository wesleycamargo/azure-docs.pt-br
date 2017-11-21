---
title: Como enviar eventos para um ambiente do Azure Time Series Insights | Microsoft Docs
description: Este tutorial explica como criar e configurar o hub de eventos e executar um aplicativo de exemplo para eventos por push a serem exibidos no Azure Time Series Insights.
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MarkMcGeeAtAquent
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 543fafac63423ab874c6c8e40d91a1ce0f161987
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Envie eventos para um ambiente de Análise de Séries Temporais usando o hub de eventos
Este artigo explica como criar e configurar o hub de eventos e executar um aplicativo de exemplo para eventos por push. Se você tiver um hub de eventos existente com eventos no formato JSON, ignore este tutorial e exibir seu ambiente na [análise de séries temporais](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Configurar um hub de eventos
1. Para criar um hub de eventos, siga as instruções na [documentação](../event-hubs/event-hubs-create.md) sobre Hub de Eventos.

2. Procure **hub de eventos** na barra de pesquisa. Clique em **Hubs de Eventos** na lista retornada.

3. Selecione seu hub de eventos clicando no nome.

4. Em Entidades, na janela de configuração central, clique novamente em **Hubs de Eventos**.

5. Selecione o nome do hub de eventos para configurá-lo.

  ![Selecione o grupo de consumidores do hub de eventos](media/send-events/consumer-group.png)

6. Em **Entidades**, selecione **Grupos de consumidores**.
 
7. Certifique-se de criar um grupo de consumidores que é usado exclusivamente pela sua origem de evento de Análise de Séries Temporais.

   > [!IMPORTANT]
   > Verifique se esse grupo de consumidores não é usado por qualquer outro serviço (como um trabalho do Stream Analytics ou outro ambiente de Análise de Séries Temporais). Se o grupo de consumidores é usado por outros serviços, a operação de leitura é prejudicada para esse ambiente e outros serviços. Se você estiver usando "$Default" como o grupo de consumidores, isso pode levar à potencial reutilização por outros leitores.

8. No cabeçalho **Configurações**, selecione **Compartilhar políticas de acesso**.

9. No hub de eventos, crie **MySendPolicy**, que é usado para enviar eventos no exemplo de csharp.

  ![Selecione Políticas de acesso compartilhado e clique no botão Adicionar](media/send-events/shared-access-policy.png)  

  ![Adicione uma política de acesso compartilhado](media/send-events/shared-access-policy-2.png)  

## <a name="create-time-series-insights-event-source"></a>Criar origem de evento de Análise de Séries Temporais
1. Se você ainda não criou a origem do evento, siga [estas instruções](time-series-insights-how-to-add-an-event-source-eventhub.md) para criar uma origem de evento.

2. Especifique **deviceTimestamp** como o nome da propriedade de carimbo de data/hora – essa propriedade é usada como o carimbo de data/hora real no exemplo C#. O nome da propriedade de carimbo de data/hora diferencia maiúsculas de minúsculas e os valores precisam seguir o formato __aaaa-MM-ddTHH:mm:ss.FFFFFFFK__ quando enviado como JSON ao hub de eventos. Se a propriedade não existir no evento, o tempo de enfileiramento do hub de eventos será usado.

  ![Criar uma origem de eventos](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>Código de exemplo para enviar eventos por push
1. Vá para a política de hub de eventos nomeada **MySendPolicy**. Copie a **cadeia de conexão** com a chave de política.

  ![Copie a cadeia de conexão MySendPolicy](media/send-events/sample-code-connection-string.png)

2. Execute o seguinte código para enviar 600 eventos a cada um dos três dispositivos. Atualize `eventHubConnectionString` com a sua cadeia de conexão.

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.Rdx.DataGenerator
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            var from = new DateTime(2017, 4, 20, 15, 0, 0, DateTimeKind.Utc);
            Random r = new Random();
            const int numberOfEvents = 600;

            var deviceIds = new[] { "device1", "device2", "device3" };

            var events = new List<string>();
            for (int i = 0; i < numberOfEvents; ++i)
            {
                for (int device = 0; device < deviceIds.Length; ++device)
                {
                    // Generate event and serialize as JSON object:
                    // { "deviceTimestamp": "utc timestamp", "deviceId": "guid", "value": 123.456 }
                    events.Add(
                        String.Format(
                            CultureInfo.InvariantCulture,
                            @"{{ ""deviceTimestamp"": ""{0}"", ""deviceId"": ""{1}"", ""value"": {2} }}",
                            (from + TimeSpan.FromSeconds(i * 30)).ToString("o"),
                            deviceIds[device],
                            r.NextDouble()));
                }
            }

            // Create event hub connection.
            var eventHubConnectionString = @"Endpoint=sb://...";
            var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubConnectionString);

            using (var ms = new MemoryStream())
            using (var sw = new StreamWriter(ms))
            {
                // Wrap events into JSON array:
                sw.Write("[");
                for (int i = 0; i < events.Count; ++i)
                {
                    if (i > 0)
                    {
                        sw.Write(',');
                    }
                    sw.Write(events[i]);
                }
                sw.Write("]");

                sw.Flush();
                ms.Position = 0;

                // Send JSON to event hub.
                EventData eventData = new EventData(ms);
                eventHubClient.Send(eventData);
            }
        }
    }
}

```
## <a name="supported-json-shapes"></a>Formas de JSON com suporte
### <a name="sample-1"></a>Exemplo 1

#### <a name="input"></a>Entrada

Um objeto JSON simples.

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
#### <a name="output---1-event"></a>Saída - 1 evento

|ID|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>Exemplo 2

#### <a name="input"></a>Entrada
Uma matriz JSON com dois objetos JSON. Cada objeto JSON será convertido em um evento.
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
#### <a name="output---2-events"></a>Saída - 2 eventos

|ID|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>Exemplo 3

#### <a name="input"></a>Entrada

Um objeto JSON com uma matriz JSON aninhada que contém dois objetos JSON.
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
#### <a name="output---2-events"></a>Saída - 2 eventos
Observe que a propriedade "location" é copiada para cada evento.

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
#### <a name="output---2-events"></a>Saída - 2 eventos

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|Oeste dos EUA|manufacturer1|Leste dos EUA|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|Oeste dos EUA|manufacturer1|Leste dos EUA|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Próximas etapas
Exibir seu ambiente no [Explorador do Time Series Insights](https://insights.timeseries.azure.com).
