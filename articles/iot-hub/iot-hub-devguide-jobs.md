---
title: Entender os trabalhos de Hub IoT do Azure | Microsoft Docs
description: "Guia do desenvolvedor – agendar trabalhos para execução em vários dispositivos conectados ao seu Hub IoT. Os trabalhos podem atualizar marcações e propriedades desejadas e invocar métodos diretos em vários dispositivos."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: fe78458f-4f14-4358-ac83-4f7bd14ee8da
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: juanpere
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: abb7f80662650efa8f158f32125ebc5350cb4f62
ms.contentlocale: pt-br
ms.lasthandoff: 05/31/2017


---
# <a name="schedule-jobs-on-multiple-devices"></a>Agendar trabalhos em vários dispositivos
## <a name="overview"></a>Visão geral
Conforme descrito em artigos anteriores, o Hub IoT do Azure permite diversos blocos de construção ([marcas e propriedades do dispositivo gêmeo][lnk-twin-devguide] e [métodos diretos][lnk-dev-methods]).  Normalmente, os aplicativos back-end permitem que administradores e operadores do dispositivo atualizem e interajam com dispositivos IoT em massa e em um horário agendado.  Os trabalhos encapsulam a execução de atualizações do dispositivo gêmeo e de métodos diretos em um conjunto de dispositivos e em um horário agendado.  Por exemplo, um operador poderia usar um aplicativo back-end que iniciaria e controlaria um trabalho a fim de reinicializar um conjunto de dispositivos no edifício 43, no terceiro andar, em um horário que não interromperia as operações do edifício.

### <a name="when-to-use"></a>Quando usar
Considere o uso de trabalhos quando: uma solução back-end precisar agendar e acompanhar o andamento de qualquer uma das seguintes atividades a seguir em um conjunto de dispositivos:

* Atualizar as propriedades desejadas
* Marcas de atualização
* Chamar métodos diretos

## <a name="job-lifecycle"></a>Ciclo de vida do trabalho
Os trabalhos são iniciados pelo back-end da solução e mantidos pelo Hub IoT.  Você pode iniciar um trabalho por meio de um URI voltado para o serviço (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14`) e consultar o andamento de um trabalho em execução por meio de um URI voltado para o serviço (`{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`).  Após o início de um trabalho, a consulta aos trabalhos permite que o aplicativo back-end atualize o status dos trabalhos em execução.

> [!NOTE]
> Quando você inicia um trabalho, os valores e nomes de propriedade só podem conter caracteres alfanuméricos imprimíveis US-ASCII, exceto pelo seguinte conjunto: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.
> 
> 

## <a name="reference-topics"></a>Tópicos de referência:
Os tópicos de referência a seguir fornecem a você mais informações sobre como usar os trabalhos.

## <a name="jobs-to-execute-direct-methods"></a>Trabalhos para execução de métodos diretos
Veja a seguir os detalhes da solicitação HTTP 1.1 para execução de um [método direto][lnk-dev-methods] em um conjunto de dispositivos usando um trabalho:

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-11-14

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            responseTimeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }
    ```
A condição de consulta também pode ser em uma única ID de dispositivo ou em uma lista de IDs de dispositivos, conforme mostrado abaixo

**Exemplos**
```
queryCondition = "deviceId = 'MyDevice1'"
queryCondition = "deviceId IN ['MyDevice1','MyDevice2']"
queryCondition = "deviceId IN ['MyDevice1']
```
A [Linguagem de consulta de Hub IoT][lnk-query] aborda a linguagem de consulta de Hub IoT em detalhes adicionais.

## <a name="jobs-to-update-device-twin-properties"></a>Trabalhos para atualização das propriedades do dispositivo gêmeo
Veja a seguir os detalhes da solicitação HTTP 1.1 de atualização das propriedades do dispositivo gêmeo usando um trabalho:

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-11-14
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }
    ```

## <a name="querying-for-progress-on-jobs"></a>Consultar o andamento dos trabalhos
Veja a seguir os detalhes da solicitação HTTP 1.1 para [consultar trabalhos][lnk-query]:

    ```
    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>
    ```

O continuationToken é fornecido pela resposta.  

## <a name="jobs-properties"></a>Propriedades dos trabalhos
Veja a seguir uma lista de propriedades e descrições correspondentes que podem ser usadas durante a consulta por trabalhos ou por resultados do trabalho.

| Propriedade | Descrição |
| --- | --- |
| **jobId** |ID fornecida pelo aplicativo para o trabalho. |
| **startTime** |Hora de início fornecida pelo aplicativo (ISO 8601) para o trabalho. |
| **endTime** |Data fornecida pelo Hub IoT (ISO-8601) para a conclusão do trabalho. Válida somente após o trabalho atingir o estado 'concluído'. |
| **tipo** |Tipos de trabalhos: |
| **scheduledUpdateTwin**: Um trabalho usado para atualizar um conjunto de propriedades ou marcas desejadas. | |
| **scheduledDeviceMethod**: um trabalho usado para invocar um método de dispositivo em um conjunto de dispositivos gêmeos. | |
| **status** |Estado atual do trabalho. Valores possíveis para o status: |
| **pendente**: agendado e aguardando ser selecionado pelo serviço do trabalho. | |
| **agendado**: agendado para um horário no futuro. | |
| **executando**: trabalho ativo no momento. | |
| **cancelado**: o trabalho foi cancelado. | |
| **falha**: o trabalho falhou. | |
| **concluído**: o trabalho foi concluído. | |
| **deviceJobStatistics** |Estatísticas sobre a execução do trabalho. |

Propriedades **deviceJobStatistics**.

| Propriedade | Descrição |
| --- | --- |
| **deviceJobStatistics.deviceCount** |Número de dispositivos no trabalho. |
| **deviceJobStatistics.failedCount** |Número de dispositivos nos quais o trabalho falhou. |
| **deviceJobStatistics.succeededCount** |Número de dispositivos nos quais o trabalho teve êxito. |
| **deviceJobStatistics.runningCount** |Número de dispositivos que estão executando o trabalho no momento. |
| **deviceJobStatistics.pendingCount** |Número de dispositivos com execução pendente do trabalho. |

### <a name="additional-reference-material"></a>Material de referência adicional
Outros tópicos de referência no Guia do desenvolvedor do Hub IoT incluem:

* [Pontos de extremidade do Hub IoT][lnk-endpoints] descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de tempo de execução e de gerenciamento.
* [Limitação e cotas][lnk-quotas] descreve as cotas que se aplicam ao serviço Hub IoT e o comportamento de limitação esperado ao usar o serviço.
* [SDKs de dispositivo e serviço do Azure IoT][lnk-sdks] lista os vários SDKs de linguagem que você pode usar no desenvolvimento de aplicativos de dispositivo e de serviço que interagem com o Hub IoT.
* [ A linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens][lnk-query] descreve a linguagem de consulta do Hub IoT que você pode utilizar para recuperar informações do Hub IoT sobre seus dispositivos gêmeos e trabalhos.
* [Suporte ao MQTT do Hub IoT][lnk-devguide-mqtt] fornece mais informações sobre o suporte do Hub IoT para o protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas
Se você quiser experimentar alguns dos conceitos descritos neste artigo, talvez se interesse pelo seguinte tutorial de Hub IoT:

* [Agendar e transmitir trabalhos][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-node-node-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md

