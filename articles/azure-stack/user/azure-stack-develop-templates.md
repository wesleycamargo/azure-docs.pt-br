---
title: Desenvolver modelos para o Azure Stack | Microsoft Docs
description: Conheça as práticas recomendadas do modelo do Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: b71fd64692f564c693ced48ca19afd1f5f2d0179
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238798"
---
# <a name="azure-resource-manager-template-considerations"></a>Considerações sobre o modelo do Azure Resource Manager

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Ao desenvolver um aplicativo, é importante garantir a portabilidade do modelo entre o Azure e o Azure Stack. Este artigo fornece considerações sobre o desenvolvimento do Azure Resource Manager [modelos](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf), de modo que você possa construir protótipos do seu aplicativo e testar sua implantação no Azure sem acesso a um ambiente do Azure Stack.

## <a name="resource-provider-availability"></a>Disponibilidade do provedor de recursos

O modelo que você planeja implantar somente deve usar os serviços do Microsoft Azure que já estão disponíveis ou na visualização no Azure Stack.

## <a name="public-namespaces"></a>Namespaces públicos

Como o Azure Stack está hospedado no seu datacenter, ele tem namespaces de ponto de extremidade de serviço diferentes do da nuvem pública do Azure. Como resultado, pontos de extremidade públicos embutidos nos modelos do Azure Resource Manager falharem quando você tenta implantá-los para o Azure Stack. Você pode criar dinamicamente os pontos de extremidade de serviço usando o `reference` e `concatenate` funções para recuperar valores do provedor de recursos durante a implantação. Por exemplo, em vez de embutir *blob.core.windows.net* em seu modelo, recuperar as [primaryendpoints. blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-vm-windows-create/azuredeploy.json#L175) para definir dinamicamente o *osdisk. URI* ponto de extremidade:

```json
"osDisk": {"name": "osdisk","vhd": {"uri":
"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
 '/',variables('OSDiskName'),'.vhd')]"}}
```

## <a name="api-versioning"></a>Controle de versão de API

As versões de serviço do Azure podem ser diferentes entre o Azure e o Azure Stack. Cada recurso requer o **apiVersion** atributo, que define os recursos oferecidos. Para garantir a compatibilidade de versão de API no Azure Stack, as seguintes versões de API são válidas para cada provedor de recursos:

| Provedor de recursos | apiVersion |
| --- | --- |
| Computação |`'2015-06-15'` |
| Rede |`'2015-06-15'`, `'2015-05-01-preview'` |
| Armazenamento |`'2016-01-01'`, `'2015-06-15'`, `'2015-05-01-preview'` |
| KeyVault | `'2015-06-01'` |
| Serviço de Aplicativo |`'2015-08-01'` |

## <a name="template-functions"></a>Funções de modelo

O Azure Resource Manager [funções](../../azure-resource-manager/resource-group-template-functions.md) fornecem os recursos necessários para compilar modelos dinâmicos. Por exemplo, você pode usar funções para tarefas como:

* CONCATENAR ou filtrar cadeias de caracteres.
* Fazer referência a valores de outros recursos.
* Iterar recursos para implantar várias instâncias.

Essas funções não estão disponíveis no Azure Stack:

* Skip
* Take

## <a name="resource-location"></a>Local do recurso

Modelos do Azure Resource Manager usam um `location` atributo colocar recursos durante a implantação. No Azure, locais se referem a uma região, como Oeste dos EUA ou na América do Sul. No Azure Stack, as localizações são diferentes, pois ele está em seu datacenter. Para garantir que os modelos são transferidos entre o Azure e o Azure Stack, você deve referenciar o local do grupo de recursos, como você implanta recursos individuais. Você pode fazer isso usando `[resourceGroup().Location]` para garantir que todos os recursos herdam o local do grupo de recursos. O código a seguir é um exemplo de como usar essa função ao implantar uma conta de armazenamento:

```json
"resources": [
{
  "name": "[variables('storageAccountName')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "[variables('apiVersionStorage')]",
  "location": "[resourceGroup().location]",
  "comments": "This storage account is used to store the VM disks",
  "properties": {
  "accountType": "Standard_GRS"
  }
}
]
```

## <a name="next-steps"></a>Próximas etapas

* [Implantar modelos com o PowerShell](azure-stack-deploy-template-powershell.md)
* [Implantar modelos com a CLI do Azure](azure-stack-deploy-template-command-line.md)
* [Implantar modelos com o Visual Studio](azure-stack-deploy-template-visual-studio.md)
