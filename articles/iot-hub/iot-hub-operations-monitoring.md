---
title: "Monitoramento de operações de Hub IoT do Azure | Microsoft Docs"
description: "Como usar o monitoramento das operações do Hub IoT do Azure para monitorar o status das operações no seu Hub IoT em tempo real."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a299f3a5-b14d-4586-9c3b-44aea14ed013
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 8f72f2ca66a5d1394e87c7c0f8d8dff9da73732f
ms.openlocfilehash: 612ef94efb9776ae0ce768de1b59fb208824da93


---
# <a name="iot-hub-operations-monitoring"></a>Monitoramento de operações do Hub IoT
O monitoramento das operações do Hub IoT permite monitorar o status das operações no seu Hub IoT em tempo real. O Hub IoT controla eventos em várias categorias de operações. Você pode aceitar o envio de eventos de uma ou mais categorias para um ponto de extremidade do seu Hub IoT para processamento. É possível monitorar os dados em busca de erros ou configurar processamento mais complexo com base nos padrões de dados.

O Hub IoT monitora seis categorias de eventos:

* Operações de identidade do dispositivo
* Telemetria de dispositivo
* Mensagens da nuvem para o dispositivo
* Conexões
* Carregamentos de arquivos
* Roteamento de mensagem

## <a name="how-to-enable-operations-monitoring"></a>Como habilitar o monitoramento de operações
1. Crie um Hub IoT. Você pode encontrar instruções sobre como criar um Hub IoT no guia de [Introdução][lnk-get-started].
2. Abra a folha do seu Hub IoT. Nela, clique em **Monitoramento de operações**.
   
    ![][1]
3. Escolha as categorias de monitoramento que deseja monitorar e clique em **Salvar**. Os eventos estão disponíveis para leitura no ponto de extremidade compatível com o Hub de Eventos listado em **Configurações de monitoramento**. O ponto de extremidade do Hub IoT chama-se `messages/operationsmonitoringevents`.
   
    ![][2]

> [!NOTE]
> A seleção do monitoramento **Detalhado** para a categoria **Conexões** faz com que o Hub IoT gere mensagens de diagnóstico adicionais. Para todas as outras categorias, a configuração **Detalhada** muda a quantidade de informações incluídas pelo Hub IoT em cada mensagem de erro.

## <a name="event-categories-and-how-to-use-them"></a>Categorias de evento e como usá-las
Cada categoria de monitoramento de operações rastreia um tipo diferente de interação com o Hub IoT e cada categoria de monitoramento tem um esquema que define como os eventos nessa categoria são estruturados.

### <a name="device-identity-operations"></a>Operações de identidade do dispositivo
A categoria de operações de identidade do dispositivo rastreia erros que ocorrem quando você tenta criar, atualizar ou excluir uma entrada no registro de identidade do seu Hub IoT. O rastreamento dessa categoria é útil em cenários de provisionamento.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": "userAgent",
         "sharedAccessPolicy": "accessPolicy"
    }

### <a name="device-telemetry"></a>Telemetria de dispositivo
A categoria de telemetria de dispositivo rastreia erros que ocorrem no Hub IoT e estão relacionados ao pipeline de telemetria. Essa categoria inclui erros que ocorrem no envio de eventos de telemetria (como limitação) e no recebimento de eventos de telemetria (como leitor não autorizado). Observe que essa categoria não pode capturar erros causados pelo código em execução no próprio dispositivo.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### <a name="cloud-to-device-commands"></a>Comandos da nuvem para o dispositivo
A categoria de comandos da nuvem para o dispositivo rastreia erros que ocorrem no Hub IoT e são relacionados ao pipeline de mensagem da nuvem para o dispositivo. Essa categoria inclui erros que ocorrem no envio (tal como remetente não autorizado) e recebimento (como contagem de entrega excedida) de mensagens da nuvem para o dispositivo e no recebimento de comentários de mensagens da nuvem para o dispositivo (como comentário expirado). Essa categoria não capturará erros de um dispositivo que manipula incorretamente uma mensagem da nuvem para o dispositivo se tal mensagem tiver sido entregue com êxito.

    {
         "messageSizeInBytes": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### <a name="connections"></a>Conexões
A categoria de conexões rastreia erros que ocorrem quando os dispositivos se conectam a um Hub IoT ou se desconectam dele. Rastrear essa categoria é útil para identificar tentativas de conexão não autorizada e para saber quando dispositivos em áreas de conectividade ruim perdem conexão.

    {
         "durationMs": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

### <a name="file-uploads"></a>Carregamentos de arquivos

A categoria de carregamento de arquivo rastreia erros que ocorrem no Hub IoT e estão relacionados à funcionalidade de carregamento de arquivo. Essa categoria inclui:

- Erros que ocorrem com o URI de SAS, como quando ele expirar antes que um dispositivo notifique o hub de um carregamento concluído.
- Carregamentos com falha relatados pelo dispositivo.
- Erros que ocorrem quando um arquivo não for encontrado no armazenamento durante a criação de mensagem de notificação do Hub IoT.

Observe que essa categoria não pode capturar erros que ocorrem diretamente enquanto o dispositivo estiver carregando um arquivo no armazenamento.


    {
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "HTTP",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "fileUpload",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "blobUri": "http//bloburi.com",
         "durationMs": 1234
    }

### <a name="message-routing"></a>Roteamento de mensagem
A categoria de roteamento de mensagem acompanha os erros que ocorrem durante a avaliação da rota da mensagem e da integridade do ponto de extremidade, conforme visto pelo Hub IoT. Essa categoria inclui eventos, por exemplo, quando uma regra é avaliada como "não definida", quando o Hub IoT marca um ponto de extremidade como inativo e todos os outros erros são recebidos de um ponto de extremidade. Observe que essa categoria não inclui erros específicos sobre as próprias mensagens (como erros de limitação de dispositivo), que são relatados na categoria "telemetria de dispositivo".
        
    {
        "messageSizeInBytes": 1234,
        "time": "UTC timestamp",
        "operationName": "ingress",
        "category": "routes",
        "level": "Error",
        "deviceId": "device-ID",
        "messageId": "ID of message",
        "routeName": "myroute",
        "endpointName": "myendpoint",
        "details": "ExternalEndpointDisabled"
    }

## <a name="next-steps"></a>Próximas etapas
Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT][lnk-devguide]
* [Simulando um dispositivo com o SDK do Gateway do IoT][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Feb17_HO2-->


