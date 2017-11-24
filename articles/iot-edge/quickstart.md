---
title: "Início rápido ao Azure IoT Edge + Windows | Microsoft Docs"
description: "Experimente o Azure IoT Edge executando a análise em um dispositivo de borda simulado"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e10b5dba6f91c97a5c6b71aee76eef062a8be82c
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Início rápido: implantar seu primeiro módulo IoT Edge do Portal do Azure para um dispositivo Windows – versão prévia

Neste guia de início rápido, use a interface de nuvem do Azure IoT Edge para implantar remotamente um código pré-compilado em um dispositivo IoT Edge. Para realizar essa tarefa, primeiro use seu dispositivo Windows para simular um dispositivo IoT Edge, depois você pode implantar um módulo a ele.

Se você não tiver uma assinatura do Azure ativa, crie uma [conta gratuita][lnk-account] antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você esteja usando um computador ou uma máquina virtual que execute o Windows para simular um dispositivo da Internet das Coisas. Caso esteja executando o Windows em uma máquina virtual, habilite a [virtualização aninhada][lnk-nested] e aloque pelo menos 2 GB de memória. 

1. Verifique se você está usando uma versão do Windows que tem suporte:
   * Windows 10 
   * Windows Server
2. Instale o [Docker para Windows][lnk-docker] e verifique se ele está em execução.
3. Instale o [Python 2.7 no Windows][lnk-python] e verifique se você pode usar o comando Pip.
4. Execute o comando a seguir para baixar o script de controle do IoT Edge.

   ```
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> O Azure IoT Edge pode executar contêineres do Windows ou do Linux. Para usar contêineres do Windows, você precisa executar:
>    * Windows 10 Fall Creators Update ou
>    * Windows Server 1709 (Build 16299) ou
>    * Windows IoT Core (Build 16299) em um dispositivo baseado em x64
>
> Para o Windows IoT Core, siga as instruções em [Instalar o tempo de execução do IoT Edge no Windows IoT Core][lnk-install-iotcore]. Caso contrário, basta [configurar o Docker para usar contêineres do Windows][lnk-docker-containers] e, como opção, validar seus pré-requisitos com o seguinte comando do PowerShell:
>    ```
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```

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

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Configurar o tempo de execução do IoT Edge

O tempo de execução do IoT Edge é implantado em todos os dispositivos IoT Edge. Ele consiste em dois módulos. Primeiro, o agente do IoT Edge facilita a implantação e o monitoramento de módulos no dispositivo IoT Edge. Em segundo lugar, o hub IoT Edge gerencia a comunicação entre os módulos no dispositivo IoT Edge e entre o dispositivo e o Hub IoT. 

Configure o tempo de execução com a cadeia de conexão do dispositivo IoT Edge da seção anterior.

```
iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Inicie o tempo de execução.

```
iotedgectl start
```

Verifique o Docker para ver se o agente IoT Edge está sendo executado como um módulo.

```
docker ps
```

## <a name="deploy-a-module"></a>Implantar um módulo

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Exibir os dados gerados

Neste guia de início rápido, você criou um novo dispositivo IoT Edge e instalou o tempo de execução de IoT Edge nele. Em seguida, você usou o Portal do Azure para enviar por push um módulo do IoT Edge para ser executado no dispositivo sem precisar fazer alterações no próprio dispositivo. Nesse caso, o módulo enviado por push cria dados de ambiente que podem ser usados para os tutoriais. 

Exiba as mensagens que estão sendo enviadas do módulo tempSensor:

```cmd/sh
docker logs -f tempSensor
```

Também exiba a telemetria sendo enviada pelo dispositivo usando a [ferramenta Gerenciador do Hub IoT][lnk-iothub-explorer]. 
## <a name="clean-up-resources"></a>Limpar recursos

Quando não precisar mais do Hub IoT criado, você pode usar o comando [az iot hub delete][lnk-delete] para remover o recurso e quaisquer dispositivos associados a ele:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Próximas etapas

Você sabe como implantar um módulo IoT Edge a um dispositivo IoT Edge. Agora tente implantar diferentes tipos de serviços do Azure como módulos para que você possa analisar dados na borda. 

* [Implantar o Azure Function como um módulo](tutorial-deploy-function.md)
* [Implantar o Azure Stream Analytics como um módulo](tutorial-deploy-stream-analytics.md)
* [Implantar seu próprio código como um módulo](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-python]: https://www.python.org/downloads/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
