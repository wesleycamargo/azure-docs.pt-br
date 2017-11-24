---
title: Instalar o Azure IoT Edge no IoT Core | Microsoft Docs
description: "Instalar o tempo de execução do Azure IoT Edge em um dispositivo Windows IoT Core"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: be2a80645d23e709d6c5cfb3978498bbe85eca34
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Instalar o tempo de execução do Azure IoT Edge em um dispositivo Windows IoT Core - versão prévia

O tempo de execução do Azure IoT Edge pode executar até mesmo em pequenos dispositivos de computador de placa única (SBC) que são muito predominantes do setor de IoT. Este artigo o orienta por meio do provisionamento de tempo de execução em um [MinnowBoard Turbot][lnk-minnow] executando o Windows IoT Core de quadro de desenvolvimento.

## <a name="install-the-runtime"></a>Instalar o tempo de execução

1. Instalar [painel do Windows 10 IoT Core][lnk-core] em um sistema de host.
1. Siga as etapas em [configurar seu dispositivo] [ lnk-board] para configurar seu quadro com a imagem MinnowBoard Turbot/máx imagem de compilação 16299. 
1. Ligar o dispositivo, em seguida, [logar remotamente com o PowerShell][lnk-powershell].
1. No console do PowerShell, [instalar binários de Docker][lnk-docker-install].
1. Execute o seguinte comando no console do PowerShell para instalar o tempo de execução do IoT Edge e verificar a configuração:

   ```powershell
   Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
   ```

   Esse script faz o seguinte: 
   * Python 3.6
   * O script de controle IoT Edge (iotedgectl.exe)

Você pode ver a informação de saída da ferramenta iotedgectl.exe em vermelho na janela do PowerShell remoto. Isso não indica necessariamente erros. 

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