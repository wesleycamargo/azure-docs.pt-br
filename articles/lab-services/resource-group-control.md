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
ms.date: 02/05/2019
ms.author: spelluru
ms.openlocfilehash: ddda9ef2b9bb716f7cdd33aa8fe9233f6c7d8e82
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55748993"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Especifique um grupo de recursos para máquinas virtuais do laboratório no Azure DevTest Labs
Como um proprietário de laboratório, você pode configurar suas máquinas virtuais do laboratório a serem criadas em um grupo de recursos específico. Use este recurso para que você não alcance os limites de grupo de recursos em sua assinatura do Azure. Esse recurso também permite que você consolide todos os seus recursos de laboratório dentro de um único grupo de recursos. Isso também simplifica o acompanhamento desses recursos e a aplicação de [políticas](../governance/policy/overview.md) para gerenciá-los no nível do grupo de recursos.

Com esse recurso, você pode usar um script para especificar um grupo de recursos novo ou existente em sua assinatura do Azure para todas as VMs de seu laboratório. Atualmente, o DevTest Labs dá suporte a esse recurso por meio de uma API. 

## <a name="api-to-configure-a-resource-group-for-labs-vms"></a>API para configurar um grupo de recursos para VMS do labs
Agora vamos examinar as opções disponíveis ao usar essa API como um proprietário de laboratório: 

- Você pode escolher o **grupo de recursos do laboratório** para todas as máquinas virtuais.
- Você pode escolher um **grupo de recursos existente** que não seja o grupo de recursos do laboratório para todas as máquinas virtuais.
- Você pode inserir um nome do **novo grupo de recursos** para todas as máquinas virtuais.
- Você pode continuar com o comportamento existente, ou seja, um grupo de recursos é criado para cada VM no laboratório.
 
Essa configuração se aplica a novas máquinas virtuais criadas no laboratório. As VMs mais antigas em seu laboratório que foram criadas em seus próprios grupos de recursos continuam não sendo afetadas. No entanto, você pode migrar essas máquinas virtuais dos seus grupos de recursos individuais para o grupo de recursos comuns para que todas as suas máquinas virtuais do laboratório estejam em um grupo de recursos comum. Para obter mais informações, veja [Mover recursos para um novo grupo de recursos](../azure-resource-manager/resource-group-move-resources.md). Ambientes criados em seu laboratório continuam em seus próprios grupos de recursos.

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
                "uniqueIdentifier": "6e6f668f-992b-435c-bac3-d328b745cd25"
            },
            "dependsOn": []
        },
```


## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos: 

- [Definir políticas para um laboratório](devtest-lab-get-started-with-lab-policies.md)
- [Perguntas frequentes](devtest-lab-faq.md)
