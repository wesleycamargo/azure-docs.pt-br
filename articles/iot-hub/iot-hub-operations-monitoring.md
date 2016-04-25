<properties
 pageTitle="Monitoramento de operações do Hub IoT"
 description="Uma visão geral do monitoramento de operações do Hub IoT do Azure, permitindo que usuários monitorem o status das operações no respectivo Hub IoT em tempo real"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="nberdy"/>

# Introdução ao monitoramento de operações

O monitoramento de operações do Hub IoT permite aos usuários monitorar o status das operações no respectivo Hub IoT em tempo real. O Hub IoT controla eventos em várias categorias de operações e os usuários podem aceitar ter eventos de uma ou mais categorias enviados para um ponto de extremidade de seu Hub IoT para processamento. Os usuários podem monitorar os dados em busca de erros ou configurar processamento mais complexo com base nos padrões de dados.

O Hub IoT monitora quatro categorias de evento:

- Operações de identidade do dispositivo
- Telemetria de dispositivo
- Comandos da nuvem para o dispositivo
- Conexões

## Como habilitar o monitoramento de operações

1. Crie um Hub IoT. Você pode encontrar instruções sobre como criar um Hub IoT no guia de [Introdução][lnk-get-started].

2. Abra a folha do seu Hub IoT. Nela, clique em **Todas as configurações** e em **Monitoramento de operações**.

    ![][1]

3. Selecione as categorias de monitoramento que deseja monitorar e clique em **Salvar**. Os eventos estão disponíveis para leitura no ponto de extremidade compatível com o Hub do Evento listado em **Configurações de monitoramento**. O ponto de extremidade do Hub IoT é chamado de `messages/operationsmonitoringevents`.

    ![][2]

## Categorias de evento e como usá-las

Cada categoria de monitoramento de operações rastreia um tipo diferente de interação com o Hub IoT e cada categoria de monitoramento tem um esquema que define como os eventos nessa categoria são estruturados.

### Operações de identidade do dispositivo

A categoria de operações de identidade do dispositivo rastreia erros que ocorrem quando o usuário tenta criar, atualizar ou excluir uma entrada no registro de identidade do seu Hub IoT. O rastreamento dessa categoria é útil em cenários de provisionamento.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": “userAgent”,
         "sharedAccessPolicy": "accessPolicy"
    }

### Telemetria de dispositivo

A categoria de telemetria de dispositivo rastreia erros que ocorrem no Hub IoT e estão relacionados ao pipeline de telemetria. Isso inclui erros que ocorrem no envio de eventos de telemetria (como limitação) e no recebimento de eventos de telemetria (como leitor não autorizado). Observe que essa categoria não pode capturar erros causados pelo código em execução no próprio dispositivo.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{"scope":"device","type":"sas","issuer":"iothub"}",
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

### Comandos da nuvem para o dispositivo

A categoria de comandos da nuvem para o dispositivo rastreia erros que ocorrem no Hub IoT e estão relacionados ao pipeline do comando do dispositivo. Isso inclui erros que ocorrem no envio de comandos (como remetente não autorizado), no recebimento de comandos (como contagem de entrega excedida) e nos comentários de recebimento de comando (como comentário expirado). Essa categoria não captura erros de um dispositivo que manipula incorretamente um comando se o comando foi entregue com êxito.

    {
         "messageSizeInBytes": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
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

### Conexões

A categoria de conexões rastreia erros quando os dispositivos se conectam ou desconectam de um Hub IoT. Rastrear essa categoria é útil para identificar tentativas de conexão não autorizada e para saber quando dispositivos em áreas de conectividade ruim perdem conexão.

    {
         "durationMs": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
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

## Próximas etapas

Depois dessa visão geral do monitoramento de operações, siga estes links para saber mais:

- [Métricas de diagnóstico do Hub IoT][lnk-diagnostic-metrics]
- [Dimensionamento do Hub IoT][lnk-scaling]
- [Alta disponibilidade e recuperação de desastres do Hub IoT][lnk-dr]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

<!---HONumber=AcomDC_0413_2016-->