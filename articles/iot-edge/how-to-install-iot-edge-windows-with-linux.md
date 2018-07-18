---
title: Como instalar o Azure IoT Edge no Windows com contêineres do Linux | Microsoft Docs
description: Instruções para instalação do Azure IoT Edge no Windows com contêineres do Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: cd517d7e652b38c7ecf28a17657936698416413a
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034178"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Instalar o tempo de execução do Azure IoT Edge no Windows para usar com contêineres do Linux

O tempo de execução do Azure IoT Edge é implantado em todos os dispositivos do IoT Edge. Há três componentes. O **daemon de segurança do IoT Edge** fornece e mantém padrões de segurança no dispositivo do Edge. O daemon inicia em cada inicialização e inicializa o dispositivo, iniciando o agente do IoT Edge. O **agente do IoT Edge** facilita a implantação e o monitoramento de módulos no dispositivo do Edge, incluindo o hub do IoT Edge. O **hub IoT Edge** gerencia a comunicação entre os módulos no dispositivo IoT Edge e entre o dispositivo e o Hub IoT.

Este artigo lista as etapas para instalar o tempo de execução do Azure IoT Edge no sistema Windows x64 (AMD/Intel). Atualmente, o suporte do Windows está em versão prévia.

>[!NOTE]
O uso de contêineres do Linux no sistema Windows não é uma configuração de produção com suporte ou recomendada para Azure IoT Edge. No entanto, pode ser utilizado para fins de desenvolvimento e teste.

## <a name="supported-windows-versions"></a>Versões do Windows com suporte
O Azure IoT Edge pode ser usado para desenvolvimento e teste nas versões a seguir do Windows, ao usar contêineres do Linux:
  * Windows 10 ou sistemas operacionais de desktop mais recentes.
  * Windows Server 2016 ou sistemas operacionais de servidor mais recentes.

## <a name="install-the-container-runtime"></a>Instalar o tempo de execução de contêiner 

O Azure IoT Edge depende de um tempo de execução de contêiner [Compatível com OCI][lnk-oci] (por exemplo, Docker). 

É possível usar o [Docker for Windows][lnk-docker-for-windows] para desenvolvimento e teste. Assegure-se de que o Docker for Windows está [configurado para usar contêineres do Linux][lnk-docker-config]

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instalar o daemon de segurança do Azure IoT Edge

>[!NOTE]
>Os pacotes de software do Azure IoT Edge estão sujeitos aos termos de licença localizados nos pacotes (no diretório LICENSE). Leia os termos de licença antes de utilizar o pacote. A instalação e o uso do pacote constitui a aceitação desses termos. Se você não concorda com os termos de licença, não utilize o pacote.

### <a name="download-the-edge-daemon-package-and-install"></a>Baixar o pacote do daemon de borda e instalar

Em uma janela do PowerShell do administrador, execute os comandos a seguir:

```powershell
Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
rmdir C:\ProgramData\iotedge\iotedged-windows
$env:Path += ";C:\ProgramData\iotedge"
SETX /M PATH "$env:Path"
```

Instale o vcruntime usando:

```powershell
Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
.\vc_redist.exe /quiet /norestart
 ```

Crie e inicie o serviço *iotedge*:

```powershell
New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
Start-Service iotedge
```

Adicione exceções do Firewall para as portas usadas pelo serviço:

```powershell
New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
```

Crie um arquivo **iotedge.reg** com o conteúdo a seguir e importe para o Registro do Windows, clicando duas vezes nele ou usando o comando`reg import iotedge.reg`:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configurar o daemon de segurança do Azure IoT Edge

O daemon pode ser configurado usando o arquivo de configuração em `C:\ProgramData\iotedge\config.yaml` O dispositivo de borda pode ser configurado <!--[automatically via Device Provisioning Service][lnk-dps] or--> manualmente usando uma [cadeia de conexão de dispositivo][lnk-dcs].

Para configuração manual, insira a cadeia de conexão do dispositivo na seção **provisionamento** do **config.yaml**

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

Obtenha o nome do dispositivo de borda usando o comando `hostname` no PowerShell e configure-o como o valor para **hostname:** no yaml de configuração. Por exemplo: 

```yaml
  ###############################################################################
  # Edge device hostname
  ###############################################################################
  #
  # Configures the environment variable 'IOTEDGE_GATEWAYHOSTNAME' injected into
  # modules.
  #
  ###############################################################################

  hostname: "edgedevice-1"
```

Em seguida, será necessário fornecer o endereço IP e a porta para **workload_uri** e **management_uri** na seção **connect:** da configuração.

Para o endereço IP, insira `ipconfig` na janela do PowerShell e selecione o endereço IP da interface **vEthernet (DockerNAT)** conforme mostrado no exemplo abaixo (o endereço IP do sistema pode ser diferente):

![DockerNat][img-docker-nat]

```yaml
connect:
  management_uri: "http://10.0.75.1:15580"
  workload_uri: "http://10.0.75.1:15581"
```

Insira os mesmos endereços na seção **listen:** da configuração. Por exemplo: 

```yaml
listen:
  management_uri: "http://10.0.75.1:15580"
  workload_uri: "http://10.0.75.1:15581"
```

Na janela do PowerShell, crie uma variável de ambiente **IOTEDGE_HOST** com o endereço **management_uri**, por exemplo:

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://10.0.75.1:15580")
```

Por fim, assegure-se de que a configuração **network:** em **moby_runtime:** está sem marca de comentário e definida para **azure-iot-edge**

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "azure-iot-edge"
```

Salve o arquivo de configuração e reinicie o serviço:

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>Verificar instalação com êxito

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
  sort-object @{Expression="TimeCreated";Descending=$false}
```

E, liste os módulos em execução com:

```powershell
iotedge list
```

## <a name="next-steps"></a>Próximas etapas

Se você estiver tendo problemas com o tempo de execução do Edge instalado corretamente, verifique a página de [solução de problemas][lnk-trouble].


<!-- Images -->
[img-docker-nat]: ./media/how-to-install-iot-edge-windows-with-linux/dockernat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows

