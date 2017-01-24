---
title: Simular um dispositivo com o SDK do Gateway IoT do Azure (Linux) |Microsoft Docs
description: Como usar o SDK do Gateway IoT do Azure no Linux para criar um dispositivo simulado que envia a telemetria por meio de um gateway para um Hub IoT.
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
ms.date: 08/29/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: e2e814559282de3e5409e3215d824e1309debe5a


---
# <a name="use-the-azure-iot-gateway-sdk-to-send-device-to-cloud-messages-with-a-simulated-device-linux"></a>Use o SDK do Gateway IoT do Azure para enviar mensagens do dispositivo para a nuvem com um dispositivo simulado (Linux)
[!INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra
Antes de começar, é necessário:

* [Configure seu ambiente de desenvolvimento][lnk-setupdevbox] para trabalhar com o SDK no Linux.
* [Crie um Hub IoT][lnk-create-hub] em sua assinatura do Azure. Você precisará do nome do hub para concluir este passo a passo. Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* Adicione dois dispositivos ao hub IoT e anote as IDs e chaves de dispositivo. É possível usar o [Gerenciador de Dispositivo ou a ferramenta iothub-explorer][lnk-explorer-tools] para adicionar seus dispositivos ao Hub IoT criado na etapa anterior e recuperar suas chaves.

Para compilar a amostra:

1. Abra um shell.
2. Navegue até a pasta raiz de sua cópia local do repositório **azure-iot-gateway-sdk** .
3. Execute o script **tools/build.sh --skip-unittests**. Esse script usa o utilitário **cmake** para criar uma pasta chamada **build** na pasta raiz da cópia local do repositório **azure-iot-gateway-sdk** e gerar um makefile. O script cria a solução, ignorando os testes de unidade. Remova o parâmetro **--skip-unittests** se você deseja criar e executar os testes de unidade. 

> [!NOTE]
> Sempre que você executa o script **build.sh**, ele exclui e recria a pasta **build** na pasta raiz da cópia local do repositório **azure-iot-gateway-sdk**.
> 
> 

Para executar a amostra:

Em um editor de texto, abra o arquivo **samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json** na cópia local do repositório **azure-iot-gateway-sdk**. Este arquivo configura os módulos no gateway de exemplo:

* O módulo **IoTHub** se conecta ao seu hub IoT. É necessário configurá-lo para enviar dados ao hub IoT. Especificamente, defina o valor de **IoTHubName** como o nome de seu hub IoT e defina o valor de **IoTHubSuffix** como **azure-devices.net**. Defina o valor de **Transporte** como um destes: "HTTP", "AMQP" ou "MQTT". Observe que, no momento, apenas "HTTP" compartilhará uma conexão TCP para todas as mensagens de dispositivo. Se você definir o valor como "AMQP" ou "MQTT", o gateway manterá uma conexão TCP separada para o Hub IoT para cada dispositivo.
* O módulo **mapping** mapeia os endereços MAC dos dispositivos simulados para as IDs de dispositivo do Hub IoT. Verifique se os valores de **deviceId** correspondem às IDs dos dois dispositivos que você adicionou ao hub IoT e se os valores de **deviceKey** contêm as chaves dos dois dispositivos.
* Os módulos **BLE1** e **BLE2** são os dispositivos simulados. Observe como os endereços MAC correspondem ao módulo **mapping** .
* O módulo **Logger** registra a atividade de gateway em um arquivo.
* Os valores de **caminho do módulo** abaixo pressupõem que você executará a amostra da raiz de sua cópia local do repositório **azure-iot-gateway-sdk**.
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

1. No shell, navegue até a pasta **azure-iot-gateway-sdk/build**.
2. Execute o comando a seguir:
   
    ```
    ./samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ./../samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```
3. É possível usar o [Gerenciador de Dispositivo ou a ferramenta iothub-explorer][lnk-explorer-tools] para monitorar as mensagens que o Hub IoT recebe do gateway.

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
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-create-hub]: iot-hub-create-through-portal.md



<!--HONumber=Dec16_HO2-->


