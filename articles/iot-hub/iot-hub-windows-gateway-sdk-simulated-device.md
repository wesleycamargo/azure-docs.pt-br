---
title: Simular um dispositivo com o SDK do Gateway IoT do Azure (Windows) |Microsoft Docs
description: Como usar o SDK do Gateway IoT do Azure no Windows para criar um dispositivo simulado que envia a telemetria por meio de um gateway para um Hub IoT.
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 6a2aeda0-696a-4732-90e1-595d2e2fadc6
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2017
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 5edf2f4c7d9d2e8e8ceb2e8de9ae7cef4c9fd02e
ms.openlocfilehash: 446e7361b5817ce888105fbb78f329affa7de6e6


---
# <a name="use-the-azure-iot-gateway-sdk-to-send-device-to-cloud-messages-with-a-simulated-device-windows"></a>Use o SDK do Gateway IoT do Azure para enviar mensagens do dispositivo para a nuvem com um dispositivo simulado (Windows)
[!INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra
Antes de começar, é necessário:

* [Configure seu ambiente de desenvolvimento][lnk-setupdevbox] para trabalhar com o SDK no Windows.
* [Crie um Hub IoT][lnk-create-hub] em sua assinatura do Azure. Você precisará do nome do hub para concluir este passo a passo. Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* Adicione dois dispositivos ao hub IoT e anote as IDs e chaves de dispositivo. É possível usar o [gerenciador de dispositivo][lnk-device-explorer] ou a ferramenta [iothub-explorer][lnk-iothub-explorer] para adicionar seus dispositivos ao Hub IoT criado na etapa anterior e recuperar suas chaves.

Para compilar a amostra:

1. Abra um prompt de comando **Prompt de Comando do Desenvolvedor para VS2015** .
2. Navegue até a pasta raiz de sua cópia local do repositório **azure-iot-gateway-sdk** .
3. Execute o scpript **tools\\build.cmd**. Este script cria um arquivo de solução do Visual Studio e compila a solução. É possível encontrar a solução do Visual Studio na pasta **build** na cópia local do repositório **azure-iot-gateway-sdk**. Parâmetros adicionais podem ser fornecidos ao script para criar e executar testes de unidade e ponta a ponta. Esses parâmetros são **--run-unittests** e **--run-e2e-tests** respectivamente.

Para executar a amostra:

Em um editor de texto, abra o arquivo **samples\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** na cópia local do repositório **azure-iot-gateway-sdk**. Este arquivo configura os módulos no gateway de exemplo:

* O módulo **IoTHub** se conecta ao seu hub IoT. É necessário configurá-lo para enviar dados ao hub IoT. Especificamente, defina o valor de **IoTHubName** como o nome de seu hub IoT e defina o valor de **IoTHubSuffix** como **azure-devices.net**. Defina o valor de **Transporte** como um destes: "HTTP", "AMQP" ou "MQTT". Observe que, no momento, apenas "HTTP" compartilhará uma conexão TCP para todas as mensagens de dispositivo. Se você definir o valor como "AMQP" ou "MQTT", o gateway manterá uma conexão TCP separada para o Hub IoT para cada dispositivo.
* O módulo **mapping** mapeia os endereços MAC dos dispositivos simulados para as IDs de dispositivo do Hub IoT. Verifique se os valores de **deviceId** correspondem às IDs dos dois dispositivos que você adicionou ao hub IoT e se os valores de **deviceKey** contêm as chaves dos dois dispositivos.
* Os módulos **BLE1** e **BLE2** são os dispositivos simulados. Observe como os endereços MAC correspondem ao módulo **mapping** .
* O módulo **Logger** registra a atividade de gateway em um arquivo.
* Os valores de **caminho do módulo** mostrados abaixo pressupõem que você clonou o repositório do SDK do Gateway IoT na raiz da unidade **C:**. Se você o baixou em outro local, precisará ajustar os valores de **caminho do módulo** de acordo.
* A matriz **links** na parte inferior do arquivo JSON conecta os módulos **BLE1** e **BLE2** ao módulo **mapping**, e o **mapping** ao módulo **IoTHub**. Ela também garante que todas as mensagens são registradas pelo módulo **Logger** .

```
{
    "modules" :
    [
      {
        "name": "IotHub",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\iothub\\Debug\\iothub.dll"
          }
          },
          "args": {
            "IoTHubName": "<<insert here IoTHubName>>",
            "IoTHubSuffix": "<<insert here IoTHubSuffix>>",
            "Transport": "HTTP"
          }
        },
      {
        "name": "mapping",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\identitymap\\Debug\\identity_map.dll"
          }
          },
          "args": [
            {
              "macAddress": "01:01:01:01:01:01",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            },
            {
              "macAddress": "02:02:02:02:02:02",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            }
          ]
        },
      {
        "name": "BLE1",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "01:01:01:01:01:01"
          }
        },
      {
        "name": "BLE2",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "02:02:02:02:02:02"
          }
        },
      {
        "name": "Logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
          }
        },
        "args": {
          "filename": "deviceCloudUploadGatewaylog.log"
        }
      }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IotHub" }
    ]
}
```

Salve todas as alterações feitas no arquivo de configuração.

Para executar a amostra:

1. No prompt de comando, navegue até a pasta raiz de sua cópia local do repositório **azure-iot-gateway-sdk** .
2. Execute o comando a seguir:
   
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. É possível usar o [gerenciador de dispositivo][lnk-device-explorer] ou a ferramenta [iothub-explorer][lnk-iothub-explorer] para monitorar as mensagens que o Hub IoT recebe do gateway.

## <a name="next-steps"></a>Próximas etapas
Se você desejar obter uma compreensão mais avançada do SDK do Gateway do IoT e experimentar alguns exemplos de código, visite os seguintes recursos e tutoriais para desenvolvedores:

* [Enviar mensagens do dispositivo para a nuvem de um dispositivo físico com o SDK do Gateway do IoT][lnk-physical-device]
* [SDK do Gateway do Azure IoT][lnk-gateway-sdk]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT][lnk-devguide]
* [Proteger sua solução de IoT desde o início][lnk-securing]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md



<!--HONumber=Feb17_HO1-->


