---
title: Extensão da máquina virtual do Log Analytics do Azure para Windows | Microsoft Docs
description: Implante o agente do Log Analytics na máquina virtual do Windows usando uma extensão da máquina virtual.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: roiyz
ms.openlocfilehash: 6a128f8fbfd39c364d63ff03a156788e44f37119
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981277"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Extensão da máquina virtual do Log Analytics para Windows

O Log Analytics fornece recursos de monitoramento nos ativos locais e da nuvem. A extensão de máquina virtual do agente Log Analytics para Windows é publicada e suportada pela Microsoft. A extensão instala o agente do Log Analytics em máquinas virtuais do Azure e registra máquinas virtuais em um workspace do Log Analytics existente. Este documento detalha as plataformas com opções de plataformas, configurações e implantação com suporte para a extensão da máquina virtual do Log Analytics para Windows.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A extensão do agente do Log Analytics para Windows pode ser executada nas versões do Windows Server 2008 R2, 2012, 2012 R2 e 2016.

### <a name="azure-security-center"></a>Central de Segurança do Azure

A Central de Segurança do Azure provisiona o agente do Log Analytics e conecta-se com o workspace do Log Analytics padrão da assinatura do Azure automaticamente. Se você estiver usando a Central de Segurança do Azure, não execute as etapas neste documento. Isso substituiria o workspace configurado e interromperia a conexão com a Central de Segurança do Azure.

### <a name="internet-connectivity"></a>Conectividade com a Internet
A extensão do agente Log Analytics para Windows requer que a máquina virtual de destino esteja conectada à Internet. 

## <a name="extension-schema"></a>Esquema de extensão

O seguinte JSON mostra o esquema para a extensão do agente do Log Analytics. A extensão requer a ID do workspace e a chave do workspace do workspace de destino do Log Analytics. Esses podem ser encontrado nas configurações para o workspace no portal do Azure. Como a chave do workspace deve ser tratada como um dado confidencial, ela é armazenada em uma configuração protegida. Os dados de configuração protegidos pela extensão da VM do Azure são criptografados, sendo descriptografados apenas na máquina virtual de destino. Observe que **workspaceId** e **workspaceKey** diferenciam maiúsculas de minúsculas.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Valores de propriedade

| NOME | Valor/Exemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publicador | Microsoft.EnterpriseCloud.Monitoring |
| Tipo | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (por exemplo)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (por exemplo) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* A workspaceId é chamada de consumerId na API do Log Analytics.

## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo do Azure Resource Manager para executar a extensão do agente do Log Analytics durante uma implantação de modelo do Azure Resource Manager. Um exemplo de modelo que inclui a extensão de VM do agente do Log Analytics pode ser encontrado na [Galeria de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

O JSON para uma extensão da máquina virtual pode ser aninhado dentro do recurso de máquina virtual ou localizado no nível de raiz ou superior de um modelo JSON do Resource Manager. O posicionamento do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte [Definir o nome e o tipo de recursos filho](../../azure-resource-manager/resource-manager-templates-resources.md#child-resources). 

O exemplo a seguir pressupõe que a extensão Log Analytics esteja aninhada dentro do recurso de máquina virtual. Ao aninhar o recurso de extensão, o JSON é colocado no objeto `"resources": []` da máquina virtual.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

Ao inserir o JSON da extensão na raiz do modelo, o nome do recurso inclui uma referência na máquina virtual pai e o tipo reflete a configuração aninhada. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>Implantação do PowerShell

O `Set-AzVMExtension` comando pode ser usado para implantar a extensão de máquina virtual do agente do Log Analytics em uma máquina virtual existente. Antes de executar o comando, as configurações públicas e privadas precisam ser armazenadas em uma tabela de hash do PowerShell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Os dados sobre o estado das implantações de extensão podem ser recuperados no Portal do Azure usando o módulo do Azure PowerShell. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir usando o módulo do Azure PowerShell.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A saída de execução da extensão é registrada nos arquivos localizados no seguinte diretório:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
