---
title: Simular o Azure IoT Edge no Linux | Microsoft Docs
description: Neste início rápido, aprenda a implantar código pré-compilado remotamente em um dispositivo IoT Edge.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 6b63c10a8c092d6568f8caf9842f007a5dc9c027
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049155"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>Início Rápido: implantar seu primeiro módulo IoT Edge em um dispositivo Linux x64

O Azure IoT Edge permite executar análise e processamento de dados em seus dispositivos em vez de enviar por push todos os dados para a nuvem. Os tutoriais do IoT Edge demonstram como implantar diferentes tipos de módulos, mas primeiro você precisa de um dispositivo para teste. 

Neste guia de início rápido, você aprende a:

1. Criar um Hub IoT.
2. Registrar um dispositivo IoT Edge em seu hub IoT.
3. Iniciar o tempo de execução do IoT Edge.
4. Implantar um módulo em um dispositivo IoT Edge remotamente.

![Arquitetura do tutorial][2]

Este guia de início rápido transforma seu computador máquina virtual Linux em um dispositivo IoT Edge. Em seguida, você pode implantar um módulo do portal do Azure em seu dispositivo. O módulo implantado neste guia de início rápido é um sensor simulado que gera dados de temperatura, umidade e pressão. Os outros tutoriais do Azure IoT Edge se baseiam no trabalho feito aqui com a implantação de módulos que analisam os dados simulados para obter informações de negócios. 

Se você não tiver uma assinatura do Azure ativa, crie uma [conta gratuita][lnk-account] antes de começar.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Comece o início rápido criando o Hub IoT no portal do Azure.
![Criar o Hub IoT][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrar um dispositivo IoT Edge

Registre um dispositivo IoT Edge no Hub IoT recém-criado.
![Registrar um dispositivo][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]


## <a name="install-and-start-the-iot-edge-runtime"></a>Instalar e iniciar o tempo de execução do IoT Edge

Instale e inicie o tempo de execução do Azure IoT Edge no dispositivo. 
![Registrar um dispositivo][5]

O tempo de execução do IoT Edge é implantado em todos os dispositivos IoT Edge. Ele tem três componentes. O **daemon de segurança do IoT Edge** é iniciado sempre que um dispositivo Edge é iniciado e inicializa o dispositivo inicializando o agente do IoT Edge. O **agente do IoT Edge** facilita a implantação e o monitoramento de módulos no dispositivo IoT Edge, incluindo o hub do IoT Edge. O **hub IoT Edge** gerencia a comunicação entre os módulos no dispositivo IoT Edge e entre o dispositivo e o Hub IoT. 

### <a name="register-your-device-to-use-the-software-repository"></a>Registrar o dispositivo para usar o repositório de software

Os pacotes para executar o tempo de execução do IoT Edge são gerenciados em um repositório de software. Configure seu dispositivo IoT Edge para acessar esse repositório. 

As etapas nesta seção são para dispositivos que executam **16.04 Ubuntu**. Para acessar o repositório de software em outras versões do Linux, confira [Instalar o tempo de execução do Azure IoT Edge no Linux (x64)](how-to-install-iot-edge-linux.md) ou [Instalar o tempo de execução do Azure IoT Edge no Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md).

1. No computador que você está usando como um dispositivo IoT Edge, instale a configuração do repositório.

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

2. Instale uma chave pública para acessar o repositório.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>Instale um tempo de execução do contêiner

O tempo de execução do IoT Edge é um conjunto de contêineres, e a lógica que você implanta em seu dispositivo IoT Edge é empacotada como contêineres. Prepare o dispositivo para esses componentes instalando um tempo de execução do contêiner.

Atualize **apt-get**.

   ```bash
   sudo apt-get update
   ```

Instale o Moby, um tempo de execução do contêiner e seus comandos da CLI. 

   ```bash
   sudo apt-get install moby-engine
   sudo apt-get install moby-cli   
   ```

### <a name="install-and-configure-the-iot-edge-security-daemon"></a>Instalar e configurar o daemon de segurança do IoT Edge

O daemon de segurança é instalado como um serviço do sistema para que o tempo de execução do IoT Edge seja iniciado sempre que o dispositivo é inicializado. A instalação também inclui uma versão de **hsmlib** que permite que o daemon de segurança interaja com a segurança de hardware do dispositivo. 

1. Baixe e instale o Daemon de Segurança do IoT Edge. 

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

2. Abra o arquivo de configuração do IoT Edge. Ele é um arquivo protegido; talvez seja necessário usar privilégios elevados para acessá-lo.
   
   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

3. Adicione a cadeia de conexão do dispositivo IoT Edge copiada quando você registrou seu dispositivo. Substitua o valor da variável **device_connection_string** que você copiou anteriormente neste guia de início rápido.

4. Reinicie o Daemon de Segurança do Edge:

   ```bash
   sudo systemctl restart iotedge
   ```

5. Verifique se o Daemon de Segurança do Edge está em execução como um serviço do sistema:

   ```bash
   sudo systemctl status iotedge
   ```

   ![Veja o Daemon do Edge em execução como um serviço do sistema](./media/quickstart-linux/iotedged-running.png)

   Você também pode ver os logs do Daemon de Segurança do Edge executando o seguinte comando:

   ```bash
   journalctl -u iotedge
   ```

6. Exiba os módulos em execução no dispositivo: 

   ```bash
   iotedge list
   ```

   ![Exibir um módulo no dispositivo](./media/quickstart-linux/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Implantar um módulo

Gerencie o dispositivo Azure IoT Edge na nuvem para implantar um módulo que enviará dados telemétricos ao Hub IoT.
![Registrar um dispositivo][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Exibir os dados gerados

Neste guia de início rápido, você criou um novo dispositivo IoT Edge e instalou o tempo de execução de IoT Edge nele. Em seguida, você usou o Portal do Azure para enviar por push um módulo do IoT Edge para ser executado no dispositivo sem precisar fazer alterações no próprio dispositivo. Nesse caso, o módulo enviado por push cria dados de ambiente que podem ser usados para os tutoriais. 

Abra o prompt de comando no computador executando o seu dispositivo simulado novamente. Confirme se o módulo implantado da nuvem está em execução no seu dispositivo IoT Edge:

   ```bash
   iotedge list
   ```

   ![Exibir três módulos no seu dispositivo](./media/quickstart-linux/iotedge-list-2.png)

Exiba as mensagens que estão sendo enviadas do módulo tempSensor:

   ```bash
   iotedge logs tempSensor -f 
   ```

![Exibir os dados do seu módulo](./media/quickstart-linux/iotedge-logs.png)

O módulo de sensor de temperatura poderá estar aguardando para se conectar ao Hub do Edge se a última linha que você vê no log for `Using transport Mqtt_Tcp_Only`. Tente eliminar o módulo e deixar que o agente do Edge o reinicie. Você pode encerrá-la com o comando `sudo docker stop tempSensor`.

Você também pode exibir a telemetria que o dispositivo está enviando usando a [ferramenta exploradora do Hub IoT] [ lnk-iothub-explorer] ou a [extensão Kit de ferramentas do Azure IoT para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Limpar recursos

Se você deseja prosseguir para os tutoriais do IoT Edge, pode usar o dispositivo registrado e configurado neste guia de início rápido. Se você deseja remover as instalações do dispositivo, use os comandos a seguir.  

Remova o tempo de execução do IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Exclua os contêineres que foram criados no seu dispositivo. 

   ```bash
   sudo docker rm -f $(sudo docker ps -aq)
   ```

Remova o tempo de execução do contêiner.

   ```bash
   sudo apt-get remove --purge moby
   ```

Quando você não precisar mais do hub IoT do Azure ou do dispositivo IoT Edge criado neste guia de início rápido, poderá excluí-los no portal do Azure. Navegue até a página de visão geral do seu hub IoT e selecione **Excluir**. 

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um novo dispositivo IoT Edge e usou a interface de nuvem do Azure IoT Edge para implantar código no dispositivo. Agora, você tem um dispositivo simulado que gera dados brutos sobre seu ambiente. 

Este guia de início rápido é pré-requisito para todos os tutoriais do IoT Edge. Prossiga para outro tutorial a fim de descobrir como o Azure IoT Edge pode ajudá-lo a transformar esses dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Filtrar dados de sensor usando uma função do Azure](tutorial-deploy-function.md)


<!-- Images -->
[1]: ./media/quickstart-linux/view-module.png
[2]: ./media/quickstart-linux/install-edge-full.png
[3]: ./media/quickstart-linux/create-iot-hub.png
[4]: ./media/quickstart-linux/register-device.png
[5]: ./media/quickstart-linux/start-runtime.png
[6]: ./media/quickstart-linux/deploy-module.png
[7]: ./media/quickstart-linux/iotedged-running.png
[8]: ./media/tutorial-simulate-device-linux/running-modules.png
[9]: ./media/tutorial-simulate-device-linux/sensor-data.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
