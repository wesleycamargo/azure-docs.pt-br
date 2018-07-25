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
ms.openlocfilehash: 5b5212d5e1663fee01ff87642432818071d4f4dd
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988527"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Instalar o tempo de execução do Azure IoT Edge no Linux (ARM32v7 / armhf)

O tempo de execução do Azure IoT Edge é implantado em todos os dispositivos IoT Edge. Tem três componentes. O daemon de segurança **IoT Edge** fornece e mantém padrões de segurança no dispositivo Edge. O daemon inicia em cada inicialização e inicializa o dispositivo iniciando o agente IoT Edge. O **agente IoT Edge** facilita a implantação e o monitoramento de módulos no dispositivo Edge, incluindo o hub IoT Edge. O **hub IoT Edge** gerencia a comunicação entre os módulos no dispositivo IoT Edge e entre o dispositivo e o Hub IoT.

Este artigo lista as etapas para instalar o tempo de execução do Azure IoT Edge em um dispositivo Linux ARM32v7 / armhf Edge (por exemplo, Raspberry Pi).

>[!NOTE]
>Os pacotes nos repositórios de software do Linux estão sujeitos aos termos de licença localizados em cada pacote (/usr/share/doc/ *package-name* ). Leia os termos da licença antes de usar o pacote. Sua instalação e uso do pacote constitui sua aceitação desses termos. Se você não concordar com os termos da licença, não use o pacote.

## <a name="install-the-container-runtime"></a>Instalar o tempo de execução de contêiner

O Azure IoT Edge depende de um tempo de execução de contêiner [compatível com OCI][lnk-oci]. Para cenários de produção, é altamente recomendável utilizar o mecanismo [baseado em Moby][lnk-moby] fornecido abaixo. É o único mecanismo de contêiner oficialmente com suporte do Azure IoT Edge. Imagens de contêiner do Docker CE/EE são compatíveis com o tempo de execução baseado em Moby.

Os comandos abaixo instalam o mecanismo baseado em Moby e a CLI (interface de linha de comando). A CLI é útil para desenvolvimento, mas é opcional para implantações de produção.

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
>[!NOTE]
>Nos dispositivos com restrição de recursos como o RaspberryPi, é altamente recomendável que a variável de ambiente *OptimizeForPerformance* seja definida como *false* de acordo com as instruções no [guia de solução de problemas.][lnk-trouble]


## <a name="next-steps"></a>Próximas etapas

Se você estiver tendo problemas com o tempo de execução de borda instalado corretamente, check-out de [solução de problemas][lnk-trouble] página.

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-trouble]: https://docs.microsoft.com/azure/iot-edge/troubleshoot#stability-issues-on-resource-constrained-devices
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
