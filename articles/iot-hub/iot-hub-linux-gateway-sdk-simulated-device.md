---
title: Simular um dispositivo com Azure IoT Edge (Linux) | Microsoft Docs
description: Como usar o Azure IoT Edge no Linux para criar um dispositivo simulado que envie telemetria por meio de um gateway do IoT Edge para um hub IoT.
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 11e7bf28-ee3d-48d6-a386-eb506c7a31cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2017
ms.author: andbuc
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: eea65c3befb2e7924ccdd174c6d493f0c79b95c6
ms.contentlocale: pt-br
ms.lasthandoff: 05/16/2017


---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-linux"></a>Use o Azure IoT Edge para enviar mensagens do dispositivo para a nuvem com um dispositivo simulado (Linux)
[!INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra
Antes de começar, é necessário:

* [Configure seu ambiente de desenvolvimento][lnk-setupdevbox] para trabalhar com o SDK no Linux.
* [Crie um Hub IoT][lnk-create-hub] em sua assinatura do Azure. Você precisará do nome do hub para concluir este passo a passo. Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* Adicione dois dispositivos ao hub IoT e anote as IDs e chaves de dispositivo. É possível usar o [gerenciador de dispositivo][lnk-device-explorer] ou a ferramenta [iothub-explorer][lnk-iothub-explorer] para adicionar seus dispositivos ao Hub IoT criado na etapa anterior e recuperar suas chaves.

Para compilar a amostra:

1. Abra um shell.
2. Navegue até a pasta raiz na sua cópia local do repositório **iot-edge**.
3. Execute o script **tools/build.sh** . Esse script usa o utilitário **cmake** para criar uma pasta chamada **build** na pasta raiz da cópia local do repositório **iot-edge** e gerar um makefile. O script cria a solução, ignorando os testes de unidade e os testes de ponta a ponta. Adicione o parâmetro **--skip-unittests** se quiser criar e executar os testes de unidade. Adicione **--run-e2e-tests** se quiser compilar e executar os testes de ponta a ponta. 

> [!NOTE]
> Sempre que você executa o script **build.sh**, ele exclui e recria a pasta **build** na pasta raiz da cópia local do repositório **iot-edge**.
> 
> 

Para executar a amostra:

Em um editor de texto, abra o arquivo **samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json** na cópia local do repositório **iot-edge**. Este arquivo configura os módulos do IoT Edge no gateway de exemplo:

* O módulo **IoTHub** se conecta ao seu hub IoT. É necessário configurá-lo para enviar dados ao hub IoT. Especificamente, defina o valor de **IoTHubName** como o nome de seu hub IoT e defina o valor de **IoTHubSuffix** como **azure-devices.net**. Defina o valor de **Transporte** como um destes: "HTTP", "AMQP" ou "MQTT". Observe que, no momento, apenas "HTTP" compartilhará uma conexão TCP para todas as mensagens de dispositivo. Se você definir o valor como "AMQP" ou "MQTT", o gateway manterá uma conexão TCP separada para o Hub IoT para cada dispositivo.
* O módulo **mapping** mapeia os endereços MAC dos dispositivos simulados para as IDs de dispositivo do Hub IoT. Verifique se os valores de **deviceId** correspondem às IDs dos dois dispositivos que você adicionou ao hub IoT e se os valores de **deviceKey** contêm as chaves dos dois dispositivos.
* Os módulos **BLE1** e **BLE2** são os dispositivos simulados. Observe como os endereços MAC correspondem ao módulo **mapping** .
* O módulo **Logger** registra a atividade de gateway em um arquivo.
* Os valores do **caminho do módulo** abaixo pressupõem que você vá executar a amostra da raiz de sua cópia local do repositório **iot-edge**.
* A matriz **links** na parte inferior do arquivo JSON conecta os módulos **BLE1** e **BLE2** ao módulo **mapping**, e o **mapping** ao módulo **IoTHub**. Ela também garante que todas as mensagens são registradas pelo módulo **Logger** .

```
{
    "modules": [
        {
            "name": "IotHub",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "./modules/iothub/libiothub.so"
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
              "module.path": "./modules/identitymap/libidentity_map.so"
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
              "module.path": "./modules/simulated_device/libsimulated_device.so"
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
              "module.path": "./modules/simulated_device/libsimulated_device.so"
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
              "module.path": "./modules/logger/liblogger.so"
            }
            },
            "args": {
              "filename": "deviceCloudUploadGatewaylog.log"
            }
          }
    ],
    "links": [
        {
            "source": "*",
            "sink": "Logger"
        },
        {
            "source": "BLE1",
            "sink": "mapping"
        },
        {
            "source": "BLE2",
            "sink": "mapping"
        },
        {
            "source": "mapping",
            "sink": "IotHub"
        }
    ]
}
```

Salve todas as alterações feitas no arquivo de configuração.

Para executar a amostra:

1. No shell, navegue até a pasta **iot-edge/build**.
2. Execute o comando a seguir:
   
    ```
    ./samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ./../samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```
3. É possível usar o [Device Explorer][lnk-device-explorer] ou a ferramenta [iothub-explorer][lnk-iothub-explorer] para monitorar as mensagens que o hub IoT recebe do gateway IoT Edge.

## <a name="next-steps"></a>Próximas etapas
Se você quiser compreender de maneira mais avançada o Azure IoT Edge e experimentar alguns exemplos de código, acesse os seguintes recursos e tutoriais para desenvolvedores:

* [Enviar mensagens do dispositivo para a nuvem de um dispositivo físico com o Azure IoT Edge][lnk-physical-device]
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
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-create-hub]: iot-hub-create-through-portal.md

