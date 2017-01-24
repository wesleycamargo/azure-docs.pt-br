---
title: "Extensão da máquina virtual do OMS Azure para Linux | Microsoft Docs"
description: "Implante o agente do OMS na máquina virtual Linux usando uma extensão da máquina virtual."
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 9f27890e52cb7a9a0d46f2bb84bfe92f7c6fff37
ms.openlocfilehash: 9864314956d79317785c1c2a4bc87621bc6a3e2d


---
# <a name="oms-virtual-machine-extension-for-linux"></a>Extensão da máquina virtual do OMS para Linux

## <a name="overview"></a>Visão geral

O OMS (Operations Management Suite) fornece recursos de monitoramento, alertas e correção de alertas nos ativos locais e da nuvem. A extensão da máquina virtual do agente do OMS para Linux é publicada e recebe suporte da Microsoft. A extensão instala o agente do OMS em máquinas virtuais do Azure e registra máquinas virtuais em um espaço de trabalho OMS existente. Este documento detalha as plataformas com opções de plataformas, configurações e implantação com suporte para a extensão da máquina virtual do OMS para Linux.

Para obter informações sobre as extensões da máquina virtual do Azure, confira [Visão geral das extensões da Máquina Virtual](./virtual-machines-linux-extensions-features.md).

Para obter mais informações sobre o Operations Management Suite, confira [Visão geral do Operations Management Suite](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A extensão do agente do OMS pode ser executada nessas distribuições do Linux.

| Distribuição | Versão |
|---|---|
| CentOS Linux | 5,6 e 7 |
| Oracle Linux | 5,6 e 7 |
| Red Hat Enterprise Linux Server | 5,6 e 7 |
| Debian GNU/Linux | 6, 7 e 8 |
| Ubuntu | 12.04 LTS, 14.04 LTS, 15.04 |
| SUSE Linux Enterprise Server | 11 e 12 |

### <a name="connectivity"></a>Conectividade

A extensão do Agente do OMS para Linux requer que a máquina virtual de destino esteja conectada à Internet. 

## <a name="extension-configuration"></a>Configuração da extensão

A extensão da máquina virtual do agente do OMS para Linux requer a ID e a chave do espaço de trabalho OMS de destino. Como a chave do espaço de trabalho deve ser tratada como um dado confidencial, ele é armazenado em uma configuração protegida. Os dados de configuração protegidos da extensão da VM do Azure são criptografados, sendo descriptografados apenas na máquina virtual de destino. As configurações públicas e privadas são especificadas no momento da implantação, que é detalhada nas seções posteriores deste documento.

### <a name="public-configuration"></a>Configuração pública

Esquema de configuração pública:

- workspaceId: (obrigatório, cadeia de caracteres) a ID do espaço de trabalho do OMS para integração da máquina virtual.

```json
{
  "workspaceId": "myWorkspaceId"
}
```

### <a name="private-configuration"></a>Configuração privada

Esquema de configuração pública:

- workspaceKey: (obrigatório, cadeia de caracteres) a chave compartilhada primária/secundária do espaço de trabalho.

```json
{
  "workspaceKey": "myWorkSpaceKey"
}
```

## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. Modelos são ideais ao implantar uma ou mais máquinas virtuais que exigem configuração pós-implantação, tal como integração ao OMS. Um modelo do Resource Manager de exemplo que inclui a extensão de VM do agente do OMS pode ser encontrado na [Galeria de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

Este exemplo pode ser implantado deste documento usando esse botão:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-oms-extension-ubuntu-vm%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

O JSON usado para implantar a extensão da VM do Agente do OMS é semelhante ao seguinte exemplo de JSON:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<extension-deployment-name>",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.0",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure

A CLI do Azure pode ser usado para implantar a extensão da VM do Agente do OMS para uma máquina virtual existente. Antes de implantar a Extensão do Agente do OMS, crie um arquivo public.json e protected.json. O esquema para esses arquivos é detalhado neste documento.

```azurecli
azure vm extension set <resource-group> <vm-name> \
  OmsAgentForLinux Microsoft.EnterpriseCloud.Monitoring 1.0 \
  --public-config-path public.json  \
  --private-config-path protected.json
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Dados sobre o estado das implantações de extensão podem ser recuperados do Portal do Azure usando a CLI do Azure. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir usando a CLI do Azure.

```azurecli
azure vm extension get myResourceGroup myVM
```

A saída de execução da extensão é registrada no seguinte arquivo:

`
/opt/microsoft/omsagent/bin/stdout
`

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/en-us/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/en-us/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).



<!--HONumber=Dec16_HO1-->


