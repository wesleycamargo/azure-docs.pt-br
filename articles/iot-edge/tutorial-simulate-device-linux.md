---
title: Simular o Azure IoT Edge no Linux | Microsoft Docs
description: "Instalar o tempo de execução do Azure IoT Edge em um dispositivo simulado no Linux e implantar seu primeiro módulo"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 10/16/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 327a959ad97897fd19f45a0599f37492938df104
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux---preview"></a>Implantar o Azure IoT Edge em um dispositivo simulado no Linux – versão prévia

O Azure IoT Edge permite executar análise e processamento de dados em seus dispositivos em vez de enviar por push todos os dados para a nuvem. Os tutoriais do IoT Edge demonstram como implantar diferentes tipos de módulos, criados de serviços do Azure ou de código personalizado, mas primeiro você precisa de um dispositivo para teste. 

Neste tutorial, você aprenderá a:

1. Crie um Hub IoT
2. Registrar um dispositivo IoT Edge
3. Iniciar o tempo de execução do IoT Edge
4. Implantar um módulo

![Arquitetura do tutorial][2]

O dispositivo simulado criado por você neste tutorial é um monitor que gera dados de temperatura, umidade e pressão. Os outros tutoriais do Azure IoT Edge se baseiam no trabalho feito aqui com a implantação de módulos que analisam os dados para obter informações de negócios. 

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você esteja usando um computador ou uma máquina virtual que executa o Linux para simular um dispositivo da Internet das Coisas. Os serviços a seguir são necessários para implantar com êxito um dispositivo IoT Edge:

- [Instale o Docker para Linux][lnk-docker-ubuntu] e verifique se ele está em execução. 
- A maioria das distribuições Linux, incluindo o Ubuntu, já tem o Python 2.7 instalado. Use o seguinte comando para verificar se o pip está instalado: `sudo apt-get install python-pip`.

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Inicie o tutorial pela criação do Hub IoT.
![Criar o Hub IoT][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrar um dispositivo IoT Edge

Registre um dispositivo IoT Edge no Hub IoT recém-criado.
![Registrar um dispositivo][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalar e iniciar o tempo de execução do IoT Edge

Instale e inicie o tempo de execução do Azure IoT Edge no dispositivo. 
![Registrar um dispositivo][5]

O tempo de execução do IoT Edge é implantado em todos os dispositivos IoT Edge. Ele consiste em dois módulos. O **agente do IoT Edge** facilita a implantação e o monitoramento de módulos no dispositivo IoT Edge. O **hub IoT Edge** gerencia a comunicação entre os módulos no dispositivo IoT Edge e entre o dispositivo e o Hub IoT. Quando você configura o tempo de execução em seu novo dispositivo, somente o agente do IoT Edge começará primeiro. O hub IoT Edge virá posteriormente, quando você implantar um módulo. 

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

![Conferir o edgeAgent no Docker](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>Implantar um módulo

Gerencie o dispositivo Azure IoT Edge na nuvem para implantar um módulo que enviará dados telemétricos ao Hub IoT.
![Registrar um dispositivo][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Exibir os dados gerados

Neste tutorial, você criou um novo dispositivo IoT Edge e instalou o tempo de execução de IoT Edge nele. Em seguida, você usou o Portal do Azure para enviar por push um módulo do IoT Edge para ser executado no dispositivo sem precisar fazer alterações no próprio dispositivo. Nesse caso, o módulo enviado por push cria dados de ambiente que podem ser usados para os tutoriais. 

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

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um novo dispositivo IoT Edge e usou a interface de nuvem do Azure IoT Edge para implantar código no dispositivo. Agora, você tem um dispositivo simulado que gera dados brutos sobre seu ambiente. 

Este tutorial é pré-requisito para todos os outros tutoriais IoT Edge. Prossiga para um dos outros tutoriais para saber como o Azure IoT Edge pode ajudá-lo a transformar esses dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Desenvolver e implantar código C# como um módulo](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/tutorial-install-iot-edge/view-module.png
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
