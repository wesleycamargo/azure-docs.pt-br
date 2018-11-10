---
title: Como instalar o Azure IoT Edge no Windows com contêineres do Windows | Microsoft Docs
description: Instruções para instalação do Azure IoT Edge no Windows com contêineres do Windows
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: d85355b50bad9f05acc7da92d763d011e6f807b6
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741072"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-windows-containers"></a>Instalar o tempo de execução do Azure IoT Edge no Windows para usar com contêineres do Windows

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O tempo de execução pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o tempo de execução do IoT Edge, você poderá começar a implantar a lógica de negócios da nuvem nele. 

Para saber mais sobre como funciona o tempo de execução do IoT Edge e quais componentes estão incluídos, veja [Entender o tempo de execução do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para instalar o tempo de execução do Azure IoT Edge com contêineres do Windows no sistema Windows x64 (AMD/Intel). 

Atualmente, o suporte do Windows está em versão prévia.

## <a name="supported-windows-versions"></a>Versões do Windows com suporte
O Azure IoT Edge com contêineres do Windows pode ser usado com:
  * Windows 10/IoT Enterprise/IoT Core com atualização de abril de 2018 (compilação 17134).
  * Windows Server 1803

Para obter mais informações sobre quais sistemas operacionais têm suporte atualmente, veja o [suporte do Azure IoT Edge](support.md#operating-systems).

## <a name="install-the-container-runtime"></a>Instalar o tempo de execução de contêiner 

>[!NOTE]
>Para a instalação do mecanismo de contêiner no Windows IoT Core, siga as etapas de [provisionar um artigo de dispositivo do IoT Core](how-to-install-iot-core.md) e continue com as instruções abaixo.

O Azure IoT Edge depende de um tempo de execução do contêiner [compatível com OCI](https://www.opencontainers.org/) (por exemplo, Docker). Você pode usar o [Docker for Windows](https://www.docker.com/docker-windows) para desenvolvimento e teste. 

Configurar o Docker para Windows [usar contêineres do Windows](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instalar o daemon de segurança do Azure IoT Edge

>[!NOTE]
>Os pacotes de software do Azure IoT Edge estão sujeitos aos termos de licença localizados nos pacotes (no diretório LICENSE). Leia os termos de licença antes de utilizar o pacote. A instalação e o uso do pacote constitui a aceitação desses termos. Se você não concorda com os termos de licença, não utilize o pacote.

Um único dispositivo Azure IoT Edge pode ser provisionado manualmente usando uma cadeia de caracteres de conexões de dispositivo fornecida pelo Hub IoT. Ou, você pode usar o serviço de provisionamento de dispositivo para provisionar automaticamente os dispositivos, que é útil quando você tem muitos dispositivos para provisionar. Dependendo de sua escolha de provisionamento, escolha o script de instalação apropriado. 

### <a name="install-and-manually-provision"></a>Instalar e provisionar manualmente

1. Siga as etapas em [Registrar um novo dispositivo Azure IoT Edge](how-to-register-device-portal.md) para registrar seu dispositivo e recuperar a cadeia de caracteres de conexão do dispositivo. 

2. No seu dispositivo IoT Edge, execute o PowerShell como administrador. 

3. Baixe e instale o tempo de execução do IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Windows
   ```

4. Quando solicitado para uma **DeviceConnectionString**, forneça a cadeia de caracteres que você recuperou do Hub IoT. Não inclua aspas na cadeia de conexão. 

### <a name="install-and-automatically-provision"></a>Instalar e provisionar automaticamente

1. Siga as etapas em [Crie e provisione um dispositivo TPM Edge simulado no Windows](how-to-auto-provision-simulated-device-windows.md) para configurar o Serviço de Provisionamento de Dispositivo e recuperar seu **ID do escopo**, simular um dispositivo TPM e recuperar seu **Registro ID** e crie uma inscrição individual. Depois que o dispositivo é registrado no Hub IoT, continue com a instalação.  

   >[!TIP]
   >Mantenha a janela que está executando o simulador do TPM aberta durante a instalação e teste. 

2. No seu dispositivo IoT Edge, execute o PowerShell como administrador. 

3. Baixe e instale o tempo de execução do IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Windows
   ```

4. Quando solicitado, forneça a **ScopeID** e **RegistrationID** para seu serviço de provisionamento e o dispositivo. 

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

É possível verificar o status do serviço do IoT Edge por: 

```powershell
Get-Service iotedge
```

Examine os logs de serviço pelos últimos 5 minutos usando:

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

Além disso, lista de módulos com em execução:

```powershell
iotedge list
```

## <a name="tips-and-suggestions"></a>Dicas e sugestões

Se a rede tiver um servidor proxy, siga as etapas em [Configurar o dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md) para instalar e iniciar o tempo de execução do IoT Edge.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo IoT Edge provisionado com o tempo de execução instalado, é possível [implantar os módulos do IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com o tempo de execução do Edge instalado corretamente, confira a página de [solução de problemas](troubleshoot.md).
