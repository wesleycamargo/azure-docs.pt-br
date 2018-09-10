---
title: Implantar módulos do Azure IoT Edge (CLI) | Microsoft Docs
description: Usar a extensão do IoT para CLI 2.0 do Azure para implantar módulos em um dispositivo do IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 07/27/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 29c11139a2c773db2d26bf44984ad4dc72f2d870
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324598"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli-20"></a>Implantar módulos do Azure IoT Edge com CLI 2.0 do Azure

Após criar os módulos do IoT Edge com a lógica de negócios, você pretende implantá-los nos dispositivos para operar na borda. Se houver vários módulos trabalhando em conjunto para coletar e processar dados, será possível implantá-los todos de uma vez e declarar as regras de roteamento que conecta-os. 

A [CLI do Azure 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando de software livre para gerenciamento dos recursos do Azure como, por exemplo, o IoT Edge. Isso permite que você gerencie instantaneamente recursos, instâncias de serviço de provisionamento de dispositivos e hubs vinculados do Hub IoT. A nova extensão de IoT aprimora a CLI do Azure 2.0 com recursos como gerenciamento de dispositivos e todos os recursos do IoT Edge.

Este artigo mostra como criar um manifesto de implantação JSON e usar esse arquivo para enviar a implantação a um dispositivo do IoT Edge. Para obter informações sobre como criar uma implantação voltada a vários dispositivos com base nas marcações compartilhadas, consulte [Implantar e monitorar módulos do IoT Edge em escala](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Pré-requisitos

* Um [Hub IoT](../iot-hub/iot-hub-create-using-cli.md) na assinatura do Azure. 
* Um [Dispositivo do IoT Edge](how-to-register-device-cli.md) com o tempo de execução do IoT Edge instalado.
* [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) no seu ambiente. A versão 2.0 da CLI do Azure deve ser 2.0.24 ou superior. Use `az –-version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack. 
* [Extensão de IoT para CLI 2.0 do Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

Para implantar módulos usando a CLI 2.0 do Azure, salve o manifesto de implantação localmente como um arquivo. JSON. Você usará o caminho do arquivo na próxima seção quando executar o comando para aplicar a configuração ao dispositivo. 

A seguir, é apresentado um manifesto básico de implantação com um módulo como um exemplo:

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
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
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
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

## <a name="deploy-to-your-device"></a>Implantar no seu dispositivo

Você implanta módulos no dispositivo, aplicando o manifesto de implantação configurado com as informações do módulo. 

Altere os diretórios para a pasta onde o manifesto de implantação é salvo. Se você tiver usado um dos modelos de borda de IoT de código do VS, use o `deployment.json` arquivo o **config** pasta do seu diretório de solução. Não use o `deployment.template.json` arquivo. 

Use o comando a seguir para aplicar a configuração a um dispositivo do IoT Edge:

   ```cli
   az iot hub apply-configuration --device-id [device id] --hub-name [hub name] --content [file path]
   ```

O parâmetro da id do dispositivo diferencia maiúsculas de minúsculas. O parâmetro de conteúdo aponta para o arquivo de manifesto de implantação que você salvou. 

   ![Definir Módulos](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Exibir módulos no dispositivo

Após implantar os módulos no dispositivo, será possível exibir todos eles com o comando a seguir: 

Exiba os módulos no seu dispositivo IoT Edge:
    
   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

O parâmetro da id do dispositivo diferencia maiúsculas de minúsculas.

   ![Listar módulos](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Próximas etapas

Saiba como [Implantar e monitorar módulos do IoT Edge em escala](how-to-deploy-monitor.md)