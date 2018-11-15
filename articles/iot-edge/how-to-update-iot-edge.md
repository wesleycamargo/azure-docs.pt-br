---
title: Atualizar dispositivos para a versão mais recente do Azure IoT Edge | Microsoft Docs
description: Como atualizar dispositivos IoT Edge para executar as versões mais recentes do daemon de segurança e do tempo de execução do IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1522d9c9bc4fda178d8571fb57cb9c94ed1044ae
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567784"
---
# <a name="update-the-iot-edge-runtime"></a>Atualizar o tempo de execução do IoT Edge

Conforme o serviço do IoT Edge liberações de novas versões, você desejará atualizar seus dispositivos IoT Edge para os recursos e melhorias de segurança mais recentes. Este artigo fornece informações sobre como atualizar seus dispositivos IoT Edge quando uma nova versão está disponível. 

Dois componentes de um dispositivo IoT Edge precisam ser atualizados se você deseja passar para uma versão mais recente. O primeiro é o daemon de segurança que é executado no dispositivo e inicia o tempo de execução quando o dispositivo é iniciado. Atualmente, o daemon de segurança só pode ser atualizado no próprio dispositivo. O segundo componente é o tempo de execução, composto pelos módulos de agente do Edge e hub do Edge. Dependendo de como você estruturar sua implantação, o tempo de execução poderá ser atualizado no dispositivo ou remotamente. 

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

Em dispositivos Windows, use o script do PowerShell para desinstalar e reinstalar o daemon de segurança. O script de instalação extrai automaticamente a versão mais recente do daemon de segurança. Você precisa fornecer a cadeia de conexão para seu dispositivo novamente durante o processo de instalação. 

Desinstale o daemon de segurança em uma sessão do PowerShell de administrador. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
UnInstall-SecurityDaemon
```

Reinstale o daemon de segurança, dependendo de se o dispositivo IoT Edge usa contêineres do Windows ou do Linux. Substitua a frase **\<Windows ou Linux\>** por um dos sistemas operacionais do contêiner. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOS <Windows or Linux>
```

## <a name="update-the-runtime-containers"></a>Atualizar os contêineres de tempo de execução

A maneira de atualizar o agente do Edge e contêineres de hub do Edge depende de se você pode usar marcas sem interrupção (como 1.0) ou marcas específicas (como 1.0.2) em sua implantação. 

Verifique a versão do agente do IoT Edge e de módulos de hub do Edge atualmente em seu dispositivo usando o comando `iotedge logs edgeAgent` ou `iotedge logs edgeHub`. 

  ![Exibir a versão de contêiner](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Reconhecer as marcas do IoT Edge

O agente do Edge e as imagens de hub do Edge são marcadas com a versão do IoT Edge a que estão associados. Há duas maneiras diferentes de usar marcas com as imagens de tempo de execução: 

* **Marcas sem interrupção** – use os dois primeiros valores do número de versão para obter a imagem mais recente que corresponde a esses dígitos. Por exemplo, 1.0 é atualizado sempre que há uma nova versão para apontar para a versão 1.0.x mais recente. Se o tempo de execução do contêiner em seu dispositivo IoT Edge extrair a imagem novamente, os módulos de tempo de execução serão atualizados para a versão mais recente. Essa abordagem é sugerida para fins de desenvolvimento. Implantações do portal do Azure usam como padrão marcas sem interrupção. 
* **Marcas específicas** – use todos os três valores do número de versão para definir explicitamente a versão da imagem. Por exemplo, 1.0.2 não mudará após seu lançamento inicial. Você pode declarar um novo número de versão no manifesto de implantação quando estiver pronto para atualizar. Essa abordagem é sugerida para fins de produção.

### <a name="update-a-rolling-tag-image"></a>Atualizar uma imagem de tag sem interrupção

Se você usar marcas sem interrupção em sua implantação (por exemplo, mcr.microsoft.com/azureiotedge-hub:**1.0**), será necessário forçar o tempo de execução do contêiner em seu dispositivo para obter a versão mais recente da imagem. 

Exclua a versão local da imagem do seu dispositivo IoT Edge. 

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