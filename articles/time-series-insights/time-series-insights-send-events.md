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
ms.date: 04/09/2018
ms.openlocfilehash: b418d1114cf6b906dcdee46bbf7e094cbc4a0521
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Envie eventos para um ambiente de Análise de Séries Temporais usando o hub de eventos
Este artigo explica como criar e configurar o hub de eventos e executar um aplicativo de exemplo para eventos por push. Se você tiver um hub de eventos existente com eventos no formato JSON, ignore este tutorial e exibir seu ambiente na [Análise de Séries Temporais](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Configurar um hub de eventos
1. Para criar um hub de eventos, siga as instruções na [documentação](../event-hubs/event-hubs-create.md) sobre Hub de Eventos.

2. Procure **hub de eventos** na barra de pesquisa. Clique em **Hubs de Eventos** na lista retornada.

3. Selecione seu hub de eventos clicando no nome.

4. Em **Entidades**, na janela de configuração central, clique novamente em **Hubs de Eventos**.

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

## <a name="add-time-series-insights-reference-data-set"></a>Adicionar conjunto de dados de referência do Azure Time Series Insights 
O uso de dados de referência no TSI contextualiza os dados de telemetria.  Esse contexto adiciona significado aos dados e torna mais fácil filtrar e agregar.  O TSI une dados de referência no tempo de ingresso e não pode unir retroativamente esses dados.  Portanto, é essencial adicionar dados de referência antes de adicionar uma origem do evento com dados.  Dados como localização ou tipo de sensor são dimensões úteis que você pode querer unir a uma ID de dispositivo/marca/sensor para tornar mais fácil fatiar e filtrar.  

> [!IMPORTANT]
> Ter um conjunto de dados de referência configurado é crítico ao fazer upload de dados históricos.

Assegure-se de que você tenha dados de referência no local ao fazer upload em massa de dados históricos para o TSI.  Tenha em mente que o TSI iniciará imediatamente a leitura a partir de uma origem do evento ingressada, se essa origem do evento tiver dados.  É útil aguardar para ingressar em uma origem do evento para o TSI até que você tenha os dados de referência no local, especialmente se essa origem do evento contiver dados. Como alternativa, é possível aguardar para efetuar push de dados para essa origem do evento até que o conjunto de dados de referência esteja no local.

Para gerenciar dados de referência, há a interface do usuário baseada na Web no Gerenciador do TSI e há uma API C# programática. O Gerenciador do TSI tem uma experiência visual do usuário para upload de arquivos ou colar conjuntos de dados de referência existentes como formato JSON ou CSV. Com a API, é possível criar um aplicativo personalizado quando necessário.

Para obter mais informações sobre gerenciamento de dados de referência no Time Series Insights, consulte o [artigo de dados de referência](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

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
#### <a name="output---one-event"></a>Saída - um evento

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
#### <a name="output---two-events"></a>Saída - dois eventos

|ID|timestamp|
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
#### <a name="output---two-events"></a>Saída - dois eventos
Observe que a propriedade "location" é copiada para cada um dos eventos.

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
#### <a name="output---two-events"></a>Saída - dois eventos

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|Oeste dos EUA|manufacturer1|Leste dos EUA|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|Oeste dos EUA|manufacturer1|Leste dos EUA|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

### <a name="json-shaping-strategies"></a>Estratégias de formatação JSON
Vamos usar o seguinte exemplo de um evento como ponto de partida e discutir os problemas relacionados, assim como as estratégias para atenuar esses problemas.

#### <a name="payload-1"></a>Carga 1:
```json
[{
            "messageId": "LINE_DATA",
            "deviceId": "FXXX",
            "timestamp": 1522355650620,
            "series": [{
                        "chId": 3,
                        "value": -3750.0
                  }, {
                        "chId": 13,
                        "value": 0.58015072345733643
                  }, {
                        "chId": 11,
                        "value": 800.0
                  }, {
                        "chId": 21,
                        "value": 0.0
                  }, {
                        "chId": 14,
                        "value": -999.0
                  }, {
                        "chId": 37,
                        "value": 2.445906400680542
                  }, {
                        "chId": 39,
                        "value": 0.0
                  }, {
                        "chId": 40,
                        "value": 1.0
                  }, {
                        "chId": 1,
                        "value": 1.0172575712203979
                  }
            ],
            "EventProcessedUtcTime": "2018-03-29T20:36:21.3245900Z",
            "PartitionId": 2,
            "EventEnqueuedUtcTime": "2018-03-29T20:34:11.0830000Z",
            "IoTHub": {
                  "MessageId": "<17xxx2xx-36x0-4875-9x1x-x428x41x1x68>",
                  "CorrelationId": "<x253x5xx-7xxx-4xx3-91x4-xxx3bx2xx0x3>",
                  "ConnectionDeviceId": "AAAA-ZZ-001",
                  "ConnectionDeviceGenerationId": "<123456789012345678>",
                  "EnqueuedTime": "2018-03-29T20:34:10.7990000Z",
                  "StreamId": null
            }
      }
]
 ```

Se você enviar essa matriz de eventos como uma carga para o TSI, ela será armazenada como um evento para cada valor de medida. Isso pode criar um excesso de eventos, que pode não ser ideal. Observe que é possível usar dados de referência no TSI para adicionar nomes significativos como propriedades.  Por exemplo, é possível criar um conjunto de dados de referência com Propriedade de Chave = chId:  

chId  Unidade de Medida 24    Pressão de óleo do motor   PSI 25    CALC Taxa de Bomba        bbl/min

Para obter mais informações sobre gerenciamento de dados de referência no Time Series Insights, consulte o [artigo de dados de referência](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

Outro problema com a primeira carga é que o carimbo de data/hora está em milissegundos. O TSI aceita apenas carimbos de data/hora formatados em ISO. Uma solução é deixar o comportamento de carimbo de data/hora padrão no TSI, que é usar o carimbo de data/hora enfileirado.

Como alternativa à carga acima, vejamos outro exemplo.  

#### <a name="payload-2"></a>Carga 2:
```json
{
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "STATE Engine State": 1,
      "unit": "NONE"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "MPC_AAAA-ZZ-001",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Well Head Px 1": -494162.8515625,
      "unit": "psi"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate": 0,
      "unit": "bbl/min"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Engine Fuel Pressure": 0,
      "unit": "psi"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Engine Oil Pressure": 0.58015072345733643,
      "unit": "psi"
}
```

Como a Carga 1, o TSI armazenará cada valor medido como um evento exclusivo.  A diferença notável é que o TSI fará a leitura do *carimbo de data/hora* corretamente como aqui, de acordo com a ISO.  

Se for necessário reduzir o número de eventos enviados, você poderá enviar as informações conforme a seguir.  

#### <a name="payload-3"></a>Carga 3:
```json
{
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate": 0,
      "CALC Pump Rate.unit": "bbl/min"
      "Engine Oil Pressure": 0.58015072345733643,
      "Engine Oil Pressure.unit": "psi"
      "Engine Fuel Pressure": 0,
      "Engine Fuel Pressure.unit": "psi"
}
```
Uma sugestão final está abaixo.

#### <a name="payload-4"></a>Carga 4:
```json
{
              "line": "Line01",
              "station": "Station 11",
              "gatewayid": "AAAA-ZZ-001",
              "deviceid": "F12XX",
              "timestamp": "2018-03-29T20:34:15.0000000Z",
              "CALC Pump Rate": {
                           "value": 0,
                           "unit": "bbl/min"
              },
              "Engine Oil Pressure": {
                           "value": 0.58015072345733643,
                           "unit": "psi"
              },
              "Engine Fuel Pressure": {
                           "value": 0,
                           "unit": "psi"
              }
}
```

Este exemplo mostra a saída após nivelar o JSON:

```json
{
      "line": "Line01",
      "station": "Station 11",,
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate.value": 0,
      "CALC Pump Rate.unit": "bbl/min"
      "Engine Oil Pressure.value": 0.58015072345733643,
      "Engine Oil Pressure.unit": "psi"
      "Engine Fuel Pressure.value": 0,
      "Engine Fuel Pressure.unit": "psi"
}
```

Você tem a liberdade de definir propriedades diferentes para cada um dos canais dentro de seu próprio objeto json, mantendo a contagem de eventos baixa. Essa abordagem nivelada ocupa mais espaço, o que é importante considerar. A capacidade do TSI é baseada tanto em eventos como em tamanho, o que ocorrer primeiro.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Exibir seu ambiente no Explorador do Time Series Insights](https://insights.timeseries.azure.com).
