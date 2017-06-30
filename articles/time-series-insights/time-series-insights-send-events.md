---
title: "Enviar eventos para um ambiente de Análise de Séries Temporais do Azure | Microsoft Docs"
description: "Este tutorial aborda como enviar eventos para seu ambiente de Análise de Séries Temporais"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: d7c01e18355b66670c9ab7d964f5cdb7ba72bb8f
ms.contentlocale: pt-br
ms.lasthandoff: 06/16/2017

---
# <a name="send-events-to-a-time-series-insights-environment-via-event-hub"></a>Envie eventos para um ambiente de Análise de Séries Temporais através do hub de eventos

Este tutorial explica como criar e configurar o hub de eventos e executa um aplicativo de exemplo para enviar eventos. Se você tiver um hub de eventos existente que já tem eventos no formato JSON, você pode ignorar este tutorial e exibir seu ambiente no [gerenciador de séries temporais](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Configurar um hub de eventos
1. Para criar um hub de eventos, siga as instruções na [documentação](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) sobre Hub de Eventos.

2. Certifique-se de criar um grupo de consumidores que é usado exclusivamente pela sua origem de evento de Análise de Séries Temporais.

  > [!IMPORTANT]
  > Verifique se esse grupo de consumidores não é usado por qualquer outro serviço (como um trabalho do Stream Analytics ou outro ambiente de Análise de Séries Temporais). Se o grupo de consumidores é usado por outros serviços, a operação de leitura é prejudicada para esse ambiente e outros serviços. Se você estiver usando "$Default" como o grupo de consumidores, isso pode levar à potencial reutilização por outros leitores.

  ![Selecione o grupo de consumidores do hub de eventos](media/send-events/consumer-group.png)

3. No hub de eventos, crie "MySendPolicy" que é usado para enviar eventos no exemplo abaixo.

  ![Selecione Políticas de acesso compartilhado e clique no botão Adicionar](media/send-events/shared-access-policy.png)  

  ![Adicione uma política de acesso compartilhado](media/send-events/shared-access-policy-2.png)  

## <a name="create-time-series-insights-event-source"></a>Criar origem de evento de Análise de Séries Temporais
1. Se você ainda não criou a origem do evento, siga as instruções especificadas [aqui](time-series-insights-add-event-source.md) para criar uma origem de evento.

2. Especifique "deviceTimestamp" como o nome da propriedade de carimbo de data/hora – esta propriedade é usada como o carimbo de data/hora real no exemplo abaixo. O nome da propriedade de carimbo de data/hora diferencia maiúsculas de minúsculas e os valores devem ter o formato __aaaa-MM-ddTHH:mm:ss.FFFFFFFK__ quando enviado como JSON ao hub de eventos. Se a propriedade não existe no evento, a hora em que o evento foi enfileirado para o hub de eventos é usada.

  ![Criar uma origem de eventos](media/send-events/event-source-1.png)

## <a name="run-sample-code-to-push-events"></a>Executar o código de exemplo para enviar eventos
1. Vá para a política de hub de eventos "MySendPolicy" e copie a cadeia de conexão com a chave da política.

  ![Copie a cadeia de conexão MySendPolicy](media/send-events/sample-code-connection-string.png)

2. Execute o seguinte código que enviará 600 eventos para cada um dos três dispositivos. Atualize `eventHubConnectionString` com a sua cadeia de conexão.

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
    "deviceId":"device1",
    "deviceTimestamp":"2016-01-08T01:08:00Z"
}
```
#### <a name="output---1-event"></a>Saída - 1 evento

|deviceId|deviceTimestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>Exemplo 2

#### <a name="input"></a>Entrada
Uma matriz JSON com dois objetos JSON. Cada objeto JSON será convertido em um evento.
```json
[
    {
        "deviceId":"device1",
        "deviceTimestamp":"2016-01-08T01:08:00Z"
    },
    {
        "deviceId":"device2",
        "deviceTimestamp":"2016-01-17T01:17:00Z"
    }
]
```
#### <a name="output---2-events"></a>Saída - 2 eventos

|deviceId|deviceTimestamp|
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
            "deviceId":"device1",
            "deviceTimestamp":"2016-01-08T01:08:00Z"
        },
        {
            "deviceId":"device2",
            "deviceTimestamp":"2016-01-17T01:17:00Z"
        }
    ]
}

```
#### <a name="output---2-events"></a>Saída - 2 eventos
Observe que a propriedade "location" é copiada para cada evento.

|location|events.deviceId|events.deviceTimestamp|
|--------|---------------|----------------------|
|Oeste dos EUA|device1|2016-01-08T01:08:00Z|
|Oeste dos EUA|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>Exemplo 4

#### <a name="input"></a>Entrada

Um objeto JSON com uma matriz JSON aninhada que contém dois objetos JSON. Essa entrada demonstra que as propriedades globais podem ser representadas pelo objeto JSON complexo.

```json
{
    "location":"WestUs",
    "manufacturerInfo":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "deviceId":"device1",
            "deviceTimestamp":"2016-01-08T01:08:00Z",
            "deviceData":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "deviceId":"device2",
            "deviceTimestamp":"2016-01-17T01:17:00Z",
            "deviceData":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```
#### <a name="output---2-events"></a>Saída - 2 eventos

|location|manufacturerInfo.name|manufacturerInfo.location|events.deviceId|events.deviceTimestamp|events.deviceData.type|events.deviceData.units|events.deviceData.value|
|---|---|---|---|---|---|---|---|
|Oeste dos EUA|manufacturer1|Leste dos EUA|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|Oeste dos EUA|manufacturer1|Leste dos EUA|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Próximas etapas

* Exibir seu ambiente no [Portal de Análise de Séries Temporais](https://insights.timeseries.azure.com)

