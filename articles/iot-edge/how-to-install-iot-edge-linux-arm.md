---
title: Como instalar o Azure IoT Edge no Linux | Microsoft Docs
description: Instruções de instalação do Azure IoT Edge no Linux no ARM32
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: ad70fcc6b9779cb33772a3fce2fb11b4cec804ee
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062591"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Instalar o tempo de execução do Azure IoT Edge no Linux (ARM32v7 / armhf)

O tempo de execução do Azure IoT Edge é implantado em todos os dispositivos IoT Edge. Tem três componentes. O daemon de segurança **IoT Edge** fornece e mantém padrões de segurança no dispositivo Edge. O daemon inicia em cada inicialização e inicializa o dispositivo iniciando o agente IoT Edge. O **agente IoT Edge** facilita a implantação e o monitoramento de módulos no dispositivo Edge, incluindo o hub IoT Edge. O **hub IoT Edge** gerencia a comunicação entre os módulos no dispositivo IoT Edge e entre o dispositivo e o Hub IoT.

Este artigo lista as etapas para instalar o tempo de execução do Azure IoT Edge em um dispositivo Linux ARM32v7 / armhf Edge (por exemplo, Raspberry Pi).

>[!NOTE]
>Os pacotes nos repositórios de software do Linux estão sujeitos aos termos de licença localizados em cada pacote (/usr/share/doc/ *package-name* ). Leia os termos da licença antes de usar o pacote. Sua instalação e uso do pacote constitui sua aceitação desses termos. Se você não concordar com os termos da licença, não use o pacote.

## <a name="install-the-container-runtime"></a>Instale o tempo de execução do contêiner

O Azure IoT Edge depende de um tempo de execução de contêiner [Compatível com OCI][lnk-oci] (por exemplo, Docker). Se você já tiver o Docker CE/EE instalado no dispositivo de borda, será possível continuar utilizando-o para desenvolvimento e teste com Azure IoT Edge. 

Para cenários de produção, é altamente recomendável utilizar o mecanismo [baseado em Moby][lnk-moby] fornecido abaixo. É o único mecanismo de contêiner oficialmente com suporte do Azure IoT Edge. Imagens de contêiner do docker CE/EE são totalmente compatíveis com o tempo de execução Moby.

Abaixo de comandos instala o mecanismo moby e interface de linha de comando (CLI). A CLI é útil para desenvolvimento, mas é opcional para implantações de produção.

```cmd/sh

# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f

```

## <a name="install-the-iot-edge-security-daemon"></a>Instalar o daemon de segurança do IoT Edge

```cmd/sh
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configurar o Daemon de segurança de borda de IoT do Azure

O daemon pode ser configurado usando o arquivo de configuração em `/etc/iotedge/config.yaml` o dispositivo de borda pode ser configurado <!--[automatically via Device Provisioning Service][lnk-dps] or--> manualmente usando um [cadeia de caracteres de conexão de dispositivo][lnk-dcs].

Para configuração manual, insira a cadeia de caracteres de conexão do dispositivo em **provisionamento** seção **config.yaml**

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

*O arquivo está protegido contra gravação por padrão, talvez seja necessário usar `sudo` para editá-lo. Por exemplo `sudo nano /etc/iotedge/config.yaml`*

Depois de inserir as informações de provisionamento na configuração, reinicie o daemon:

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Você pode verificar o status do Daemon de borda IoT usando:

```cmd/sh
systemctl status iotedge
```

Examine os logs do daemon usando:

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Além disso, lista de módulos com em execução:

```cmd/sh
iotedge list
```

## <a name="next-steps"></a>Próximas etapas

Se você estiver tendo problemas com o tempo de execução de borda instalado corretamente, check-out de [solução de problemas][lnk-trouble] página.

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md