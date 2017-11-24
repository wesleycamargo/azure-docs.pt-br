---
title: Simular o Azure IoT Edge no Windows | Microsoft Docs
description: "Instalar o tempo de execução do Azure IoT Edge em um dispositivo simulado no Windows e implantar seu primeiro módulo"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 08c501b9132bb21f47f099725d1fad5556befb4c
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Implantar o Azure IoT Edge em um dispositivo simulado no Windows – versão prévia

O Azure IoT Edge leva o poder da nuvem para seus dispositivos IoT (Internet das Coisas). Este tutorial orienta você pela criação de um dispositivo simulado do IoT Edge que gera dados de sensor. Você aprenderá como:

![Arquitetura do tutorial][2]

O dispositivo simulado criado por você neste tutorial é um monitor em uma turbina eólica que gera temperatura, umidade e dados de pressão. Você está interessado nesses dados porque suas turbinas funcionam em diferentes níveis de eficiência, dependendo das condições climáticas. Os outros tutoriais do Azure IoT Edge se baseiam no trabalho feito aqui com a implantação de módulos que analisam os dados para obter informações de negócios. 

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você esteja usando um computador ou uma máquina virtual que executa o Windows para simular um dispositivo da Internet das Coisas. 

>[!TIP]
>Caso esteja executando o Windows em uma máquina virtual, habilite a [virtualização aninhada][lnk-nested] e aloque, pelo menos, 2 GB de memória. 

1. Verifique se você está usando uma versão com suporte do Windows:
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
>    * Windows IoT Core (Build 16299) em um dispositivo com base em x64
>
> Para o Windows IoT Core, siga as instruções em [Instalar o tempo de execução do IoT Edge no Windows IoT Core][lnk-install-iotcore]. Caso contrário, basta [configurar o Docker para usar contêineres do Windows][lnk-docker-containers] e, opcionalmente, validar os pré-requisitos com o seguinte comando do PowerShell:
>    ```
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>Crie um hub IoT

Inicie o tutorial criando o Hub IoT.
![Criar o Hub IoT][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrar um dispositivo do IoT Edge

Registre um dispositivo do IoT Edge com o Hub IoT recém-criado.
![Registrar um dispositivo][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Configurar o tempo de execução do IoT Edge

Instale e inicie o tempo de execução do Azure IoT Edge no dispositivo. 
![Registrar um dispositivo][5]

O tempo de execução do IoT Edge é implantado em todos os dispositivos do IoT Edge. Ele consiste em dois módulos. Primeiro, o agente do IoT Edge facilita a implantação e o monitoramento de módulos no dispositivo do IoT Edge. Em segundo lugar, o hub do IoT Edge gerencia a comunicação entre os módulos no dispositivo do IoT Edge e entre o dispositivo e o Hub IoT. 


Use as seguintes etapas para instalar e iniciar o tempo de execução do IoT Edge:

1. Configure o tempo de execução com a cadeia de conexão do dispositivo do IoT Edge da seção anterior.

   ```
   iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
   ```

1. Inicie o tempo de execução.

   ```
   iotedgectl start
   ```

1. Verifique o Docker para ver se o agente do IoT Edge está sendo executado como um módulo.

   ```
   docker ps
   ```

## <a name="deploy-a-module"></a>Implantar um módulo

Gerencie o dispositivo do Azure IoT Edge na nuvem para implantar um módulo que enviará dados telemétricos ao Hub IoT.
![Registrar um dispositivo][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Exibir os dados gerados

Neste guia de início rápido, você criou um novo dispositivo do IoT Edge e instalou o tempo de execução de IoT Edge nele. Em seguida, você usou o portal do Azure para enviar por push um módulo do IoT Edge para ser executado no dispositivo sem precisar fazer alterações no próprio dispositivo. Nesse caso, o módulo enviado por push cria dados de ambiente que podem ser usados para os tutoriais. 

Exiba as mensagens que estão sendo enviadas do módulo tempSensor:

```cmd/sh
sudo docker logs -f tempSensor
```

Também exiba a telemetria enviada pelo dispositivo usando a [ferramenta Gerenciador do Hub IoT][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um novo dispositivo do IoT Edge e usou a interface de nuvem do Azure IoT Edge para implantar o código no dispositivo. Agora, você tem um dispositivo simulado que gera dados brutos sobre seu ambiente. 

Este tutorial é pré-requisito para todos os outros tutoriais do IoT Edge. Continue com um dos outros tutoriais para saber como o Azure IoT Edge pode ajudá-lo a transformar esses dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Desenvolver e implantar o código C# como um módulo](tutorial-csharp-module.md)

<!-- Images -->
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md