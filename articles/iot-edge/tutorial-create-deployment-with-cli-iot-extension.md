---
title: "Implantar módulos para dispositivos IoT Edge usando a extensão de IoT da CLI do Azure 2.0 | Microsoft Docs"
description: "Implantar módulos para dispositivos IoT Edge usando a extensão de IoT da CLI do Azure 2.0"
services: iot-edge
keywords: 
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 02/12/2018
ms.topic: article
ms.service: iot-edge
ms.custom: mvc
ms.reviewer: kgremban
ms.openlocfilehash: ce3e979428233af578d71dee5ed10103e105f4f4
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Implantar módulos para dispositivos IoT Edge usando a extensão de IoT da CLI do Azure 2.0

A [CLI do Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) é uma ferramenta de linha de comando de software livre para gerenciamento dos recursos do Azure como, por exemplo, o IoT Edge. A CLI do Azure 2.0 está disponível para Windows, Linux e MacOS.

A CLI do Azure 2.0 permite que você gerencie instantaneamente recursos, instâncias de serviço de provisionamento de dispositivos e hubs vinculados do Hub IoT do Azure. A nova extensão de IoT aprimora a CLI do Azure 2.0 com recursos como gerenciamento de dispositivos e todos os recursos do IoT Edge.

Neste tutorial, você primeiro conclui as etapas para configurar a CLI do Azure 2.0 e a extensão de IoT. Em seguida, você aprende como implantar módulos em um dispositivo de IoT Edge usando os comandos disponíveis da CLI.

## <a name="prerequisites"></a>pré-requisitos

* Uma conta do Azure. Se você não tiver uma, poderá [criar uma conta gratuita](https://azure.microsoft.com/free/?v=17.39a) hoje. 

* [Python 2.7x ou Python 3.x](https://www.python.org/downloads/).

* [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) no seu ambiente. A versão 2.0 da CLI do Azure deve ser 2.0.24 ou superior. Use `az –-version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack. Uma maneira simples de instalar no Windows é fazer o download e instalar o [MSI](https://aka.ms/InstallAzureCliWindows).

* [Extensão de IoT para CLI 2.0 do Azure](https://github.com/Azure/azure-iot-cli-extension):
   1. Execute `az extension add --name azure-cli-iot-ext`. 
   2. Após a instalação, use `az extension list` para validar as extensões instaladas no momento ou `az extension show --name azure-cli-iot-ext` para ver os detalhes sobre a extensão de IoT.
   3. Para remover a extensão, use `az extension remove --name azure-cli-iot-ext`.


## <a name="create-an-iot-edge-device"></a>Criar um dispositivo IoT Edge
Este artigo fornece instruções para criar uma implantação do IoT Edge. O exemplo mostra como entrar na sua conta do Azure, criar um Grupo de Recursos do Azure (um contêiner que contém recursos relacionados a uma solução do Azure), criar um Hub IoT, criar três identidades de dispositivos IoT Edge, definir marcas e criar uma implantação do IoT Edge direcionada a esses dispositivos. 

Faça logon na sua conta do Azure. Após inserir o seguinte comando de logon, você será solicitado a usar um navegador da Web para entrar, usando um código avulso: 

   ```cli
   az login
   ```

Crie um novo grupo de recursos chamado **IoTHubCLI** na região Leste dos EUA: 

   ```cli
   az group create -l eastus -n IoTHubCLI
   ```

   ![Criar grupo de recursos][2]

Crie um Hub IoT chamado **CLIDemoHub** no grupo de recursos criado recentemente:

   ```cli
   az iot hub create --name CLIDemoHub --resource-group IoTHubCLI --sku S1
   ```

   >[!TIP]
   >Cada assinatura é atribuída a um Hub IoT gratuito. Para criar um hub gratuito com o comando CLI, substitua o valor SKU por `--sku F1`. Se você já tiver um hub gratuito na sua assinatura, uma mensagem de erro será exibida ao tentar criar um segundo. 

Crie um dispositivo IoT Edge:

   ```cli
   az iot hub device-identity create --device-id edge001 -hub-name CLIDemoHub --edge-enabled
   ```

   ![Criar dispositivo IoT Edge][4]

## <a name="configure-the-iot-edge-device"></a>Configurar o dispositivo IoT Edge

Crie um modelo JSON de implantação e salve-o localmente como um arquivo txt. Você precisará do caminho para o arquivo quando executar o comando para a aplicar configuração.

Os modelos JSON de implantação sempre devem incluir os dois módulos do sistema, edgeAgent e edgeHub. Além desses dois, é possível usar esse arquivo para implantar módulos adicionais no dispositivo IoT Edge. Use o exemplo a seguir para configurar o dispositivo IoT Edge com um módulo tempSensor:

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

Aplique a configuração no seu dispositivo IoT Edge:

   ```cli
   az iot hub apply-configuration --device-id edge001 --hub-name CLIDemoHub --content C:\<configuration.txt file path>
   ```

Exiba os módulos no seu dispositivo IoT Edge:
    
   ```cli
   az iot hub module-identity list --device-id edge001 --hub-name CLIDemoHub
   ```

   ![Listar módulos][6]

## <a name="next-steps"></a>Próximas etapas

* Saiba mais como [usar um dispositivo IoT Edge como um gateway](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.png
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.png

