---
title: Instalar o Azure IoT Edge no IoT Core | Microsoft Docs
description: "Instalar o tempo de execução do Azure IoT Edge em um dispositivo Windows IoT Core"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: cc34e5cecafe485608ba428395b690ba57f71e9c
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2017
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Instalar o tempo de execução do Azure IoT Edge em um dispositivo Windows IoT Core - versão prévia

Azure IoT Edge e [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) trabalham juntos para permitir computação de borda até em dispositivos pequenos. O tempo de execução do Azure IoT Edge pode executar até mesmo em pequenos dispositivos de computador de placa única (SBC) que são muito predominantes do setor de IoT. 

Este artigo o orienta por meio do provisionamento de tempo de execução em um [MinnowBoard Turbot][lnk-minnow] executando o Windows IoT Core de quadro de desenvolvimento. O Windows IoT Core oferece suporte ao Azure IoT Edge somente em processadores Intel baseados em x64. 

## <a name="install-the-runtime"></a>Instalar o tempo de execução

1. Instalar [painel do Windows 10 IoT Core][lnk-core] em um sistema de host.
1. Siga as etapas em [configurar seu dispositivo] [ lnk-board] para configurar seu quadro com a imagem MinnowBoard Turbot/máx imagem de compilação 16299. 
1. Ligar o dispositivo, em seguida, [logar remotamente com o PowerShell][lnk-powershell].
1. No console do PowerShell, instale o tempo de execução do contêiner: 

   ```powershell
   Invoke-WebRequest https://master.dockerproject.org/windows/x86_64/docker-17.06.0-dev.zip -o temp.zip
   Expand-Archive .\temp.zip $env:ProgramFiles -f
   Remove-Item .\temp.zip
   $env:Path += ";$env:programfiles\docker"
   SETX /M PATH "$env:Path"
   dockerd --register-service
   start-service docker
   ```

   >[!NOTE]
   >Esse tempo de execução do contêiner é do servidor de build de projeto Moby e destina-se apenas a fins de avaliação. Ele não é testado, aprovadas nem tem suporte do Docker.

1. Instale o tempo de execução do IoT Edge e verifique a configuração:

   ```powershell
   Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
   ```

   Esse script faz o seguinte: 
   * Python 3.6
   * O script de controle IoT Edge (iotedgectl.exe)

Você pode ver as informações da ferramenta iotedgectl.exe em verde na janela do PowerShell remoto. Isso não indica necessariamente erros. 

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo que executa o tempo de execução do IoT Edge, saiba como [implantar e monitorar os módulos de IoT Edge em escala][lnk-deploy].

<!--Links-->
[lnk-minnow]: https://minnowboard.org/ 
[lnk-core]: https://docs.microsoft.com/windows/iot-core/connect-your-device/iotdashboard
[lnk-board]: https://developer.microsoft.com/windows/iot/Docs/GetStarted/mbm/sdcard/stable/getstartedstep2
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
