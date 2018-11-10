---
title: Como instalar o Azure IoT Edge no Windows com contêineres do Linux | Microsoft Docs
description: Instruções para instalação do Azure IoT Edge no Windows com contêineres do Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: c389f904fb3ea21c9bcc538a62c7a866ef3b254d
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739253"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Instalar o tempo de execução do Azure IoT Edge no Windows para usá-lo com contêineres do Linux

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O tempo de execução pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o tempo de execução do IoT Edge, você poderá começar a implantar a lógica de negócios da nuvem nele. 

Para saber mais sobre como funciona o tempo de execução do IoT Edge e quais componentes estão incluídos, veja [Entender o tempo de execução do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para instalar o tempo de execução do Azure IoT Edge com contêineres do Linux no sistema Windows x64 (AMD/Intel). Atualmente, o suporte do Windows está em versão prévia.

>[!NOTE]
O uso de contêineres do Linux no sistema Windows não é uma configuração de produção com suporte ou recomendada para Azure IoT Edge. No entanto, pode ser utilizado para fins de desenvolvimento e teste.

## <a name="supported-windows-versions"></a>Versões do Windows com suporte
O Azure IoT Edge pode ser usado para desenvolvimento e teste nas versões a seguir do Windows, ao usar contêineres do Linux:
  * Windows 10 ou sistemas operacionais de desktop mais recentes.
  * Windows Server 2016 ou sistemas operacionais de servidor mais recentes.

Para obter mais informações sobre quais sistemas operacionais têm suporte atualmente, veja o [suporte do Azure IoT Edge](support.md#operating-systems). 

## <a name="install-the-container-runtime"></a>Instalar o tempo de execução de contêiner 

O Azure IoT Edge depende de um tempo de execução de contêiner [Compatível com OCI](https://www.opencontainers.org/) (por exemplo, Docker). 

É possível usar o [Docker for Windows](https://www.docker.com/docker-windows) para desenvolvimento e teste. Configurar o Docker CE for Windows [para usar os contêineres do Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instalar o daemon de segurança do Azure IoT Edge

>[!NOTE]
>Os pacotes de software do Azure IoT Edge estão sujeitos aos termos de licença localizados nos pacotes (no diretório LICENSE). Leia os termos de licença antes de utilizar o pacote. A instalação e o uso do pacote constitui a aceitação desses termos. Se você não concorda com os termos de licença, não utilize o pacote.

Um único dispositivo Azure IoT Edge pode ser provisionado manualmente usando uma cadeia de caracteres de conexões de dispositivo fornecida pelo Hub IoT. Ou, você pode usar o serviço de provisionamento de dispositivo para provisionar automaticamente os dispositivos, que é útil quando você tem muitos dispositivos para provisionar. Dependendo de sua escolha de provisionamento, escolha o script de instalação apropriado. 

### <a name="option-1-install-and-manually-provision"></a>Opção 1: instalar e provisionar manualmente

1. Siga as etapas em [Registrar um novo dispositivo Azure IoT Edge](how-to-register-device-portal.md) para registrar seu dispositivo e recuperar a cadeia de caracteres de conexão do dispositivo. 

2. No seu dispositivo IoT Edge, execute o PowerShell como administrador. 

3. Baixe e instale o tempo de execução do IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Linux
   ```

4. Quando solicitado para uma **DeviceConnectionString**, forneça a cadeia de caracteres que você recuperou do Hub IoT. Não inclua aspas na cadeia de conexão. 

### <a name="option-2-install-and-automatically-provision"></a>Opção 2: instalar e provisionar automaticamente

1. Siga as etapas em [Criar e provisionar um dispositivo do Azure Stream Analytics no IoT Edge TPM simulado no Windows](how-to-auto-provision-simulated-device-windows.md)para configurar o serviço de provisionamento de dispositivos e recuperar seu**ID do escopo**, simular um TPM dispositivo e como recuperar seu **ID do registro**, em seguida, crie um registro individual. Depois que o dispositivo é registrado no Hub IoT, continue com a instalação.  

   >[!TIP]
   >Mantenha a janela que está executando o simulador do TPM aberta durante a instalação e teste. 

2. No seu dispositivo IoT Edge, execute o PowerShell como administrador. 

3. Baixe e instale o tempo de execução do IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Linux
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

Agora que você tem um dispositivo IoT Edge provisionado com o tempo de execução instalado, você pode [implantar módulos do IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com o tempo de execução do Edge instalado corretamente, confira a página de [solução de problemas](troubleshoot.md).
