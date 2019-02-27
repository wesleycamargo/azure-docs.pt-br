---
title: Especifique o grupo de recursos para VMs no Azure DevTest Labs | Microsoft Docs
description: Saiba como especificar um grupo de recursos para VMs em um laboratório no Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: 94e5f5b29e93409df2373cf6c56e8185dc5373a2
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312966"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Especifique um grupo de recursos para máquinas virtuais do laboratório no Azure DevTest Labs
Como um proprietário de laboratório, você pode configurar suas máquinas virtuais do laboratório a serem criadas em um grupo de recursos específico. Esse recurso o ajuda nos seguintes cenários: 

- Tem menos grupos de recursos criados por laboratórios na sua assinatura.
- Seus laboratórios devem operar dentro de um conjunto fixo de grupos de recursos configurados por você
- Contornar as restrições e aprovações necessárias para a criação de grupos de recursos na sua assinatura do Azure.
- Consolidar todos os seus recursos de laboratório dentro de um único grupo de recursos para simplificar o acompanhamento desses recursos e aplicar [políticas](../governance/policy/overview.md) para gerenciá-los no nível do grupo de recursos.

Com esse recurso, você pode usar um script para especificar um grupo de recursos novo ou existente em sua assinatura do Azure para todas as VMs de seu laboratório. Atualmente, o DevTest Labs dá suporte a esse recurso por meio de uma API. 

## <a name="api-to-configure-a-resource-group-for-lab-virtual-machines"></a>API para configurar um grupo de recursos para máquinas virtuais do laboratório
Agora vamos examinar as opções disponíveis ao usar essa API como um proprietário de laboratório: 

- Você pode escolher o **grupo de recursos do laboratório** para todas as máquinas virtuais.
- Você pode escolher um **grupo de recursos existente** que não seja o grupo de recursos do laboratório para todas as máquinas virtuais.
- Você pode inserir um nome do **novo grupo de recursos** para todas as máquinas virtuais.
- Você pode continuar com o comportamento existente, ou seja, um grupo de recursos é criado para cada VM no laboratório.
 
Essa configuração se aplica a novas máquinas virtuais criadas no laboratório. As VMs mais antigas em seu laboratório que foram criadas em seus próprios grupos de recursos continuam não sendo afetadas. Ambientes criados em seu laboratório continuam em seus próprios grupos de recursos.

### <a name="how-to-use-this-api"></a>Como usar essa API:
- Use a versão de API **2018_10_15_preview** enquanto usa essa API. 
- Se você especificar um novo grupo de recursos, verifique se você tem **permissões de gravação em grupos de recursos** dentro de sua assinatura. Sem permissões de gravação, criar novas máquinas virtuais no grupo de recursos especificado resulta em falha. 
- Ao usar a API, passe a **ID completa do grupo de recursos**. Por exemplo: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Verifique se o grupo de recursos está na mesma assinatura que o laboratório. 

## <a name="use-powershell"></a>Usar o PowerShell 
O exemplo a seguir descreve como criar todas as máquinas virtuais de laboratório em um novo grupo de recursos usando um script do PowerShell.

```PowerShell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

Invoque o script usando o comando a seguir (ResourceGroup.ps1 é o arquivo que contém o script anterior): 

```PowerShell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-azure-resource-manager-template"></a>Usar o modelo do Azure Resource Manager
Se você estiver usando o modelo do Azure Resource Manager para criar um laboratório, use a propriedade **vmCreationResourceGroupId** na seção de propriedades do laboratório do modelo do Resource Manager, conforme mostrado no exemplo a seguir:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos: 

- [Definir políticas para um laboratório](devtest-lab-get-started-with-lab-policies.md)
- [Perguntas frequentes](devtest-lab-faq.md)
