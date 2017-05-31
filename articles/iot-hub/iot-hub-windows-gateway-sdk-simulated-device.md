---
title: Simular um dispositivo com Azure IoT Edge (Windows) | Microsoft Docs
description: Como usar o Azure IoT Edge no Windows para criar um dispositivo simulado que envie telemetria por meio de um gateway do Azure IoT Edge para um hub IoT.
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
ms.date: 03/29/2017
ms.author: andbuc
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 0ea8483e19ddec447642a33c24c9ecdd9937ea11
ms.contentlocale: pt-br
ms.lasthandoff: 05/16/2017


---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-windows"></a>Use o Azure IoT Edge para enviar mensagens do dispositivo para a nuvem com um dispositivo simulado (Windows)
[!INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra
Antes de começar, é necessário:

* [Configure seu ambiente de desenvolvimento][lnk-setupdevbox] para trabalhar com o SDK no Windows.
* [Crie um Hub IoT][lnk-create-hub] em sua assinatura do Azure. Você precisará do nome do hub para concluir este passo a passo. Se não tiver uma conta, você poderá criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* Adicione dois dispositivos ao hub IoT e anote as IDs e chaves de dispositivo. É possível usar o [gerenciador de dispositivo][lnk-device-explorer] ou a ferramenta [iothub-explorer][lnk-iothub-explorer] para adicionar seus dispositivos ao Hub IoT criado na etapa anterior e recuperar suas chaves.

Para compilar a amostra:

1. Abra um **Prompt de comando do desenvolvedor para VS 2015** ou **Prompt de comando do desenvolvedor para VS 2017**.
2. Navegue até a pasta raiz na sua cópia local do repositório **iot-edge**.
3. Execute o scpript **tools\\build.cmd**. Este script cria um arquivo de solução do Visual Studio e compila a solução. É possível encontrar a solução do Visual Studio na pasta **build** na cópia local do repositório **iot-edge**. Parâmetros adicionais podem ser fornecidos ao script para criar e executar testes de unidade e ponta a ponta. Esses parâmetros são **--run-unittests** e **--run-e2e-tests**, respectivamente.

Para executar a amostra:

Em um editor de texto, abra o arquivo **samples\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** na sua cópia local do repositório **iot-edge**. Este arquivo configura os módulos do IoT Edge no gateway de exemplo:

* O módulo **IoTHub** se conecta ao seu hub IoT. Você o configura para enviar dados ao Hub IoT. Especificamente, defina o valor de **IoTHubName** como o nome de seu hub IoT e defina o valor de **IoTHubSuffix** como **azure-devices.net**. Defina o valor de **Transporte** como um destes: "HTTP", "AMQP" ou "MQTT". Atualmente, apenas "HTTP" compartilhará uma conexão TCP para todas as mensagens de dispositivo. Se você definir o valor como "AMQP" ou "MQTT", o gateway manterá uma conexão TCP separada para o Hub IoT para cada dispositivo.
* O módulo **mapping** mapeia os endereços MAC dos dispositivos simulados para as IDs de dispositivo do Hub IoT. Verifique se os valores de **deviceId** correspondem às IDs dos dois dispositivos que você adicionou ao hub IoT e se os valores de **deviceKey** contêm as chaves dos dois dispositivos.
* Os módulos **BLE1** e **BLE2** são os dispositivos simulados. Observe como os endereços MAC do módulo correspondem aos endereços no módulo **mapping**.
* O módulo **Logger** registra a atividade de gateway em um arquivo.
* Os valores de **caminho do módulo** mostrados no exemplo a seguir pressupõem que você tenha clonado o repositório do IoT Edge para a raiz da unidade **C:**. Se você o baixou em outro local, precisará ajustar os valores de **caminho do módulo** de acordo.
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

1. No prompt de comando, navegue para a pasta raiz da sua cópia local do repositório **iot-edge**.
2. Execute o comando a seguir:
   
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. É possível usar o [gerenciador de dispositivo][lnk-device-explorer] ou a ferramenta [iothub-explorer][lnk-iothub-explorer] para monitorar as mensagens que o Hub IoT recebe do gateway.

## <a name="next-steps"></a>Próximas etapas
Se você quiser compreender de maneira mais avançada o IoT Edge e experimentar alguns exemplos de código, acesse os seguintes recursos e tutoriais para desenvolvedores:

* [Enviar mensagens do dispositivo para a nuvem de um dispositivo físico com o IoT Edge][lnk-physical-device]
* [Azure IoT Edge][lnk-gateway-sdk]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT][lnk-devguide]
* [Proteger sua solução de IoT desde o início][lnk-securing]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-gateway-sdk]: https://github.com/Azure/iot-edge/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md

