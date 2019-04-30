---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 07/26/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 414bb0183e68cb46e52c379ea3f7aceda5d4170e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61446089"
---
## <a name="the-parts-of-the-device-model-schema"></a>As partes do esquema de modelo do dispositivo

Cada modelo do dispositivo, como um resfriador ou caminhão, define um tipo de dispositivo para que o serviço de simulação pode simular. Cada modelo do dispositivo é armazenado em um arquivo JSON com o seguinte esquema de nível superior:

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "elevator-01",
  "Version": "0.0.1",
  "Name": "Elevator",
  "Description": "Elevator with floor, vibration and temperature sensors.",
  "Protocol": "AMQP",
  "Simulation": {
    // Specify the simulation behavior
  },
  "Properties": {
    // Define properties
  },
  "Telemetry": [
    // Specify telemetry
  ],
  "CloudToDeviceMethods": {
    // Specify methods
  }
}
```

É possível exibir os arquivos de esquema para os dispositivos simulados padrão na [pasta devicemodels](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) no GitHub.

A tabela a seguir descreve as entradas de esquema de nível superior:

| Entrada de esquema | DESCRIÇÃO |
| -- | --- |
| `SchemaVersion` | A versão do esquema sempre é `1.0.0` e específica para o formato desse arquivo. |
| `Id` | Uma ID exclusiva para esse modelo do dispositivo. |
| `Version` | Identifica a versão do modelo do dispositivo. |
| `Name` | Um nome amigável para o modelo do dispositivo. |
| `Description` | Uma descrição do modelo do dispositivo. |
| `Protocol` | O protocolo de conexão que o dispositivo usa. Pode ser um `AMQP`, `MQTT` e `HTTP`. |

As seções a seguir descrevem as outras seções no esquema JSON:

## <a name="simulation"></a>Simulação

Na `Simulation` seção, você define o estado interno do dispositivo simulado. Todos os valores de telemetria enviados pelo dispositivo devem ser parte desse estado do dispositivo.

A definição do estado do dispositivo tem dois elementos:

* `InitialState` define valores iniciais para todas as propriedades do objeto de estado do dispositivo.
* `Script` identifica um arquivo JavaScript que executa em uma programação para atualizar o estado do dispositivo. É possível usar esse arquivo de script para randomizar os valores de telemetria enviados pelo dispositivo.

Para saber mais sobre o arquivo JavaScript que atualiza o objeto de estado do dispositivo, consulte [Compreender o comportamento do modelo do dispositivo](../articles/iot-accelerators/iot-accelerators-device-simulation-device-behavior.md).

O exemplo a seguir mostra a definição do objeto de estado do dispositivo para um dispositivo resfriador simulado:

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

O serviço de simulação executa o arquivo **chiller-01-state.js** a cada cinco segundos para atualizar o estado do dispositivo. É possível ver os arquivos JavaScript para os dispositivos simulados padrão na [pasta de scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) no GitHub. Por convenção, esses arquivos JavaScript têm o sufixo **-state** para distingui-los dos arquivos que implementam comportamentos do método.

## <a name="properties"></a>propriedades

A seção `Properties` do esquema define os valores de propriedade que o dispositivo informa sobre a solução. Por exemplo: 

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

Quando a solução inicia, todos os dispositivos simulados são consultados para compilar uma lista de valores `Type` para usar na interface do usuário. A solução usa as propriedades `Latitude` e `Longitude` para adicionar o local do dispositivo ao mapa no painel de controle.

## <a name="telemetry"></a>Telemetria

A matriz `Telemetry` lista todos os tipos de telemetria que o dispositivo simulado envia para a solução.

O exemplo a seguir envia uma mensagem de telemetria JSON a cada 10 segundos com dados `floor`, `vibration` e `temperature` dos sensores do elevador:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate": "{\"floor\":${floor},\"vibration\":${vibration},\"vibration_unit\":\"${vibration_unit}\",\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
    "MessageSchema": {
      "Name": "elevator-sensors;v1",
      "Format": "JSON",
      "Fields": {
        "floor": "integer",
        "vibration": "double",
        "vibration_unit": "text",
        "temperature": "double",
        "temperature_unit": "text"
      }
    }
  }
]
```

`MessageTemplate` define a estrutura da mensagem JSON enviada pelo dispositivo simulado. Os espaços reservados em `MessageTemplate` usam a sintaxe `${NAME}` onde `NAME` é uma chave do [objeto de estado do dispositivo](#simulation). As cadeias de caracteres deverão ser cotadas, os números não deverão.

`MessageSchema` define o esquema da mensagem enviada pelo dispositivo simulado. O esquema de mensagens também é publicado no Hub IoT para permitir que os aplicativos back-end reutilizem as informações para interpretar a telemetria recebida.

Atualmente, é possível usar somente os esquemas de mensagens JSON. Os campos listados no esquema podem ser dos tipos a seguir:

* Objeto - serializado usando JSON
* Binário - serializado usando base64
* Text
* Boolean
* Número inteiro
* Double
* DateTime

Para enviar mensagens de telemetria em intervalos diferentes, adicione vários tipos de telemetria à matriz `Telemetry`. O exemplo a seguir envia dados de temperatura e umidade a cada 10 segundos e o estado da luz a cada minuto:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate":
      "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
    "MessageSchema": {
      "Name": "RoomComfort;v1",
      "Format": "JSON",
      "Fields": {
        "temperature": "double",
        "temperature_unit": "text",
        "humidity": "integer"
      }
    }
  },
  {
    "Interval": "00:01:00",
    "MessageTemplate": "{\"lights\":${lights_on}}",
    "MessageSchema": {
      "Name": "RoomLights;v1",
      "Format": "JSON",
      "Fields": {
        "lights": "boolean"
      }
    }
  }
],
```

## <a name="cloudtodevicemethods"></a>CloudToDeviceMethods

Um dispositivo simulado pode responder a métodos de nuvem para dispositivo chamados de um hub IoT. A seção `CloudToDeviceMethods` no arquivo de esquema de modelo do dispositivo:

* Define os métodos aos quais o dispositivo simulado pode responder.
* Identifica o arquivo JavaScript que contém a lógica a ser executada.

O dispositivo simulado envia a lista de métodos que ele suporta para o hub IoT ao qual está conectado.

Para saber mais sobre o arquivo JavaScript que implementa o comportamento do dispositivo, consulte [Compreender o comportamento do modelo do dispositivo](../articles/iot-accelerators/iot-accelerators-device-simulation-device-behavior.md).

O exemplo a seguir especifica três métodos com suporte e os arquivos JavaScript que implementam esses métodos:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

É possível ver os arquivos JavaScript para os dispositivos simulados padrão na [pasta de scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) no GitHub. Por convenção, esses arquivos JavaScript possuem o sufixo **-method** para distingui-los dos arquivos que implementam o comportamento de estado.