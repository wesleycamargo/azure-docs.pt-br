---
title: Entrada de telemetria e de conectividade do dispositivo com Gêmeos Digitais do Azure | Microsoft Docs
description: Visão geral da integração de um dispositivo com Gêmeos Digitais do Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 61d05a7e9936a0edd17c5528ce4f55233b6e7e0e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323660"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Conectividade do dispositivo e entrada de telemetria

Os dados de telemetria enviados pelos dispositivos e sensores formam o backbone de qualquer solução de IoT. Como tal, representar esses recursos diferentes e gerenciá-los dentro do contexto de um local é a principal preocupação no desenvolvimento de aplicativos de IoT. O recurso Gêmeos Digitais do Azure simplifica o desenvolvimento de soluções de IoT unindo dispositivos e sensores com um grafo de inteligência espacial.

Para começar, um recurso `IoTHub` deverá ser criado na raiz do grafo espacial, permitindo que todos os dispositivos sob o espaço de raiz enviem mensagens. Depois que o Hub IoT tiver sido criado, e dispositivos com sensores tiverem sido registrados na instância de Gêmeos Digitais, os dispositivos poderão começar a enviar dados para um serviço de Gêmeos Digitais por meio do [SDK de Dispositivo de IoT do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-device-sdks).

Um guia passo a passo para a integração de dispositivos pode ser encontrado no [Tutorial para implantar e configurar Gêmeos Digitais](tutorial-facilities-setup.md). Em uma visão geral, as etapas são:

- Implantar uma instância de Gêmeos Digitais do Azure usando o [portal do Azure](https://portal.azure.com)
- Criar espaços no grafo
- Criar um recurso `IoTHub` e atribuí-lo a um espaço no seu grafo
- Criar dispositivos e sensores no grafo e atribuí-los aos espaços criados nas etapas acima
- Criar um correspondente para filtrar mensagens de telemetria com base em condições
- Criar uma [**função definida pelo usuário**](concepts-user-defined-functions.md) e atribuí-la a um espaço no grafo para processamento personalizado de suas mensagens de telemetria
- Atribuir uma função para permitir que a função definida pelo usuário acesse os dados do grafo
- Obter a cadeia de conexão do dispositivo do Hub IoT das APIs de Gerenciamento de Gêmeos Digitais
- Configurar a cadeia de conexão do dispositivo no dispositivo com o SDK do Dispositivo IoT do Azure

A seguir, você aprenderá a obter a cadeia de conexão de dispositivo do Hub IoT da API de Gerenciamento de Gêmeos Digitais e como usar o formato de mensagem de telemetria do Hub IoT para enviar telemetria com base em sensor. Os Gêmeos Digitais exigem que cada parte da telemetria que ele recebe seja associada a um sensor de dentro do grafo espacial para garantir que os dados sejam processados e encaminhados dentro do contexto espacial apropriado.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Obter a cadeia de conexão de dispositivo do Hub IoT da API de Gerenciamento

Fazer uma chamada GET na API do Dispositivo com um parâmetro `includes=ConnectionString` para obter a cadeia de conexão do dispositivo do Hub IoT. Você pode filtrar por GUID do dispositivo ou ID de hardware para localizar o dispositivo fornecido:

```plaintext
https://yourManagementApiUrl/api/v1.0/devices/yourDeviceGuid?includes=ConnectionString
```

```plaintext
https://yourManagementApiUrl/api/v1.0/devices?hardwareIds=yourDeviceHardwareId&includes=ConnectionString
```

| Nome do Atributo Personalizado | Substitua por |
| --- | --- |
| `yourManagementApiUrl` | O caminho da URL completo para a API de Gerenciamento |
| `yourDeviceGuid` | A ID do dispositivo |
| `yourDeviceHardwareId` | A ID de hardware do dispositivo |

No payload da resposta, copie a propriedade `connectionString` do dispositivo, que você usará ao chamar o SDK do Dispositivo IoT do Azure para enviar dados de Gêmeos Digitais do Azure.

## <a name="device-to-cloud-message"></a>Mensagens do dispositivo para a nuvem

Você pode personalizar o payload e o formato de mensagem do dispositivo para atender às necessidades da sua solução. Você pode usar qualquer contrato de dados que possa ser serializado em uma matriz de bytes ou fluxo compatível com a [classe de Mensagem de Cliente de Dispositivo do Azure IoT, Message(byte[] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). A mensagem pode ser um formato binário personalizado de sua escolha, desde que você decodifique o contrato de dados em uma Função Definida pelo Usuário correspondente. O único requisito para uma mensagem de Dispositivo para Nuvem é manter um conjunto de propriedades (veja abaixo) para garantir que sua mensagem seja roteada adequadamente para o mecanismo de processamento.

### <a name="telemetry-properties"></a>Propriedades de telemetria

Embora o conteúdo do payload de um `Message` possa ter dados arbitrários de até 256 KB, há alguns requisitos quanto às [Message.Properties](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) esperadas. As etapas descritas abaixo refletem as propriedades obrigatórias e opcionais com suporte do sistema:

| Nome da Propriedade | Valor | Obrigatório | DESCRIÇÃO |
|---|---|---|---|
| DigitalTwins-Telemetry | 1.0 | Sim | Um valor constante que identifica uma mensagem para o sistema |
| DigitalTwins-SensorHardwareId | `string(72)` | Sim | Um identificador exclusivo do sensor que está enviando `Message`. Esse valor deve corresponder à propriedade `HardwareId` do objeto para o sistema processá-la. Por exemplo, `00FF0643BE88-CO2` |
| CreationTimeUtc | `string` | não | Uma cadeia de caracteres formatada em [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) identificando a hora de amostragem do payload. Por exemplo, `2018-09-20T07:35:00.8587882-07:00` |
| CorrelationId | `string` | não | Um `uuid` que pode ser usado para rastrear eventos em todo o sistema. Por exemplo, `cec16751-ab27-405d-8fe6-c68e1412ce1f`

### <a name="sending-your-message-to-digital-twins"></a>Como enviar sua mensagem para Gêmeos Digitais

Use a chamada DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) ou [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) para enviar a mensagem ao seu serviço de Gêmeos Digitais.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o processamento de dados de Gêmeos Digitais do Azure e funcionalidades de Funções Definidas pelo Usuário, leia [Processamento de dados de Gêmeos Digitais do Azure e funções definidas pelo usuário](concepts-user-defined-functions.md).

