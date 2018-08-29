---
title: Como instalar o Azure IoT Edge no Linux | Microsoft Docs
description: Instruções para instalação do Azure IoT Edge no Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/14/2018
ms.author: kgremban
ms.openlocfilehash: 5cd12d4fab97f295cad1e0ea06112fc53e376b12
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139984"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Instalar o tempo de execução do Azure IoT Edge no Linux (x64)

O tempo de execução do Azure IoT Edge é implantado em todos os dispositivos do IoT Edge. Tem três componentes. O daemon de segurança **IoT Edge** fornece e mantém padrões de segurança no dispositivo Edge. O daemon inicia em cada inicialização e inicializa o dispositivo iniciando o agente IoT Edge. O **agente IoT Edge** facilita a implantação e o monitoramento de módulos no dispositivo Edge, incluindo o hub IoT Edge. O **hub IoT Edge** gerencia a comunicação entre os módulos no dispositivo IoT Edge e entre o dispositivo e o Hub IoT.

Este artigo lista as etapas para instalar o tempo de execução do Azure IoT Edge no dispositivo de borda do Linux x64 (Intel/AMD).

>[!NOTE]
>Os pacotes nos repositórios de software do Linux estão sujeitos aos termos de licença localizados em cada pacote (/usr/share/doc/*nome-do-pacote*). Leia os termos da licença antes de usar o pacote. Sua instalação e uso do pacote constitui sua aceitação desses termos. Se você não concorda com os termos de licença, não utilize o pacote.

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

# Perform apt upgrade
sudo apt-get upgrade
```

## <a name="install-the-container-runtime"></a>Instalar o tempo de execução de contêiner 

O Azure IoT Edge depende de um tempo de execução de contêiner [compatível com OCI][lnk-oci]. Para cenários de produção, é altamente recomendável utilizar o mecanismo [baseado em Moby][lnk-moby] fornecido abaixo. É o único mecanismo de contêiner oficialmente com suporte do Azure IoT Edge. Imagens de contêiner do docker CE/EE são compatíveis com o tempo de execução Moby.

Atualize o apt-get.

```bash
sudo apt-get update
```

Instale o mecanismo de Moby. 

```bash
sudo apt-get install moby-engine
```

Instale a interface de linha de comando Moby (CLI). A CLI é útil para desenvolvimento, mas é opcional para implantações de produção.

```bash
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instalar o daemon de segurança do Azure IoT Edge

Os comandos abaixo também instalarão a versão padrão do **iothsmlib**, se ainda não estiver presente.

```bash
sudo apt-get update
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configurar o Daemon de segurança de borda de IoT do Azure

O daemon pode ser configurado usando o arquivo de configuração no `/etc/iotedge/config.yaml`. O arquivo está protegido contra gravação por padrão, permissões elevadas talvez sejam necessárias para editá-lo.

```bash
sudo nano /etc/iotedge/config.yaml
```

O dispositivo de borda pode ser configurado manualmente usando uma [cadeia de conexão do dispositivo][lnk-dcs] ou [automaticamente por meio do Serviço de Provisionamento de Dispositivo][lnk-dps].

* Para configuração manual, remova o modo de provisionamento **manual**. Atualizar o valor de **device_connection_string** com a cadeia de caracteres de conexão do dispositivo IoT Edge.

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning: 
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

* Para configuração automática, remova o modo de provisionamento **dps**. Atualize os valores de **scope_id** e **registration_id** com os valores da sua instância de DPS do Hub IoT e o dispositivo do IoT Edge com o TPM. 

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning: 
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

Depois de inserir as informações de provisionamento na configuração, reinicie o daemon:

```cmd/sh
sudo systemctl restart iotedge
```

>[!TIP]
>Você precisa de privilégios elevados para executar os comandos `iotedge`. Depois que você sair da sua máquina e fizer login novamente na primeira vez após instalar o tempo de execução do IoT Edge, suas permissões serão atualizadas automaticamente. Até lá, use **sudo** na frente dos comandos. 

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Se você tiver usado as etapas de **configuração manual** na seção anterior, o tempo de execução do IoT Edge deve ser provisionado e em execução no seu dispositivo com êxito. Se tiver usado as etapas de **configuração automática**, você precisará concluir algumas etapas adicionais para que o tempo de execução possa registrar seu dispositivo no hub IoT em seu nome. Para as próximas etapas, consulte [Criar e provisionar um dispositivo de borda do TPM simulado em uma máquina virtual Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

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
sudo iotedge list
```

## <a name="next-steps"></a>Próximas etapas

Se você estiver tendo problemas com o tempo de execução do Edge instalado corretamente, confira a página de [solução de problemas][lnk-trouble].

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
