---
title: Simular o Azure IoT Edge no Windows | Microsoft Docs
description: "Instalar o tempo de execução do Azure IoT Edge em um dispositivo simulado no Windows e implantar seu primeiro módulo"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/16/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0207418cf71902ce9bc9d2911124d1d46889d893
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Implantar o Azure IoT Edge em um dispositivo simulado no Windows – versão prévia

O Azure IoT Edge permite executar análise e processamento de dados em seus dispositivos em vez de enviar por push todos os dados para a nuvem. Os tutoriais do IoT Edge demonstram como implantar diferentes tipos de módulos, criados de serviços do Azure ou de código personalizado, mas primeiro você precisa de um dispositivo para teste. 

Neste tutorial, você aprenderá a:

1. Crie um Hub IoT
2. Registrar um dispositivo IoT Edge
3. Iniciar o tempo de execução do IoT Edge
4. Implantar um módulo

![Arquitetura do tutorial][2]

O dispositivo simulado criado por você neste tutorial é um monitor em uma turbina eólica que gera temperatura, umidade e dados de pressão. Você está interessado nesses dados porque suas turbinas funcionam em diferentes níveis de eficiência, dependendo das condições climáticas. Os outros tutoriais do Azure IoT Edge se baseiam no trabalho feito aqui com a implantação de módulos que analisam os dados para obter informações de negócios. 

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você esteja usando um computador ou uma máquina virtual que execute o Windows para simular um dispositivo da Internet das Coisas. 

>[!TIP]
>Caso esteja executando o Windows em uma máquina virtual, habilite a [virtualização aninhada][lnk-nested] e aloque pelo menos 2 GB de memória. 

1. Verifique se você está usando uma versão do Windows que tem suporte:
   * Windows 10 
   * Windows Server
2. Instale o [Docker para Windows][lnk-docker] e verifique se ele está em execução.
3. Instale o [Python 2.7 no Windows][lnk-python] e verifique se você pode usar o comando Pip.
4. Execute o comando a seguir para baixar o script de controle do IoT Edge.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> O Azure IoT Edge pode executar contêineres do Windows ou do Linux. Se você estiver executando uma das seguintes versões do Windows, poderá usar contêineres do Windows:
>    * Windows 10 Fall Creators Update
>    * Windows Server 1709 (Build 16299)
>    * Windows IoT Core (Build 16299) em um dispositivo baseado em x64
>
> Para o Windows IoT Core, siga as instruções em [Instalar o tempo de execução do IoT Edge no Windows IoT Core][lnk-install-iotcore]. Caso contrário, simplesmente [configure o Docker para usar contêineres do Windows][lnk-docker-containers]. Use o comando a seguir para validar os pré-requisitos:
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>Crie um hub IoT

Inicie o tutorial pela criação do Hub IoT.
![Criar o Hub IoT][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrar um dispositivo IoT Edge

Registre um dispositivo IoT Edge no Hub IoT recém-criado.
![Registrar um dispositivo][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Configurar o tempo de execução do IoT Edge

Instale e inicie o tempo de execução do Azure IoT Edge no dispositivo. 
![Registrar um dispositivo][5]

O tempo de execução do IoT Edge é implantado em todos os dispositivos IoT Edge. Ele consiste em dois módulos. O **agente do IoT Edge** facilita a implantação e o monitoramento de módulos no dispositivo IoT Edge. O **hub IoT Edge** gerencia a comunicação entre os módulos no dispositivo IoT Edge e entre o dispositivo e o Hub IoT. Quando você configura o tempo de execução em seu novo dispositivo, somente o agente do IoT Edge começará primeiro. O hub IoT Edge virá posteriormente, quando você implantar um módulo. 


Configure o tempo de execução com a cadeia de conexão do dispositivo IoT Edge da seção anterior.

```cmd
iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Inicie o tempo de execução.

```cmd
iotedgectl start
```

Verifique o Docker para ver se o agente IoT Edge está sendo executado como um módulo.

```cmd
docker ps
```

![Conferir o edgeAgent no Docker](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>Implantar um módulo

Gerencie o dispositivo Azure IoT Edge na nuvem para implantar um módulo que enviará dados telemétricos ao Hub IoT.
![Registrar um dispositivo][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Exibir os dados gerados

Neste tutorial, você criou um novo dispositivo IoT Edge e instalou o tempo de execução de IoT Edge nele. Em seguida, você usou o Portal do Azure para enviar por push um módulo do IoT Edge para ser executado no dispositivo sem precisar fazer alterações no próprio dispositivo. Nesse caso, o módulo enviado por push cria dados de ambiente que podem ser usados para os tutoriais. 

Abra o prompt de comando no computador executando o seu dispositivo simulado novamente. Confirme se o módulo implantado da nuvem está em execução no seu dispositivo IoT Edge. 

```cmd
docker ps
```

![Exibir três módulos no seu dispositivo](./media/tutorial-simulate-device-windows/docker-ps2.png)

Exiba as mensagens que estão sendo enviadas do módulo tempSensor para a nuvem. 

```cmd
docker logs -f tempSensor
```

![Exibir os dados do seu módulo](./media/tutorial-simulate-device-windows/docker-logs.png)

Também exiba a telemetria sendo enviada pelo dispositivo usando a [ferramenta Gerenciador do Hub IoT][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um novo dispositivo IoT Edge e usou a interface de nuvem do Azure IoT Edge para implantar código no dispositivo. Agora, você tem um dispositivo simulado que gera dados brutos sobre seu ambiente. 

Este tutorial é pré-requisito para todos os outros tutoriais IoT Edge. Prossiga para um dos outros tutoriais para saber como o Azure IoT Edge pode ajudá-lo a transformar esses dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Desenvolver e implantar código C# como um módulo](tutorial-csharp-module.md)

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