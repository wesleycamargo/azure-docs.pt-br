---
title: "Início rápido do Azure IoT Edge + Linux | Microsoft Docs"
description: "Experimente o Azure IoT Edge executando a análise em um dispositivo de borda simulado"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 440b70f4d04728973d77e54e7f6303e1ad7fcd89
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-or-mac-device---preview"></a>Início rápido: implantar seu primeiro módulo IoT Edge em um dispositivo Linux ou Mac – versão prévia

O Azure IoT Edge leva o poder da nuvem para seus dispositivos Internet das Coisas. Neste tópico, aprenda a usar a interface de nuvem do Azure IoT Edge para implantar remotamente um código pré-compilado em um dispositivo IoT Edge.

Se você não tiver uma assinatura do Azure ativa, crie uma [conta gratuita][lnk-account] antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido usa seu computador ou máquina virtual como um dispositivo de Internet das Coisas. Para transformar seu computador em um dispositivo IoT Edge, os serviços a seguir são necessários:

* Python Pip, para instalar o tempo de execução do IoT Edge.
   * Linux: `sudo apt-get install python-pip`.
   * MacOS: `sudo easy_install pip`.
* Docker, para executar os módulos de IoT Edge
   * [Instale o Docker para Linux][lnk-docker-ubuntu] e verifique se ele está em execução. 
   * [Instale o Docker para Mac][lnk-docker-mac] e verifique se ele está em execução. 

## <a name="create-an-iot-hub-with-azure-cli"></a>Criar um hub IoT usando a CLI do Azure

Criar um hub IoT em sua assinatura do Azure. O nível gratuito do Hub IoT funciona para este guia de início rápido. Se já tiver usado o Hub IoT no passado e já tiver um hub gratuito criado, você pode ignorar esta seção e ir para [Registrar um dispositivo IoT Edge][anchor-register]. Cada assinatura pode ter somente um hub IoT gratuito. 

1. Entre no [Portal do Azure][lnk-portal]. 
1. Selecione o botão **Cloud Shell**. 

   ![Botão do Cloud Shell][1]

1. Crie um grupos de recursos. O código a seguir cria um grupo de recursos chamado **IoTEdge** na região **Oeste dos EUA**:

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. Crie um hub IoT em seu novo grupo de recursos. O código a seguir cria um hub **F1** gratuito chamado **MyIotHub** no grupo de recursos **IoTEdge**:

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Registrar um dispositivo IoT Edge

Crie uma identidade de dispositivo para seu dispositivo simulado para que ele possa se comunicar com o hub IoT. Como os dispositivos IoT Edge se comportam e podem ser gerenciados diferentemente de dispositivos IoT comuns, declare esse para ser um dispositivo de IoT Edge desde o início. 

1. No portal do Azure, navegue para o hub IoT.
1. Selecione **IoT Edge (versão prévia)**.
1. Selecione **Adicionar dispositivo com IoT Edge**.
1. Dê o seu dispositivo simulado uma ID de dispositivo exclusivo.
1. Selecione **Salvar** para adicionar seu dispositivo.
1. Selecione seu dispositivo na lista de dispositivos. 
1. Copie o valor de **cadeia de caracteres de Conexão - chave primária** e salve-o. Você usará esse valor para configurar o tempo de execução de IoT Edge na próxima seção. 

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalar e iniciar o tempo de execução do IoT Edge

O tempo de execução do IoT Edge é implantado em todos os dispositivos IoT Edge. Ele consiste em dois módulos. Primeiro, o agente do IoT Edge facilita a implantação e o monitoramento de módulos no dispositivo IoT Edge. Em segundo lugar, o hub IoT Edge gerencia a comunicação entre os módulos no dispositivo IoT Edge e entre o dispositivo e o Hub IoT. 

No computador onde você executará o dispositivo IoT Edge, baixe o script de controle do IoT Edge:
```cmd
sudo pip install -U azure-iot-edge-runtime-ctl
```

Configure o tempo de execução com a cadeia de conexão do dispositivo IoT Edge da seção anterior:
```cmd
sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Inicie o tempo de execução:
```cmd
sudo iotedgectl start
```

Verifique o Docker para ver se o agente IoT Edge está sendo executado como um módulo:
```cmd
sudo docker ps
```

![Confira o agente Edge no Docker](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>Implantar um módulo

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Exibir os dados gerados

Neste guia de início rápido, você criou um novo dispositivo IoT Edge e instalou o tempo de execução de IoT Edge nele. Em seguida, você usou o Portal do Azure para enviar por push um módulo do IoT Edge para ser executado no dispositivo sem precisar fazer alterações no próprio dispositivo. Nesse caso, o módulo enviado por push cria dados de ambiente que podem ser usados para os tutoriais. 

Abra o prompt de comando no computador executando o seu dispositivo simulado novamente. Confirme se o módulo implantado da nuvem está em execução no seu dispositivo IoT Edge:

```cmd
sudo docker ps
```

![Exibir três módulos no seu dispositivo](./media/tutorial-simulate-device-linux/docker-ps2.png)

Exiba as mensagens que estão sendo enviadas do módulo tempSensor para a nuvem:

```cmd
sudo docker logs -f tempSensor
```

![Exibir os dados do seu módulo](./media/tutorial-simulate-device-linux/docker-logs.png)

Também exiba a telemetria sendo enviada pelo dispositivo usando a [ferramenta Gerenciador do Hub IoT][lnk-iothub-explorer]. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando não precisar mais do Hub IoT criado, você pode usar o comando [az iot hub delete][lnk-delete] para remover o recurso e quaisquer dispositivos associados a ele:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Próximas etapas

Você sabe como implantar um módulo IoT Edge a um dispositivo IoT Edge. Agora tente implantar diferentes tipos de serviços do Azure como módulos para que você possa analisar dados na borda. 

* [Implantar seu próprio código como um módulo](tutorial-csharp-module.md)
* [Implantar o Azure Function como um módulo](tutorial-deploy-function.md)
* [Implantar o Azure Stream Analytics como um módulo](tutorial-deploy-stream-analytics.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-docker-mac]: https://docs.docker.com/docker-for-mac/install/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
