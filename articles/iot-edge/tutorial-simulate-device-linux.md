---
title: Simular o Azure IoT Edge no Linux | Microsoft Docs
description: "Instalar o tempo de execução do Azure IoT Edge em um dispositivo simulado no Linux e implantar seu primeiro módulo"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 041919fd729880d429e08d8942f8d1ee087ccf61
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux---preview"></a>Implantar o Azure IoT Edge em um dispositivo simulado no Linux – versão prévia

O Azure IoT Edge permite executar análise e processamento de dados em seus dispositivos em vez de enviar por push todos os dados para a nuvem. Os tutoriais do IoT Edge demonstram como implantar diferentes tipos de módulos, criados de serviços do Azure ou de código personalizado, mas primeiro você precisa de um dispositivo para teste. 

Este tutorial mostra o passo a passo da criação de um dispositivo simulado do IoT Edge que gera dados de sensor. Você aprenderá como:

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

O tempo de execução do IoT Edge é implantado em todos os dispositivos IoT Edge. Ele consiste em dois módulos. Primeiro, o agente do IoT Edge facilita a implantação e o monitoramento de módulos no dispositivo IoT Edge. Em segundo lugar, o hub IoT Edge gerencia a comunicação entre os módulos no dispositivo IoT Edge e entre o dispositivo e o Hub IoT. 

Use as seguintes etapas para instalar e iniciar o tempo de execução do IoT Edge:

1. No computador onde você executará o dispositivo IoT Edge, baixe o script de controle do IoT Edge.

   ```
   sudo pip install -U azure-iot-edge-runtime-ctl
   ```

1. Configure o tempo de execução com a cadeia de conexão do dispositivo IoT Edge da seção anterior.

   ```
   sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
   ```

1. Inicie o tempo de execução.

   ```
   sudo iotedgectl start
   ```

1. Verifique o Docker para ver se o agente IoT Edge está sendo executado como um módulo.

   ```
   sudo docker ps
   ```

## <a name="deploy-a-module"></a>Implantar um módulo

Gerencie o dispositivo Azure IoT Edge na nuvem para implantar um módulo que enviará dados telemétricos ao Hub IoT.
![Registrar um dispositivo][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Exibir os dados gerados

Neste guia de início rápido, você criou um novo dispositivo IoT Edge e instalou o tempo de execução de IoT Edge nele. Em seguida, você usou o Portal do Azure para enviar por push um módulo do IoT Edge para ser executado no dispositivo sem precisar fazer alterações no próprio dispositivo. Nesse caso, o módulo enviado por push cria dados de ambiente que podem ser usados para os tutoriais. 

Exiba as mensagens que estão sendo enviadas do módulo tempSensor:

```cmd/sh
docker logs -f tempSensor
```

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
