---
title: Instalar o Azure IoT Edge no IoT Core | Microsoft Docs
description: Instalar o tempo de execução do Azure IoT Edge em um dispositivo Windows IoT Core
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f57db00894dab80f96f45111331d47a173520ced
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39575991"
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Instalar o tempo de execução do Azure IoT Edge em um dispositivo Windows IoT Core - versão prévia

Azure IoT Edge e [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) trabalham juntos para permitir computação de borda até em dispositivos pequenos. O tempo de execução do Azure IoT Edge pode executar até mesmo em pequenos dispositivos de computador de placa única (SBC) que são muito predominantes do setor de IoT. 

Este artigo o orienta por meio do provisionamento de tempo de execução em um quadro de desenvolvimento executando o Windows IoT Core. 

**Atualmente, Windows IoT Core oferece suporte ao Azure IoT Edge somente em processadores Intel baseados em x64.**

## <a name="install-the-container-runtime"></a>Instalar o contêiner de tempo de execução

1. Configurar seu quadro com imagem IoT Core **Build 17134 (RS4)**. 
1. Ligar o dispositivo, em seguida, [logar remotamente com o PowerShell][lnk-powershell].
1. No console do PowerShell, instale o tempo de execução do contêiner: 

   ```powershell
   Invoke-WebRequest https://master.dockerproject.org/windows/x86_64/docker-0.0.0-dev.zip -o temp.zip
   Expand-Archive .\temp.zip $env:ProgramFiles -f
   Remove-Item .\temp.zip
   $env:Path += ";$env:programfiles\docker"
   SETX /M PATH "$env:Path"
   dockerd --register-service
   start-service docker
   ```

   >[!NOTE]
   >Esse tempo de execução do contêiner é do servidor de build de projeto Moby e destina-se apenas a fins de avaliação. Ele não é testado, aprovadas nem tem suporte do Docker.

## <a name="finish-installing"></a>Terminar instalação

Instalar o Daemon de Segurança de IoT Edge e configurá-lo usando instruções [neste artigo][lnk-install-windows-on-windows]

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo que executa o tempo de execução do IoT Edge, saiba como [implantar e monitorar os módulos de IoT Edge em escala][lnk-deploy].

<!--Links-->
[lnk-install-windows-on-windows]: how-to-install-iot-edge-windows-with-windows.md
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
