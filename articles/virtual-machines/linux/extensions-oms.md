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
ms.date: 04/26/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 05f823955eb5c47ce024c2b7d246e361e1302d78
ms.contentlocale: pt-br
ms.lasthandoff: 04/27/2017


---
# <a name="oms-virtual-machine-extension-for-linux"></a>Extensão da máquina virtual do OMS para Linux

## <a name="overview"></a>Visão geral

O OMS (Operations Management Suite) fornece recursos de monitoramento, alertas e correção de alertas nos ativos locais e da nuvem. A extensão da máquina virtual do agente do OMS para Linux é publicada e recebe suporte da Microsoft. A extensão instala o agente do OMS em máquinas virtuais do Azure e registra máquinas virtuais em um espaço de trabalho OMS existente. Este documento detalha as plataformas com opções de plataformas, configurações e implantação com suporte para a extensão da máquina virtual do OMS para Linux.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A extensão do agente do OMS pode ser executada nessas distribuições do Linux.

| Distribuição | Versão |
|---|---|
| CentOS Linux | 5, 6 e 7 |
| Oracle Linux | 5, 6 e 7 |
| Red Hat Enterprise Linux Server | 5, 6 e 7 |
| Debian GNU/Linux | 6, 7 e 8 |
| Ubuntu | 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS |
| SUSE Linux Enterprise Server | 11 e 12 |

### <a name="internet-connectivity"></a>Conectividade com a Internet

A extensão do Agente do OMS para Linux requer que a máquina virtual de destino esteja conectada à Internet. 

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão do Agente do OMS. A extensão requer a ID e a chave do espaço de trabalho do OMS de destino, que esses valores podem ser encontrados no portal do OMS. Como a chave do espaço de trabalho deve ser tratada como um dado confidencial, ela é armazenada em uma configuração protegida. Os dados de configuração protegidos pela extensão da VM do Azure são criptografados, sendo descriptografados apenas na máquina virtual de destino. Observe que **workspaceId** e **workspaceKey** diferenciam maiúsculas de minúsculas.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.3",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publicador | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1,3 |
| workspaceId (por exemplo) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (por exemplo) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. Modelos são ideais ao implantar uma ou mais máquinas virtuais que exigem configuração pós-implantação, tal como integração ao OMS. Um modelo do Resource Manager de exemplo que inclui a extensão de VM do agente do OMS pode ser encontrado na [Galeria de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

O JSON para uma extensão da máquina virtual pode ser aninhado dentro do recurso de máquina virtual ou localizado no nível de raiz ou superior de um modelo JSON do Resource Manager. O posicionamento do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte [Definir o nome e o tipo de recursos filho](../../azure-resource-manager/resource-manager-template-child-resource.md). 

O exemplo a seguir pressupõe que a extensão OMS esteja aninhada dentro do recurso de máquina virtual. Ao aninhar o recurso de extensão, o JSON é colocado no objeto `"resources": []` da máquina virtual.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.3",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Ao inserir o JSON da extensão na raiz do modelo, o nome do recurso inclui uma referência na máquina virtual pai e o tipo reflete a configuração aninhada.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.3",
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

A CLI do Azure pode ser usado para implantar a extensão da VM do Agente do OMS para uma máquina virtual existente. Substitua a chave e a ID do OMS por aqueles de seu espaço de trabalho do OMS. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.0 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Dados sobre o estado das implantações de extensão podem ser recuperados do Portal do Azure usando a CLI do Azure. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir usando a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída de execução da extensão é registrada no seguinte arquivo:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e seus significados

| Código do Erro | Significado | Ação possível |
| :---: | --- | --- |
| 2 | Opção inválida fornecida para o pacote de shell | |
| 3 | Nenhuma opção fornecida para o pacote de shell | |
| 4 | Tipo de pacote inválido | |
| 5 | O pacote de shell deve ser executado como raiz | |
| 6 | Arquitetura de pacote inválida | |
| 10 | A VM já está conectada a um espaço de trabalho do OMS | Para conectar a VM ao espaço de trabalho especificado no esquema de extensão, defina stopOnMultipleConnections como falso nas configurações públicas ou remova esta propriedade. Essa VM é cobrada uma vez para cada espaço de trabalho ao qual está conectada. |
| 11 | Configuração inválida fornecida para a extensão | Siga os exemplos anteriores para definir todos os valores de propriedade necessários para a implantação. |
| 20 | Falha na instalação do SCX/OMI | |
| 21 | Falha na instalação dos kits SCX/Provedor | |
| 22 | Falha na instalação do pacote único | |
| 23 | Pacote SCX ou OMI já instalado | |
| 30 | Erro interno do pacote | |
| 51 | Não há suporte para essa extensão no sistema operacional da VM | |
| 60 | Versão sem suporte do OpenSSL | Instale uma versão do OpenSSL que atenda aos [requisitos de pacote](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#package-requirements). |
| 61 | Biblioteca de ctypes do Python ausente | Instale a biblioteca ou pacote ctypes do Python (python-ctypes). |
| 62 | Programa tar ausente | Instale o tar. |
| 63 | Programa sed ausente | Instale o sed. |

Informações adicionais podem ser encontradas no [Guia de solução de problemas do OMS-Agent-for-Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#).

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/en-us/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/en-us/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).

