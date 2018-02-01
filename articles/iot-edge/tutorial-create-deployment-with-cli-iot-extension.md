---
title: "Implantar módulos para dispositivos IoT Edge usando a extensão de IoT da CLI do Azure 2.0 | Microsoft Docs"
description: "Implantar módulos para dispositivos IoT Edge usando a extensão de IoT da CLI do Azure 2.0"
services: iot-edge
keywords: 
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 01/11/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 26067187864f9a2a4c85c953ae8aca888458d245
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Implantar módulos para dispositivos IoT Edge usando a extensão de IoT da CLI do Azure 2.0

A [CLI do Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) é uma ferramenta de linha de comando de software livre para gerenciamento dos recursos do Azure como, por exemplo, o IoT Edge. A CLI do Azure 2.0 está disponível para Windows, Linux e MacOS.

A CLI do Azure 2.0 permite que você gerencie instantaneamente recursos, instâncias de serviço de provisionamento de dispositivos e hubs vinculados do Hub IoT do Azure. A nova extensão de IoT aprimora a CLI do Azure 2.0 com recursos como gerenciamento de dispositivos e todos os recursos do IoT Edge.

Neste tutorial, você primeiro conclui as etapas para configurar a CLI do Azure 2.0 e a extensão de IoT. Em seguida, você aprende como implantar módulos em um dispositivo de IoT Edge usando os comandos disponíveis da CLI.

## <a name="installation"></a>Instalação 

### <a name="step-1---install-python"></a>Etapa 1 - Instale o Python

[Python 2.7x ou Python 3.x](https://www.python.org/downloads/) são necessários.

### <a name="step-2---install-azure-cli-20"></a>Etapa 2 - Instale a CLI do Azure 2.0

Siga as [instruções de instalação](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) para configurar a CLI do Azure 2.0 no seu ambiente. A versão 2.0 da CLI do Azure deve ser 2.0.24 ou superior. Use `az –version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack. Uma maneira simples de instalar no Windows é baixar e instalar o [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Etapa 3 - Instale a extensão de IoT

[O Leiame da extensão de IoT](https://github.com/Azure/azure-iot-cli-extension) descreve várias maneiras de instalar a extensão. A maneira mais simples é executar `az extension add --name azure-cli-iot-ext`. Após a instalação, você pode usar `az extension list` para validar as extensões instaladas no momento ou `az extension show --name azure-cli-iot-ext` para ver os detalhes sobre a extensão de IoT. Para remover a extensão, você pode usar `az extension remove --name azure-cli-iot-ext`.


## <a name="deploy-modules-to-an-iot-edge-device"></a>Implantar módulos em um dispositivo do IoT Edge
Neste tutorial, você aprenderá como criar uma implantação do IoT Edge. O exemplo mostra como fazer logon em sua conta do Azure, criar um Grupo de Recursos do Azure (um contêiner que contém recursos relacionados para uma solução do Azure), criar um IoT Hub, criar três identidade de dispositivos IoT Edge, definir marcas e, em seguida, criar uma implantação de IoT Edge que tem como alvo esses dispositivos. Conclua as etapas de instalação descritas anteriormente antes de começar. Se você ainda não tiver uma conta do Azure, você pode [criar uma conta gratuita](https://azure.microsoft.com/free/?v=17.39a) hoje. 


### <a name="1-login-to-the-azure-account"></a>1. Faça logon na conta Azure
  
    az login

![logon][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Criar um grupo de recursos IoTHubBlogDemo no eastus

    az group create -l eastus -n IoTHubBlogDemo

![Criar grupo de recursos][2]


### <a name="3-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>3. Criar um blogDemoHub do Hub IoT no grupo de recursos recentemente criado

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Criar Hub IoT][3]


### <a name="4-create-an-iot-edge-device"></a>4. Criar um dispositivo IoT Edge

    az iot hub device-identity create -d edge001 -n blogDemoHub --edge-enabled

![Criar dispositivo IoT Edge][4]

### <a name="5-apply-configuration-to-the-iot-edge-device"></a>5. Aplicar a configuração para o dispositivo de IoT Edge

Salve o modelo de implantação de JSON localmente como um arquivo txt. Você precisará do caminho para o arquivo quando executar o comando para a aplicar configuração.

Abaixo está um modelo de implantação de JSON de exemplo que contém um módulo tempSensor:

```json
{
  "moduleContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": ""
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-agent:1.0-preview",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-hub:1.0-preview",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "tempSensor": {
      "properties.desired": {}
    }
  }
}
```

    az iot hub apply-configuration --device-id edge001 --hub-name blogDemoHub --content C:\<yourLocation>\edgeconfig.txt

![Aplicar configuração][5]

### <a name="6-list-modules"></a>6. Listar módulos
    
    az iot hub module-identity list --device-id edge001 --hub-name blogDemoHub

![Listar módulos][6]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma função do Azure que contém código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge. Para continuar explorando o Azure IoT Edge, aprenda a usar um dispositivo IoT Edge como gateway. 

> [!div class="nextstepaction"]
> [Criar um dispositivo de gateway IoT Edge](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-create-deployment-with-cli-iot-extension/login.jpg
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.jpg
[3]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-hub.jpg
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[5]: ./media/tutorial-create-deployment-with-cli-iot-extension/apply-configuration.PNG
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.PNG

