---
title: Instalar o Azure IoT Edge para Linux no Windows | Microsoft Docs
description: Instruções de instalação do IoT Edge do Azure para contêineres do Linux no Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: bb47a1b828084673961a6d2c5657793b4437f294
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160569"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Usar o IoT Edge no Windows para executar contêineres do Linux

Teste os módulos do IoT Edge para dispositivos do Linux usando um computador Windows. 

Em um cenário de produção, os dispositivos do Windows só devem executar contêineres do Windows. No entanto, um cenário de desenvolvimento comum é usar um computador Windows para criar módulos para dispositivos Linux de IoT Edge. O tempo de execução do IoT Edge para Windows permite que você executar contêineres do Linux para **teste e desenvolvimento** fins. 

Este artigo lista as etapas para instalar o tempo de execução do IoT Edge do Azure usando contêineres do Linux no seu Windows x64 (Intel/AMD) sistema. Para saber mais sobre o instalador de tempo de execução do IoT Edge, incluindo detalhes sobre todos os parâmetros de instalação, consulte [instalar o tempo de execução do Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md).

## <a name="prerequisites"></a>Pré-requisitos

Use esta seção para verificar se o dispositivo Windows pode dar suporte ao IoT Edge e prepará-lo para um mecanismo de contêiner antes da instalação. 

### <a name="supported-windows-versions"></a>Versões do Windows com suporte

O Azure IoT Edge com contêineres do Linux pode executar as seguintes versões do Windows: 
* Atualização de Aniversário do Windows 10 (build 14393) ou mais recente
* Windows Server 2016 ou mais recente

Para obter mais informações sobre o que está incluído na última versão do IoT Edge, consulte [Lançamentos do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Se você quiser instalar o IoT Edge em uma máquina virtual, habilitar a virtualização aninhada e aloque pelo menos 2 GB de memória. Como habilitar a virtualização aninhada é diferente dependendo de seu uso no hipervisor. Para o Hyper-V, máquinas virtuais de geração 2 têm aninhados virtualização habilitada por padrão. Para o VMWare, há uma alternância para habilitar o recurso em sua máquina virtual. 

### <a name="prepare-the-container-engine"></a>Preparar o mecanismo de contêiner 

O Azure IoT Edge depende de um mecanismo de contêiner [compatível com OCI](https://www.opencontainers.org/). A maior diferença de configuração entre a execução de contêineres do Windows e Linux em um Windows máquina é que a instalação do IoT Edge inclui um tempo de execução de contêiner do Windows, mas você precisa fornecer seu próprio tempo de execução para contêineres do Linux antes de instalar o IoT Edge. 

Para configurar um computador Windows para desenvolver e testar contêineres para dispositivos do Linux, você pode usar [área de trabalho do Docker](https://www.docker.com/docker-windows) como seu mecanismo de contêiner. Você precisa instalar o Docker e configurá-lo para [usar contêineres do Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) antes de instalar o IoT Edge.  

Se o dispositivo do IoT Edge for um computador Windows, verifique se atende aos [requisitos do sistema](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) para Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Instalar o IoT Edge em um novo dispositivo

>[!NOTE]
>Os pacotes de software do Azure IoT Edge estão sujeitos aos termos de licença localizados nos pacotes (no diretório LICENSE). Leia os termos de licença antes de utilizar o pacote. A instalação e o uso do pacote constitui a aceitação desses termos. Se você não concorda com os termos de licença, não utilize o pacote.

Um script do PowerShell baixa e instala o daemon de segurança do Azure IoT Edge. Em seguida, o daemon de segurança inicia o primeiro dos dois módulos de tempo de execução, o agente do IoT Edge, que permite implantações remotas de outros módulos. 

Quando você instala o tempo de execução do IoT Edge pela primeira vez em um dispositivo, é necessário provisionar o dispositivo com uma identidade de um hub IoT. Um único dispositivo IoT Edge pode ser provisionado manualmente usando uma cadeia de caracteres de conexões de dispositivo fornecida pelo seu hub IoT. Ou, você também pode usar o Serviço de Provisionamento de Dispositivos para provisionar dispositivos automaticamente, o que é útil quando há muitos dispositivos para configurar. 

Você pode ler mais sobre as opções de instalação diferente e os parâmetros no artigo [instalar o tempo de execução do Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md). Uma vez que a área de trabalho do Docker instalado e configurado para contêineres do Linux, a diferença principal de instalação está declarando Linux com o **- ContainerOs** parâmetro. Por exemplo:  

1. Se você ainda não fez isso, registre um novo dispositivo IoT Edge e recuperar a cadeia de caracteres de conexão do dispositivo. Copie a cadeia de conexão para usar posteriormente nesta seção. Você pode concluir esta etapa usando as seguintes ferramentas:

   * [Portal do Azure](how-to-register-device-portal.md)
   * [CLI do Azure](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Execute o PowerShell como administrador.

3. O **IoTEdge implantar** comando verifica seu computador Windows está em uma versão com suporte, ativa o recurso de contêineres e, em seguida, baixa o tempo de execução moby (que não é usado para contêineres do Linux) e o tempo de execução do IoT Edge. Os padrões de comando para contêineres do Windows, portanto, declare o Linux como o sistema de operacional do contêiner desejado. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. Neste ponto, dispositivos de IoT Core poderá ser reiniciado automaticamente. Outros dispositivos Windows 10 ou Windows Server podem solicitar que você reinicie. Nesse caso, reinicie seu dispositivo agora. Depois que o dispositivo está pronto, execute novamente o PowerShell como administrador.

5. O **IoTEdge Initialize** comando configura o tempo de execução do IoT Edge em seu computador. O comando assumirá como padrão o provisionamento manual com uma cadeia de caracteres de conexão do dispositivo. Declare novamente o Linux como o sistema de operacional do contêiner desejado. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Quando solicitado, forneça a cadeia de caracteres de conexão de dispositivo que você recuperou na etapa 1. A cadeia de caracteres de conexão do dispositivo associa o dispositivo físico com uma ID de dispositivo no IoT Hub. 

   A cadeia de caracteres de conexão do dispositivo recebe o seguinte formato e não deve incluir as aspas: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Verifique o status do serviço do IoT Edge. Ele deverá estar listada como em execução.  

```powershell
Get-Service iotedge
```

Examine os logs de serviço pelos últimos 5 minutos usando. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Módulos de execução da lista. Após uma nova instalação, o único módulo que você verá é a execução **edgeAgent**. Depois que você [implantar módulos do IoT Edge](how-to-deploy-modules-portal.md), você verá que outras pessoas. 

```powershell
iotedge list
```

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo IoT Edge provisionado com o tempo de execução instalado, é possível [implantar os módulos do IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com a instalação correta do IoT Edge, consulte a página de [solução de problemas](troubleshoot.md).

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o daemon de segurança e o tempo de execução do IoT Edge](how-to-update-iot-edge.md).
