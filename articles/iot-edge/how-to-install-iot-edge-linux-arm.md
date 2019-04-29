---
title: Instalar o Azure IoT Edge no Linux ARM32 | Microsoft Docs
description: Instruções de instalação do Azure IoT Edge no Linux em dispositivos ARM32, como um Raspberry PI
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kgremban
ms.openlocfilehash: ad7e34110b0c6d047eb7454b7fac9f8c10df8be2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126353"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Instalar o tempo de execução do Azure IoT Edge no Linux (ARM32v7 / armhf)

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O tempo de execução pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o tempo de execução do IoT Edge, você poderá começar a implantar a lógica de negócios da nuvem nele. 

Para saber mais sobre como funciona o tempo de execução do IoT Edge e quais componentes estão incluídos, veja [Entender o tempo de execução do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para instalar o tempo de execução do Azure IoT Edge em um dispositivo Linux ARM32v7/armhf IoT Edge. Essas etapas funcionariam, por exemplo, para dispositivos Raspberry Pi. Para obter uma lista dos sistemas de operacionais ARM32 suportados, consulte [suporte do Azure IoT Edge](support.md#operating-systems). 

>[!NOTE]
>Os pacotes nos repositórios de software do Linux estão sujeitos aos termos de licença localizados em cada pacote (/usr/share/doc/ *package-name* ). Leia os termos da licença antes de usar o pacote. Sua instalação e uso do pacote constitui sua aceitação desses termos. Se você não concordar com os termos da licença, não use o pacote.

## <a name="install-the-container-runtime"></a>Instalar o tempo de execução de contêiner

O Azure IoT Edge depende de um tempo de execução de contêiner [compatível com OCI](https://www.opencontainers.org/). Para cenários de produção, é altamente recomendável usar o mecanismo [baseado em Moby](https://mobyproject.org/) fornecido abaixo. É o único mecanismo de contêiner oficialmente com suporte do Azure IoT Edge. Imagens de contêiner do Docker CE/EE são compatíveis com o tempo de execução baseado em Moby.

Os comandos a seguir instalam o mecanismo baseado em Moby e a interface de linha de comando (CLI). A CLI é útil para desenvolvimento, mas é opcional para implantações de produção.

```bash
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

O **daemon de segurança do IoT Edge** fornece e mantém os padrões de segurança no dispositivo do IoT Edge. O daemon é iniciado a cada inicialização e inicializa o dispositivo iniciando o restante do tempo de execução do IoT Edge. 


```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

## <a name="connect-your-device-to-an-iot-hub"></a>Conectar seu dispositivo a um Hub IoT 

Configure o tempo de execução do IoT Edge para vincular seu dispositivo físico com uma identidade do dispositivo existente em um Hub IoT do Azure. 

O daemon pode ser configurado usando o arquivo de configuração no `/etc/iotedge/config.yaml`. O arquivo está protegido contra gravação por padrão, portanto, você talvez precise de permissões com privilégios elevados para editá-lo.

Um único dispositivo Azure IoT Edge pode ser provisionado manualmente usando uma cadeia de caracteres de conexões de dispositivo fornecida pelo Hub IoT. Ou, você pode usar o serviço de provisionamento de dispositivo para provisionar automaticamente os dispositivos, que é útil quando você tem muitos dispositivos para provisionar. Dependendo de sua escolha de provisionamento, escolha o script de instalação apropriado. 

### <a name="option-1-manual-provisioning"></a>Opção 1: provisionamento manual

Para configurar manualmente um dispositivo, você precisará fornecê-la com um [cadeia de conexão do dispositivo](how-to-register-device-portal.md) que você pode criar, registrando um novo dispositivo IoT Edge em seu hub IoT.

Abra o arquivo de configuração. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Localize a seção de provisionamento do arquivo e remova a marca de comentário do modo de provisionamento **manual**. Atualizar o valor de **device_connection_string** com a cadeia de caracteres de conexão do dispositivo IoT Edge.

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

Salve e feche o arquivo. 

`CTRL + X`, `Y`, `Enter`

Depois de inserir as informações de provisionamento no arquivo de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opção 2: provisionamento automático

Para provisionar um dispositivo automaticamente, [configure o Serviço de provisionamento de dispositivos e recupere a ID de registro do dispositivo](how-to-auto-provision-simulated-device-linux.md). O provisionamento automático funciona apenas com dispositivos que têm um chip TPM (Trusted Platform Module). Por exemplo, dispositivos Raspberry Pi não são fornecidos com o TPM por padrão. 

Abra o arquivo de configuração. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Localize a seção de provisionamento do arquivo e remova a marca de comentário do modo de provisionamento **dps**. Atualize os valores de **scope_id** e **registration_id** com os valores de seu Serviço de Provisionamento de Dispositivos no Hub IoT e o dispositivo do IoT Edge com o TPM. 

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

Salve e feche o arquivo. 

`CTRL + X`, `Y`, `Enter`

Depois de inserir as informações de provisionamento no arquivo de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Se você tiver usado as etapas de **configuração manual** na seção anterior, o tempo de execução do IoT Edge deve ser provisionado e estar em execução no seu dispositivo com êxito. Ou, se você tiver usado o **a configuração automática** etapas, você precisará concluir algumas etapas adicionais para que o tempo de execução pode registrar seu dispositivo no hub IoT em seu nome. Para as próximas etapas, consulte [criar e provisionar um dispositivo simulado do IoT Edge do TPM em uma máquina virtual Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Você pode verificar o status do Daemon de borda IoT usando:

```bash
systemctl status iotedge
```

Examine os logs do daemon usando:

```bash
journalctl -u iotedge --no-pager --no-full
```

Além disso, lista de módulos com em execução:

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>Dicas e sugestões

Você precisa de privilégios elevados para executar comandos `iotedge`. Após instalar o tempo de execução, saia da sua máquina virtual e entre novamente para atualizar suas permissões automaticamente. Até lá, use **sudo** na frente de qualquer um dos comandos `iotedge`.

Em dispositivos com restrição de recursos, é altamente recomendável que a variável de ambiente *OptimizeForPerformance* seja definida como *false* de acordo com as instruções no [guia de solução de problemas](troubleshoot.md#stability-issues-on-resource-constrained-devices).

Se a rede tem um servidor proxy, siga as etapas em [Configurar o dispositivo do IoT Edge para comunicar-se por meio de um servidor proxy](how-to-configure-proxy-support.md).

## <a name="uninstall-iot-edge"></a>Desinstalar o IoT Edge

Para remover a instalação do IoT Edge do dispositivo Linux, use o seguinte comando em uma linha de comando. 

Remova o tempo de execução do IoT Edge. 

```bash
sudo apt-get remove --purge iotedge
```

Quando o tempo de execução do IoT Edge for removido, o contêiner criado por ele será interrompido, mas ainda existará no seu dispositivo. Exiba todos os contêineres para ver quais deles permanecem. 

```bash
sudo docker ps -a
```

Exclua os contêineres do seu dispositivo, incluindo dois contêineres de tempo de execução. 

```bash
sudo docker rm -f <container name>
```

Por fim, remova o tempo de execução do contêiner do seu dispositivo. 

```bash 
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo IoT Edge provisionado com o tempo de execução instalado, é possível [implantar os módulos do IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com o tempo de execução do IoT Edge instalado corretamente, consulte a [solução de problemas](troubleshoot.md#stability-issues-on-resource-constrained-devices) página.

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o IoT Edge de segurança e o tempo de execução do IoT Edge](how-to-update-iot-edge.md).
