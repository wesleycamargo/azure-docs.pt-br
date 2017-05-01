---
title: Gerenciamento de dispositivos IoT do Azure com o iothub-explorer | Microsoft Docs
description: "Use a ferramenta da CLI iothub-explorer para o gerenciamento de dispositivos Hub IoT do Azure, que contam com métodos Diretos e as opções de gerenciamento de propriedades desejadas do Gêmeo."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: gerenciamento de dispositivos iot do azure, gerenciamento de dispositivos hub iot do azure, iot de gerenciamento de dispositivos, gerenciamento de dispositivos hub iot
ms.assetid: b34f799a-fc14-41b9-bf45-54751163fffe
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/10/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 530d4549e204a194462fa9fd592fca23060aa27c
ms.lasthandoff: 04/12/2017


---
# <a name="use-iothub-explorer-for-azure-iot-hub-device-management"></a>Usar o iothub-explorer para o gerenciamento de dispositivos Hub IoT do Azure

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

O [iothub-explorer](https://github.com/azure/iothub-explorer) é uma ferramenta da CLI executada em um computador host para gerenciar identidades de dispositivo no Registro do Hub IoT. Ela é fornecida com opções de gerenciamento que podem ser usadas para executar várias tarefas.

| Opção de gerenciamento          | Tarefa                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| Métodos diretos             | Faça com que um dispositivo comece ou pare de enviar mensagens ou seja reinicializado.                                        |
| Propriedades desejadas do gêmeo    | Coloque um dispositivo em alguns estados, como definir um LED como verde ou definir o intervalo de envio de telemetria como 30 minutos.         |
| Propriedades relatadas do gêmeo   | Obtenha o estado relatado de um dispositivo. Por exemplo, o dispositivo relata que o LED está piscando no momento.                                    |
| Marcações do gêmeo                  | Armazene os metadados específicos do dispositivo na nuvem. Por exemplo, o local de implantação de uma máquina de vendas.                         |
| Mensagens da nuvem para o dispositivo   | Envie notificações para um dispositivo. Por exemplo, “É muito provável que chova hoje. Não se esqueça de levar um guarda-chuva”.              |
| Consultas de dispositivo gêmeo        | Consulte todos os dispositivos gêmeos para recuperar aqueles com condições arbitrárias, como identificar os dispositivos que estão disponíveis para uso. |

Para obter explicações mais detalhadas sobre as diferenças e diretrizes sobre como usar essas opções, consulte [Diretrizes de comunicação do dispositivo para a nuvem](iot-hub-devguide-d2c-guidance.md) e [Diretrizes de comunicação da nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md).

> [!NOTE]
> Dispositivos gêmeos são documentos JSON que armazenam informações do estado do dispositivo (metadados, configurações e condições). O Hub IoT persiste um dispositivo gêmeo para cada dispositivo que você conecta a ele. Para obter mais informações sobre dispositivos gêmeos, consulte [Introdução aos dispositivos gêmeos](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>O que você aprenderá

Você aprenderá a usar o iothub-explorer com várias opções de gerenciamento.

## <a name="what-you-do"></a>O que fazer

Execute o iothub-explorer com várias opções de gerenciamento.

## <a name="what-you-need"></a>O que você precisa

- O tutorial [Conectar a ESP8266 ao Hub IoT do Azure](iot-hub-arduino-huzzah-esp8266-get-started.md) foi concluído e abordou os seguintes requisitos:
  - Uma assinatura ativa do Azure.
  - Um hub IoT do Azure em sua assinatura.
  - O aplicativo cliente que envia mensagens para o Hub IoT do Azure.
- iothub-explorer. ([Instalar o iothub-explorer](https://github.com/azure/iothub-explorer))

## <a name="connect-to-your-iot-hub"></a>Conectar ao seu Hub IoT

Conecte-se ao seu Hub IoT executando o seguinte comando:

```bash
iothub-explorer login <your IoT hub connection string>
```

## <a name="use-iothub-explorer-with-direct-methods"></a>Usar o iothub-explorer com métodos diretos

Invoque o método `start` no aplicativo do dispositivo para enviar mensagens para o Hub IoT executando o seguinte comando:

```bash
iothub-explorer device-method <your device Id> start
```

Invoque o método `stop` no aplicativo do dispositivo para parar de enviar mensagens para o Hub IoT executando o seguinte comando:

```bash
iothub-explorer device-method <your device Id> stop
```

## <a name="use-iothub-explorer-with-twins-desired-properties"></a>Usar o iothub-explorer com as propriedades desejadas do gêmeo

Defina um intervalo de propriedade desejado = 3000 executando o seguinte comando:

```bash
iothub-explorer update-twin mydevice {\"properties\":{\"desired\":{\"interval\":3000}}}
```

Essa propriedade pode ser lida pelo dispositivo.

## <a name="use-iothub-explorer-with-twins-reported-properties"></a>Usar o iothub-explorer com as propriedades relatadas do gêmeo

Obtenha as propriedades relatadas do dispositivo executando o seguinte comando:

```bash
iothub-explorer get-twin <your device id>
```

Uma das propriedades é $metadata.$lastUpdated, que mostra a última vez que esse dispositivo enviou ou recebeu uma mensagem.

## <a name="use-iothub-explorer-with-twins-tags"></a>Usar o iothub-explorer com marcações do gêmeo

Exiba as marcações e as propriedades do dispositivo executando o seguinte comando:

```bash
iothub-explorer get-twin <your device id>
```

Adicione uma função de campo = temperature&humidity ao dispositivo executando o seguinte comando:

```bash
iothub-explorer update-twin <your device id> {\"tags\":{\"role\":\"temperature&humidity\"}}
```

## <a name="use-iothub-explorer-with-cloud-to-device-messages"></a>Usar o iothub-explorer para enviar mensagens da nuvem para o dispositivo

Envie uma mensagem “Olá, mundo” para o dispositivo executando o seguinte comando:

```bash
iothub-explorer send <device-id> "Hello World"
```

Consulte [Usar o iothub-explorer para enviar e receber mensagens entre o dispositivo e o Hub IoT](iot-hub-explorer-cloud-device-messaging.md) para obter um cenário real do uso desse comando.

## <a name="use-iothub-explorer-with-device-twins-queries"></a>Usar o iothub-explorer com consultas de dispositivos gêmeos

Consulte dispositivos com uma marcação de função = 'temperature&humidity' executando o seguinte comando:

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Consulte todos os dispositivos, exceto aqueles com uma marcação de função = 'temperature&humidity', executando o seguinte comando:

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Próximas etapas

Você aprendeu a usar o iothub-explorer com várias opções de gerenciamento.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
