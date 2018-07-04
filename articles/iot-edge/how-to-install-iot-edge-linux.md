---
title: Como instalar o Azure IoT Edge no Linux | Microsoft Docs
description: Instruções para instalação do Azure IoT Edge no Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 43f82341a3cc9d2163afd35e42864aaa7866b1b2
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034421"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Instalar o tempo de execução do Azure IoT Edge no Linux (x64)

O tempo de execução do Azure IoT Edge é implantado em todos os dispositivos do IoT Edge. Há três componentes. O **daemon de segurança do IoT Edge** fornece e mantém padrões de segurança no dispositivo do Edge. O daemon inicia em cada inicialização e inicializa o dispositivo, iniciando o agente do IoT Edge. O **agente do IoT Edge** facilita a implantação e o monitoramento de módulos no dispositivo do Edge, incluindo o hub do IoT Edge. O **hub IoT Edge** gerencia a comunicação entre os módulos no dispositivo IoT Edge e entre o dispositivo e o Hub IoT.

Este artigo lista as etapas para instalar o tempo de execução do Azure IoT Edge no dispositivo de borda do Linux x64 (Intel/AMD).

>[!NOTE]
>Os pacotes nos repositórios de software do Linux estão sujeitos aos termos de licença localizados em cada pacote (/usr/share/doc/*nome-do-pacote*). Leia os termos de licença antes de utilizar o pacote. A instalação e o uso do pacote constitui a aceitação desses termos. Se você não concorda com os termos de licença, não utilize o pacote.

## <a name="register-microsoft-key-and-software-repository-feed"></a>Registrar a chave da Microsoft e o feed do repositório de software

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="debian-9"></a>Debian 9

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/debian/9/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

## <a name="install-the-container-runtime"></a>Instalar o tempo de execução do contêiner 

O Azure IoT Edge depende de um tempo de execução de contêiner [Compatível com OCI][lnk-oci] (por exemplo, Docker). Se você já tiver o Docker CE/EE instalado no dispositivo de borda, será possível continuar utilizando-o para desenvolvimento e teste com Azure IoT Edge. 

Para cenários de produção, é altamente recomendável utilizar o mecanismo [baseado em Moby][lnk-moby] fornecido abaixo. É o único mecanismo de contêiner oficialmente com suporte do Azure IoT Edge. As imagens do contêiner do Docker CE/EE são totalmente compatíveis com o tempo de execução do Moby.

*As instruções abaixo instalam o mecanismo Moby e a CLI (interface de linha de comando). A CLI é útil para desenvolvimento, mas opcional para implantações de produção.* 

```cmd/sh
sudo apt-get update
sudo apt-get install moby-engine
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instalar o daemon de segurança do Azure IoT Edge

Os comandos abaixo também instalarão a versão padrão do **iothsmlib**, se ainda não estiver presente.

```cmd/sh
sudo apt-get update
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configurar o daemon de segurança do Azure IoT Edge

O daemon pode ser configurado usando o arquivo de configuração em `/etc/iotedge/config.yaml` O dispositivo de borda pode ser configurado <!--[automatically via Device Provisioning Service][lnk-dps] or--> manualmente usando uma [cadeia de conexão de dispositivo][lnk-dcs].

Para configuração manual, insira a cadeia de conexão do dispositivo na seção **provisionamento** do **config.yaml**

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

*Por padrão, o arquivo é protegido contra gravação e você deverá utilizar `sudo` para editá-lo. Por exemplo `sudo nano /etc/iotedge/config.yaml`*

Após inserir as informações de provisionamento na configuração, reinicie o daemon:

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verificar instalação com êxito

É possível verificar o status do Daemon do IoT Edge usando:

```cmd/sh
systemctl status iotedge
```

Examine os logs do daemon usando:

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

E liste os módulos em execução com:

```cmd/sh
iotedge list
```

## <a name="next-steps"></a>Próximas etapas

Se você estiver tendo problemas com o tempo de execução do Edge instalado corretamente, verifique a página de [solução de problemas][lnk-trouble].

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
