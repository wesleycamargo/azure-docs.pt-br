---
title: Início rápido do Azure IoT Edge + Linux | Microsoft Docs
description: Neste início rápido, aprenda a implantar código pré-compilado remotamente em um dispositivo IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/14/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 2d988d70285e64414277bc2337a564aa87e45201
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888130"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>Início Rápido: implantar seu primeiro módulo IoT Edge em um dispositivo Linux x64

O Azure IoT Edge leva o poder da nuvem para seus dispositivos Internet das Coisas. Neste início rápido, aprenda a usar a interface de nuvem para implantar remotamente um código pré-compilado em um dispositivo IoT Edge.

Neste guia de início rápido, você aprende a:

1. Crie um Hub IoT.
2. Registrar um dispositivo IoT Edge em seu Hub IoT.
3. Instale e inicie o tempo de execução do IoT Edge no dispositivo.
4. Implantar um módulo em um dispositivo IoT Edge remotamente.

![Arquitetura do início rápido](./media/quickstart-linux/install-edge-full.png)

Este guia de início rápido transforma seu computador máquina virtual Linux em um dispositivo IoT Edge. Em seguida, você pode implantar um módulo do portal do Azure em seu dispositivo. O módulo implantado neste guia de início rápido é um sensor simulado que gera dados de temperatura, umidade e pressão. Os outros tutoriais do Azure IoT Edge se baseiam no trabalho feito aqui com a implantação de módulos que analisam os dados simulados para obter informações de negócios.

Se você não tiver uma assinatura do Azure ativa, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você usa a CLI do Azure para concluir muitas das etapas neste guia de início rápido e o Azure IoT possui uma extensão para habilitar funcionalidades adicionais. 

Adicione a extensão do Azure IoT à instância do shell de nuvem.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prerequisites"></a>Pré-requisitos

Recursos de nuvem: 

* Um grupo de recursos para gerenciar todos os recursos que você usará neste início rápido. 

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Dispositivo IoT Edge:

* Uma máquina virtual ou dispositivo com Linux para agir como o dispositivo IoT Edge. É recomendável usar a máquina virtual do [Azure IoT Edge no Ubuntu](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) fornecida pela Microsoft, que pré-instalará o tempo de execução do IoT Edge. Crie essa máquina virtual usando o seguinte comando:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   Ao criar uma nova máquina virtual, anote o **publicIpAddress**, que é fornecido como parte da saída do comando create. Você usará esse endereço IP público para se conectar à máquina virtual posteriormente no início rápido.

* Se você preferir executar o tempo de execução do Azure IoT Edge em seu sistema local, siga as instruções em [Instalar o tempo de execução do Azure IoT Edge no Linux (x64)](how-to-install-iot-edge-linux.md).

* Se você quiser usar um dispositivo baseado em ARM32, como Raspberry Pi, siga as instruções em [Instalar o tempo de execução do Azure IoT Edge no Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md).

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Comece o início rápido criando o Hub IoT com a CLI do Azure.

![Criar Hub IoT](./media/quickstart-linux/create-iot-hub.png)

O nível gratuito do Hub IoT funciona para este guia de início rápido. Se você tiver usado o Hub IoT antes e já tiver um hub disponível criado, você pode usar esse Hub IoT. Cada assinatura pode ter somente um hub IoT gratuito. 

O código a seguir cria um hub **F1** gratuito no grupo de recursos **IoTEdgeResources**. Substitua *{hub_name}* por um nome exclusivo para o Hub IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 
   ```

   Se você receber um erro porque já exsite um hub gratuito na sua assinatura, altere o SKU para **S1**. Caso você receba um erro que o nome do Hub IoT não está disponível, isso significa que alguém já tem um hub com esse nome. Tente usar um novo nome. 

## <a name="register-an-iot-edge-device"></a>Registrar um dispositivo IoT Edge

Registre um dispositivo IoT Edge no Hub IoT recém-criado.
![Registrar um dispositivo](./media/quickstart-linux/register-device.png)

Crie uma identidade de dispositivo para seu dispositivo simulado para que ele possa se comunicar com o hub IoT. A identidade do dispositivo reside na nuvem e você usa uma cadeia de conexão de dispositivo exclusiva para associar um dispositivo físico a uma identidade do dispositivo. 

Como os dispositivos IoT Edge se comportam e podem ser gerenciados diferentemente de dispositivos IoT comuns, declare essa identidade para ser um dispositivo IoT Edge com o sinalizador `--edge-enabled`. 

1. No Azure Cloud Shell, digite o seguinte comando para criar um dispositivo denominado **myEdgeDevice** no seu hub.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

   Se você receber um erro sobre as chaves de política do iothubowner, verifique se seu cloud shell está executando a versão mais recente da extensão do azure-cli-iot-ext. 

2. Recupere a cadeia de conexão para o seu dispositivo, o que vincula o dispositivo físico à sua identidade no Hub IoT. 

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Copie a cadeia de conexão e salve-a. Você usará esse valor para configurar o tempo de execução de IoT Edge na próxima seção. 

## <a name="connect-the-iot-edge-device-to-iot-hub"></a>Conecte o dispositivo do IoT Edge ao Hub IoT

Instale e inicie o tempo de execução do Azure IoT Edge no dispositivo IoT Edge. 
![Registrar um dispositivo](./media/quickstart-linux/start-runtime.png)

O tempo de execução do IoT Edge é implantado em todos os dispositivos IoT Edge. Tem três componentes. O **daemon de segurança do IoT Edge** é iniciado sempre que um dispositivo Edge é iniciado e inicializa o dispositivo inicializando o agente do IoT Edge. O **agente do IoT Edge** facilita a implantação e o monitoramento de módulos no dispositivo IoT Edge, incluindo o hub do IoT Edge. O **hub IoT Edge** gerencia a comunicação entre os módulos no dispositivo IoT Edge e entre o dispositivo e o Hub IoT. 

Durante a configuração do tempo de execução, você precisa fornecer uma cadeia de conexão do dispositivo. Use a cadeia de caracteres que você recuperou da CLI do Azure. Essa cadeia de caracteres associa seu dispositivo físico à identidade do dispositivo IoT Edge no Azure. 

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>Configure a cadeia de conexão do dispositivo do IoT Edge

* Se você estiver usando o Azure IoT Edge na máquina virtual do Ubuntu, use a cadeia de conexão de dispositivo que você copiou antes para configurar remotamente seu dispositivo IoT Edge:

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script '/etc/iotedge/configedge.sh "{device_connection_string}"'
   ```

   Para as etapas restantes, recupere o endereço IP público gerado pelo comando de criação. Também é possível encontrar o endereço IP público na página de visão geral da máquina virtual no portal do Azure. Use o comando a seguir para se conectar à sua máquina virtual. Substitua **{publicIpAddress}** pelo seu endereço de máquina. 

   ```azurecli-interactive
   ssh azureuser@{publicIpAddress}
   ```

* Se você estiver executando o IoT Edge em seu computador local ou em um dispositivo ARM32, abra o arquivo de configuração localizado em /etc/iotedge/config.yaml e atualize a variável **device_connection_string** com o valor copiado anteriormente, em seguida, reinicie o daemon de segurança do IoT Edge para aplicar as alterações:

   ```bash
   sudo systemctl restart iotedge
   ```

>[!TIP]
>Você precisa de privilégios elevados para executar comandos `iotedge`. Depois que você sair da sua máquina e fizer login novamente na primeira vez após instalar o tempo de execução do IoT Edge, suas permissões serão atualizadas automaticamente. Até lá, use **sudo** na frente dos comandos. 

### <a name="view-the-iot-edge-runtime-status"></a>Veja o status do tempo de execução do IoT Edge

Verifique se o tempo de execução foi instalado e configurado com êxito.

1. Verifique se o daemon de segurança do Edge está em execução como um serviço do sistema.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Veja o Daemon do Edge em execução como um serviço do sistema](./media/quickstart-linux/iotedged-running.png)

2. Se você precisar solucionar problemas do serviço, recupere os logs de serviço. 

   ```bash
   journalctl -u iotedge
   ```

3. Visualizar os módulos em execução no seu dispositivo. 

   ```bash
   sudo iotedge list
   ```

   ![Exibir um módulo no dispositivo](./media/quickstart-linux/iotedge-list-1.png)

Seu dispositivo IoT Edge agora está configurado. Ele está pronto para executar os módulos implantados na nuvem. 

## <a name="deploy-a-module"></a>Implantar um módulo

Gerencie o dispositivo Azure IoT Edge na nuvem para implantar um módulo que enviará dados telemétricos ao Hub IoT.
![Registrar um dispositivo](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Exibir os dados gerados

Neste guia de início rápido, você criou um novo dispositivo IoT Edge e instalou o tempo de execução de IoT Edge nele. Em seguida, você usou o Portal do Azure para enviar por push um módulo do IoT Edge para ser executado no dispositivo sem precisar fazer alterações no próprio dispositivo. Nesse caso, o módulo enviado por push cria dados de ambiente que podem ser usados para os tutoriais.

Abra novamente o prompt de comando no seu dispositivo IoT Edge ou use a conexão SSH na CLI do Azure. Confirme se o módulo implantado da nuvem está em execução no seu dispositivo IoT Edge:

   ```bash
   sudo iotedge list
   ```

   ![Exibir três módulos no seu dispositivo](./media/quickstart-linux/iotedge-list-2.png)

Exiba as mensagens que estão sendo enviadas do módulo tempSensor:

   ```bash
   sudo iotedge logs tempSensor -f
   ```

![Exibir os dados do seu módulo](./media/quickstart-linux/iotedge-logs.png)

O módulo de sensor de temperatura poderá estar aguardando para se conectar ao Hub do Edge se a última linha que você vê no log for `Using transport Mqtt_Tcp_Only`. Tente eliminar o módulo e deixar que o agente do Edge o reinicie. Você pode encerrá-la com o comando `sudo docker stop tempSensor`.

Você também pode exibir as mensagens que são recebidas pelo seu Hub IoT usando a [Extensão de Kit de Ferramentas do Azure IoT para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Limpar recursos

Se você deseja prosseguir para os tutoriais do IoT Edge, pode usar o dispositivo registrado e configurado neste guia de início rápido. Caso contrário, é possível excluir os recursos do Azure que você criou e remover o tempo de execução do IoT Edge do seu dispositivo.

### <a name="delete-azure-resources"></a>Excluir recursos do Azure

Se você tiver criado a sua máquina virtual e o Hub IoT em um novo grupo de recursos, é possível excluir esse grupo e todos os recursos associados. Verifique novamente o conteúdo do grupo de recursos para ter certeza de que não haja nada que você queira manter. Caso não queira excluir o grupo inteiro, é possível excluir recursos individuais em vez disso.

Remova o grupo **IoTEdgeResources**.

   ```azurecli-interactive
   az group delete --name IoTEdgeResources 
   ```

### <a name="remove-the-iot-edge-runtime"></a>Remover o tempo de execução do IoT Edge

Se você deseja remover as instalações do dispositivo, use os comandos a seguir.  

Remova o tempo de execução do IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Quando o tempo de execução do IoT Edge for removido, os contêineres criados por ele são interrompidos, mas ainda existem no seu dispositivo. Visualizar todos os contêineres.

   ```bash
   sudo docker ps -a
   ```

Exclua os contêineres que foram criados no seu dispositivo pelo tempo de execução do IoT Edge. Altere o nome do contêiner tempSensor se você deu um outro nome para ele. 

   ```bash
   sudo docker rm -f tempSensor
   sudo docker rm -f edgeHub
   sudo docker rm -f edgeAgent
   ```

Remova o tempo de execução do contêiner.

   ```bash
   sudo apt-get remove --purge moby-cli
   sudo apt-get remove --purge moby-engine
   ```

## <a name="next-steps"></a>Próximas etapas

Este guia de início rápido é pré-requisito para todos os tutoriais do IoT Edge. Prossiga para um dos outros tutoriais para saber como o Azure IoT Edge pode ajudá-lo a transformar esses dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Filtrar dados de sensor usando uma função do Azure](tutorial-deploy-function.md)
