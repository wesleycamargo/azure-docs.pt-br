---
title: Desenvolver modelos para o Azure Stack | Microsoft Docs
description: "Conheça as práticas recomendadas do modelo do Azure Stack"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: helaw
ms.openlocfilehash: b9109c58b29d5f09f1a86068a87c5e7f839228af
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2017
---
# <a name="azure-resource-manager-template-considerations"></a>Considerações sobre o modelo do Azure Resource Manager

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Ao desenvolver um aplicativo, é importante garantir a portabilidade do modelo entre o Azure e o Azure Stack.  Este tópico oferece considerações sobre o desenvolvimento de [modelos](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) do Azure Resource Manager, para que você possa construir protótipos do seu aplicativo e testar sua implantação no Azure sem acesso a um ambiente do Azure Stack.

## <a name="resource-provider-availability"></a>Disponibilidade do provedor de recursos
O modelo que você planeja implantar deve estar usando um serviço do Microsoft Azure que já está disponível ou no modo de visualização na pilha do Azure.

## <a name="public-namespaces"></a>Namespaces públicos
Como o Azure Stack está hospedado no seu datacenter, ele tem namespaces de ponto de extremidade de serviço diferentes do da nuvem pública do Azure. Como resultado, pontos de extremidade públicos em modelos do Gerenciador de recursos codificados falharem quando você tentar implantá-los a pilha do Azure. Em vez disso, é possível usar as funções *referência* e *concatenar* para criar dinamicamente o ponto de extremidade de serviço com base em valores recuperados do provedor de recursos durante a implantação. Por exemplo, em vez de especificar *blob.core.windows.net* em seu modelo, recupere o [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-simple-windows-vm/azuredeploy.json#L201) para definir dinamicamente o ponto de extremidade *osDisk.URI*:

     "osDisk": {"name": "osdisk","vhd": {"uri": 
     "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
      '/',variables('OSDiskName'),'.vhd')]"}}

## <a name="api-versioning"></a>Controle de versão de API
As versões de serviço do Azure podem ser diferentes entre o Azure e o Azure Stack. Cada recurso requer o atributo apiVersion, que define os recursos oferecidos. Para garantir a compatibilidade de versão de API na pilha do Azure, estas são as versões de API válidas para cada provedor de recursos:

| Provedor de Recursos | apiVersion |
| --- | --- |
| Computação |`'2015-06-15'` |
| Rede |`'2015-06-15'`, `'2015-05-01-preview'` |
| Armazenamento |`'2016-01-01'`, `'2015-06-15'`, `'2015-05-01-preview'` |
| KeyVault | `'2015-06-01'` |
| Serviço de Aplicativo |`'2015-08-01'` |
| MySQL |`'2015-09-01'` |
| SQL |`'2014-04-01-preview'` |

## <a name="template-functions"></a>Funções de modelo
As [funções](../../azure-resource-manager/resource-group-template-functions.md) do Resource Manager fornecem os recursos necessários para criar modelos dinâmicos. Por exemplo, é possível usar as funções para tarefas como:

* Concatenar ou filtrar cadeias de caracteres 
* Referenciar valores de outros recursos
* Iterar recursos para implantar várias instâncias 

Conforme você cria seus modelos, algumas funções não estão disponíveis no Kit de desenvolvimento de pilha do Azure e não devem ser usadas. Essas funções são:

* Skip
* Take

## <a name="resource-location"></a>Local do recurso
Os modelos do Resource Manager usam um atributo de localização para colocar recursos durante a implantação. No Azure, localização é uma região, como o oeste dos EUA ou a América do Sul. No Azure Stack, as localizações são diferentes, pois ele está em seu datacenter.  Para garantir que os modelos sejam transferíveis entre o Azure e o Azure Stack, você deve referenciar a localização do grupo de recursos enquanto implanta recursos individuais. Você pode fazer isso usando `[resourceGroup().Location]` para garantir que todos os recursos herdam o local do grupo de recursos.  O seguinte trecho de modelo do Resource Manager é um exemplo do uso dessa função ao implantar uma conta de armazenamento:

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

## <a name="next-steps"></a>Próximas etapas
* [Implantar modelos com o PowerShell](azure-stack-deploy-template-powershell.md)
* [Implantar modelos com a CLI do Azure](azure-stack-deploy-template-command-line.md)
* [Implantar modelos com o Visual Studio](azure-stack-deploy-template-visual-studio.md)

