---
title: Atualização da versão do IoT Edge nos dispositivos – Azure IoT Edge | Microsoft Docs
description: Como atualizar dispositivos IoT Edge para executar as versões mais recentes do daemon de segurança e do tempo de execução do IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: b11f11aa3966bc57caa5b8dd0379f4d5c59c8375
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672892"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Atualize o daemon de segurança do IoT Edge e o tempo de execução

Conforme o serviço do IoT Edge liberações de novas versões, você desejará atualizar seus dispositivos IoT Edge para os recursos e melhorias de segurança mais recentes. Este artigo fornece informações sobre como atualizar seus dispositivos IoT Edge quando uma nova versão está disponível. 

Dois componentes de um dispositivo IoT Edge precisam ser atualizados se você deseja passar para uma versão mais recente. O primeiro é o daemon de segurança que é executado no dispositivo e inicia os módulos de tempo de execução quando o dispositivo é iniciado. Atualmente, o daemon de segurança só pode ser atualizado no próprio dispositivo. O segundo componente é o tempo de execução, composto pelos módulos de agente do IoT Edge e hub do IoT Edge. Dependendo de como você estruturar sua implantação, o tempo de execução poderá ser atualizado no dispositivo ou remotamente. 

>[!IMPORTANT]
>Se você estiver executando o Azure IoT Edge em um dispositivo Windows, não atualize para a versão 1.0.5, se um dos procedimentos a seguir aplica-se ao seu dispositivo: 
>* Você não atualizou seu dispositivo para a compilação 17763 do Windows. A versão 1.0.5 do IoT Edge não oferece suporte a compilações Windows anteriores a 17763.
>* Você executa os módulos Java ou Node.js no seu dispositivo do Windows. Ignore a versão 1.0.5 mesmo se você tiver atualizado seu dispositivo Windows para a compilação mais recente. 
>
>Para obter mais informações sobre o IoT Edge versão 1.0.5, consulte [Notas de versão 1.0.5](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5). Para obter mais informações sobre como manter suas ferramentas de desenvolvimento de atualização para a versão mais recente, consulte [o blog de desenvolvedores de IoT](https://devblogs.microsoft.com/iotdev/).


Para localizar a versão mais recente do Azure IoT Edge, confira [Versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Atualizar o daemon de segurança

O daemon de segurança do IoT Edge é um componente nativo que precisa ser atualizado usando o gerenciador de pacotes no dispositivo do IoT Edge. 

Verifique a versão do daemon de segurança em execução no seu dispositivo usando o comando `iotedge version`. 

### <a name="linux-devices"></a>Dispositivos do Linux

Em dispositivos Linux, use o apt-get ou o gerenciador de pacotes apropriado para atualizar o daemon de segurança. 

```bash
apt-get update
apt-get install libiothsm iotedge
```

### <a name="windows-devices"></a>Dispositivos Windows

Em dispositivos Windows, use o script do PowerShell para desinstalar e reinstalar o daemon de segurança. O script de instalação extrai automaticamente a versão mais recente do daemon de segurança. 

Desinstale o daemon de segurança em uma sessão do PowerShell de administrador. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-SecurityDaemon
```

Executar o comando `Uninstall-SecurityDaemon` sem parâmetros remove o daemon de segurança do seu dispositivo, juntamente com as duas imagens de contêiner de tempo de execução. O arquivo config.yaml é mantido no dispositivo, bem como os dados do mecanismo de contêiner Moby. Preservar a configuração significa que você não precisa fornecer a cadeia de caracteres de conexão ou informações de serviço de provisionamento de dispositivo para seu dispositivo novamente durante o processo de instalação. 

Reinstale o daemon de segurança, dependendo de se o dispositivo IoT Edge usa contêineres do Windows ou do Linux. Substitua a frase **\<Windows ou Linux\>** por um dos sistemas operacionais do contêiner. Use o sinalizador **-ExistingConfig** para apontar para o arquivo config.yaml existente em seu dispositivo. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -ExistingConfig -ContainerOS <Windows or Linux>
```

Se você quiser instalar uma versão específica do daemon de segurança, baixe o arquivo iotedged-windows.zip apropriado das [versões do IoT Edge](https://github.com/Azure/azure-iotedge/releases). Em seguida, use o parâmetro `-OfflineInstallationPath` para apontar para o local do arquivo. Para obter mais informações, consulte [Instalação offline](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>Atualizar os contêineres de tempo de execução

A maneira de atualizar o agente do IoT Edge e contêineres de hub do IoT Edge depende de se você pode usar marcas sem interrupção (como 1.0) ou marcas específicas (como 1.0.2) em sua implantação. 

Verifique a versão do agente do IoT Edge e de módulos de hub do IoT Edge atualmente em seu dispositivo usando o comando `iotedge logs edgeAgent` ou `iotedge logs edgeHub`. 

  ![Encontre a versão do contêiner nos logs](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Reconhecer as marcas do IoT Edge

O agente do IoT Edge e as imagens de hub do IoT Edge são marcadas com a versão do IoT Edge a que estão associados. Há duas maneiras diferentes de usar marcas com as imagens de tempo de execução: 

* **Marcas sem interrupção** – use os dois primeiros valores do número de versão para obter a imagem mais recente que corresponde a esses dígitos. Por exemplo, 1.0 é atualizado sempre que há uma nova versão para apontar para a versão 1.0.x mais recente. Se o tempo de execução do contêiner em seu dispositivo IoT Edge extrair a imagem novamente, os módulos de tempo de execução serão atualizados para a versão mais recente. Essa abordagem é sugerida para fins de desenvolvimento. Implantações do portal do Azure usam como padrão marcas sem interrupção. 
* **Marcas específicas** – use todos os três valores do número de versão para definir explicitamente a versão da imagem. Por exemplo, 1.0.2 não mudará após seu lançamento inicial. Você pode declarar um novo número de versão no manifesto de implantação quando estiver pronto para atualizar. Essa abordagem é sugerida para fins de produção.

### <a name="update-a-rolling-tag-image"></a>Atualizar uma imagem de tag sem interrupção

Se você usar marcas sem interrupção em sua implantação (por exemplo, mcr.microsoft.com/azureiotedge-hub:**1.0**), será necessário forçar o tempo de execução do contêiner em seu dispositivo para obter a versão mais recente da imagem. 

Exclua a versão local da imagem do seu dispositivo IoT Edge. Em computadores Windows, desinstalar o daemon de segurança também remove as imagens de tempo de execução, portanto, você não precisará realizar esta etapa novamente. 

```cmd/sh
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Talvez você precise usar a marca `-f` de forçar para remover as imagens. 

O serviço do IoT Edge obterá as versões mais recentes das imagens do tempo de execução e as iniciará automaticamente em seu dispositivo outra vez. 

### <a name="update-a-specific-tag-image"></a>Atualizar uma imagem de tag específica

Se você usar tags específicas em sua implantação (por exemplo, mcr.microsoft.com/azureiotedge-hub:**1.0.2**), bastará atualizar a tag em seu manifesto de implantação e aplicar as alterações ao seu dispositivo. 

No portal do Azure, as imagens de implantação de tempo de execução são declaradas na seção **Definir configurações de tempo de execução do Edge avançadas**. 

[Definir configurações avançadas de tempo de execução do Edge](./media/how-to-update-iot-edge/configure-runtime.png)

Em um manifesto de implantação de JSON, atualize as imagens de módulo na seção **systemModules**. 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.2",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.2",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="next-steps"></a>Próximas etapas

Exibir as [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) mais recentes.

Fique por dentro das atualizações e comunicados mais recentes no [blog da Internet das Coisas](https://azure.microsoft.com/blog/topics/internet-of-things/) 