<properties
 pageTitle="Metadados de informações de dispositivo na solução de monitoramento remoto | Microsoft Azure"
 description="Uma descrição do monitoramento remoto pré-configurado da solução de IoT do Azure e sua arquitetura."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="06/20/2016"
 ms.author="dobett"/>

# Metadados de informações de dispositivo na solução pré-configurada de monitoramento remoto

A solução pré-configurada de monitoramento remoto do Azure IoT Suite demonstra uma abordagem para o gerenciamento de metadados de dispositivo. Este artigo descreve a abordagem utilizada por essa solução para que você possa compreender:

- Quais metadados de dispositivo a solução armazena.
- Como a solução gerencia os metadados de dispositivo.

## Contexto

A solução pré-configurada de monitoramento remoto usa o [Hub IoT do Azure][lnk-iot-hub] para permitir que os dispositivos enviem dados para a nuvem. O Hub IoT inclui um [registro de identidade do dispositivo][lnk-identity-registry] para controlar o acesso ao Hub IoT. O registro de identidade do dispositivo do Hub IoT é separado do *registro do dispositivo* específico da solução de monitoramento remoto que armazena metadados de informações de dispositivo. A solução de monitoramento remoto usa um banco de dados do [Banco de Dados de Documentos][lnk-docdb] para implementar seu registro do dispositivo para armazenar metadados de informações de dispositivo. A [Arquitetura de Referência do Microsoft Azure IoT][lnk-ref-arch] descreve a função do registro do dispositivo em uma solução típica de IoT.

> [AZURE.NOTE] A solução pré-configurada de monitoramento remoto mantém o registro de identidade do dispositivo em sincronia com o registro do dispositivo. Ambos usam a mesma id de dispositivo para identificar exclusivamente cada dispositivo conectado ao seu hub IoT.

A [visualização de gerenciamento do dispositivo Hub IoT][lnk-dm-preview] adiciona recursos ao Hub IoT semelhantes aos recursos de gerenciamento de informações de dispositivo na solução pré-configurada de monitoramento remoto descrita neste artigo. Neste momento, no entanto, a solução de monitoramento remoto utiliza os recursos de disponibilidade geral (GA) no Hub IoT.

## Metadados de informações de dispositivo

Um documento JSON de metadados de informações de dispositivo armazenado no banco de dados do Banco de Dados de Documentos de registro do dispositivo tem a seguinte estrutura:

```
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

- **DeviceProperties**: o próprio dispositivo grava essas propriedades e o dispositivo é a autoridade para esses dados. Outras propriedades de dispositivo de exemplo incluem fabricante, número do modelo e número de série.
- **DeviceID**: a id exclusiva do dispositivo. Esse valor é igual no registro de identidade do dispositivo Hub IoT.
- **HubEnabledState**: o status do dispositivo no Hub IoT. Isso é inicialmente definido como **null** até o dispositivo se conectar pela primeira vez. No portal da solução, isso é representado como um dispositivo “registrado mas não presente”.
- **CreatedTime**: a hora em que o dispositivo foi criado.
- **DeviceState**: o estado relatado pelo dispositivo.
- **UpdatedTime**: a hora em que o dispositivo foi atualizado pela última vez por meio do portal da solução.
- **SystemProperties**: o portal de solução grava as propriedades do sistema e o dispositivo não tem conhecimento dessas propriedades. Uma propriedade do sistema de exemplo é o **ICCID**, se a solução for autorizada por um serviço que gerencia dispositivos habilitados para SIM e conectada a ele.
- **Commands**: uma lista de comandos com suporte do dispositivo. O dispositivo fornece essas informações para a solução.
- **CommandHistory**: uma lista dos comandos enviados pela solução de monitoramento remoto para o dispositivo e o status desses comandos.
- **IsSimulatedDevice**: um sinalizador que identifica um dispositivo como um dispositivo simulado.
- **id**: o identificador exclusivo do Banco de Dados de Documentos para este documento de dispositivo.

> [AZURE.NOTE] As informações de dispositivo também podem incluir metadados para descrever a telemetria que o dispositivo envia ao Hub IoT. A solução de monitoramento remoto usa esses metadados de telemetria para personalizar a forma como o painel exibe a [telemetria dinâmica][lnk-dynamic-telemetry].

## Ciclo de vida

Quando você cria um dispositivo no portal de solução, a solução cria uma entrada em seu registro do dispositivo, como mostrado acima. Grande parte das informações é inicialmente oculta e o **HubEnabledState** é definido como **null**. Neste ponto, a solução também cria uma entrada para o dispositivo no registro de identidade do dispositivo do Hub IoT que gera as chaves que o dispositivo usa para se autenticar no Hub IoT.

Quando um dispositivo se conecta pela primeira vez à solução, ele envia uma mensagem de informações de dispositivo. Essa mensagem de informações de dispositivo inclui propriedades como o fabricante do dispositivo, o número de modelo e o número de série do dispositivo. Uma mensagem de informações de dispositivo também inclui uma lista dos comandos aos quais o dispositivo oferece suporte, incluindo informações sobre quaisquer parâmetros de comando. Quando a solução recebe essa mensagem, ela atualiza os metadados de informações de dispositivo no registro do dispositivo.

### Exibir e editar informações de dispositivo no portal da solução

A lista de dispositivos no portal da solução exibe as seguintes propriedades de dispositivo como colunas: **Status**, **IdDoDispositivo**, **Fabricante**, **Número do Modelo**, **Número de Série**, **Firmware**, **Plataforma**, **Processador** e **RAM Instalada**. As propriedades do dispositivo **Latitude** e **Longitude** direcionam a localização no Bing Mapa no painel.

![Lista de dispositivos][img-device-list]

Se você clicar em **Editar** no painel **Detalhes do Dispositivo** no portal de solução, poderá editar todas essas propriedades. A edição dessas propriedades atualiza o registro do dispositivo no banco de dados do Banco de Dados de Documentos; no entanto, se um dispositivo enviar uma mensagem de informações de dispositivo atualizado, ela substituirá quaisquer alterações feitas no portal da solução. Você não pode editar as propriedades **DeviceId**, **Hostname**, **HubEnabledState**, **CreatedTime**, **DeviceState** e **UpdatedTime** no portal da solução porque somente o dispositivo tem autoridade sobre essas propriedades.

![Edição de dispositivo][img-device-edit]

Você pode usar o portal da solução para remover um dispositivo da sua solução. Quando você remover um dispositivo, a solução removerá os metadados de informações de dispositivo do registro do dispositivo da solução e removerá a entrada do registro de identidade do dispositivo do Hub IoT. Antes de remover um dispositivo, você deverá desabilitá-lo.

![Remoção de dispositivo][img-device-remove]

## Processamento de mensagens de informações de dispositivo

As mensagens de informação de dispositivo enviadas por um dispositivo são diferentes das mensagens de telemetria porque incluem informações como as propriedades do dispositivo, os comandos a que um dispositivo pode responder e qualquer histórico de comandos. O Hub IoT em si não tem conhecimento dos metadados contidos em uma mensagem de informações de dispositivo e processa a mensagem da mesma forma que processa todas as mensagens de dispositivo para a nuvem. Na solução de monitoramento remoto, um trabalho do [Stream Analytics do Azure][lnk-stream-analytics] (ASA) lê as mensagens do Hub IoT. O trabalho de análise do fluxo **DeviceInfo** filtra mensagens que contêm **"ObjectType": "DeviceInfo"** e as encaminha para a instância de host **EventProcessorHost** executada em um trabalho Web. A lógica na instância **EventProcessorHost** usa a id do dispositivo para localizar o registro do Banco de Dados de Documentos para o dispositivo específico e atualiza o registro. O registro do dispositivo agora inclui informações como as propriedades do dispositivo, os comandos e o histórico de comandos.

> [AZURE.NOTE] Uma mensagem de informações de dispositivo é uma mensagem padrão do dispositivo para a nuvem. A solução faz distinção entre as mensagens de informações de dispositivo e as mensagens de telemetria por meio de consultas ASA.

## Registros de informações de dispositivo de exemplo

A solução pré-configurada de monitoramento remoto usa dois tipos de registros de informações de dispositivo: registros para os dispositivos simulados implantados com a solução e registros para os dispositivos personalizados conectados à solução.

### Dispositivo simulado

O exemplo a seguir mostra o registro de informações de dispositivo JSON para um dispositivo simulado. Este registro tem um valor definido para **UpdatedTime** que indica que o dispositivo enviou uma mensagem **DeviceInfo** ao Hub IoT. O registro inclui algumas propriedades comuns do dispositivo, define os seis comandos com suporte dos dispositivos simulados e tem o sinalizador **IsSimulatedDevice** definido como **1**.

```
{
  "DeviceProperties": {
    "DeviceID": "SampleDevice001_455",
    "HubEnabledState": true,
    "CreatedTime": "2016-01-26T19:02:01.4550695Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-01T15:28:41.8105157Z",
    "Manufacturer": "Contoso Inc.",
    "ModelNumber": "MD-369",
    "SerialNumber": "SER9009",
    "FirmwareVersion": "1.39",
    "Platform": "Plat-34",
    "Processor": "i3-2191",
    "InstalledRAM": "3 MB",
    "Latitude": 47.583582,
    "Longitude": -122.130622
  },
  "Commands": [
    {
      "Name": "PingDevice",
      "Parameters": null
    },
    {
      "Name": "StartTelemetry",
      "Parameters": null
    },
    {
      "Name": "StopTelemetry",
      "Parameters": null
    },
    {
      "Name": "ChangeSetPointTemp",
      "Parameters": [
        {
          "Name": "SetPointTemp",
          "Type": "double"
        }
      ]
    },
    {
      "Name": "DiagnosticTelemetry",
      "Parameters": [
        {
          "Name": "Active",
          "Type": "boolean"
        }
      ]
    },
    {
      "Name": "ChangeDeviceState",
      "Parameters": [
        {
          "Name": "DeviceState",
          "Type": "string"
        }
      ]
    }
  ],
  "CommandHistory": [],
  "IsSimulatedDevice": 1,
  "Version": "1.0",
  "ObjectType": "DeviceInfo",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleDevice001_455",
    "ConnectionDeviceGenerationId": "635894317219942540",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  },
  "SystemProperties": {
    "ICCID": null
  },
  "id": "7101c002-085f-4954-b9aa-7466980a2aaf"
}
```

### Dispositivo personalizado

O exemplo a seguir mostra o registro de informações de dispositivo JSON para um dispositivo personalizado e tem o sinalizador **IsSimulatedDevice** definido como **0**. Você pode ver que esse dispositivo personalizado oferece suporte a dois comandos e que o portal da solução enviou um comando **SetTemperature** para o dispositivo:

```
{
  "DeviceProperties": {
    "DeviceID": "mydevice01",
    "HubEnabledState": true,
    "CreatedTime": "2016-03-28T21:05:06.6061104Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-07T22:05:34.2802549Z"
  },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [
    {
      "Name": "SetHumidity",
      "Parameters": [
        {
          "Name": "humidity",
          "Type": "int"
        }
      ]
    },
    {
      "Name": "SetTemperature",
      "Parameters": [
        {
          "Name": "temperature",
          "Type": "int"
        }
      ]
    }
  ],
  "CommandHistory": [
    {
      "Name": "SetTemperature",
      "MessageId": "2a0cec61-5eca-4de7-92dc-9c0bc4211c46",
      "CreatedTime": "2016-06-07T21:05:18.140796Z",
      "Parameters": {
        "temperature": 20
      },
      "UpdatedTime": "2016-06-07T21:05:18.716076Z",
      "Result": "Expired"
    }
  ],
  "IsSimulatedDevice": 0,
  "id": "6184ae0f-2d94-4fbd-91cd-4b193aecc9d1",
  "ObjectType": "DeviceInfo",
  "Version": "1.0",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleCustom",
    "ConnectionDeviceGenerationId": "635947959068246845",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  }
}
```

A seguir, a mensagem **DeviceInfo** JSON enviada pelo dispositivo para atualizar os metadados de informações de dispositivo:

```
{ "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties": { "DeviceID":"mydevice01", "HubEnabledState":true },
  "Commands": [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    {"Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

## Próximas etapas

Você pode ler mais sobre o envio de mensagens de informações do dispositivo personalizado em [Conectar o dispositivo à solução pré-configurada de monitoramento remoto][lnk-connect-custom].

Leia mais sobre como personalizar as soluções pré-configuradas em [Personalizar uma solução pré-configurada][lnk-customize].

<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide.md#device-identity-registry
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-ref-arch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-connect-custom]: iot-suite-connecting-devices.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dm-preview]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

<!---HONumber=AcomDC_0720_2016-->