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
ms.date: 03/07/2019
ms.author: spelluru
ms.openlocfilehash: 1001e6aec7ba2f6ce62eb267d218149296048bb9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60394330"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Especifique um grupo de recursos para máquinas virtuais do laboratório no Azure DevTest Labs

Como um proprietário de laboratório, você pode configurar suas máquinas virtuais do laboratório a serem criadas em um grupo de recursos específico. Esse recurso o ajuda nos seguintes cenários:

- Tem menos grupos de recursos criados por laboratórios na sua assinatura.
- Ter seus laboratórios operar dentro de um conjunto fixo de grupos de recursos que você configurar.
- Contornar as restrições e aprovações necessárias para a criação de grupos de recursos na sua assinatura do Azure.
- Consolidar todos os seus recursos de laboratório dentro de um único grupo de recursos para simplificar o acompanhamento desses recursos e aplicando [políticas](../governance/policy/overview.md) para gerenciar recursos no nível do grupo de recursos.

Com esse recurso, você pode usar um script para especificar um grupo de recursos novo ou existente em sua assinatura do Azure para todas as VMs de seu laboratório. Atualmente, o Azure DevTest Labs dá suporte a esse recurso por meio de uma API.

## <a name="use-azure-portal"></a>Usar o portal do Azure
Siga estas etapas para especificar um grupo de recursos para todas as VMs criadas no laboratório. 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu de navegação à esquerda. 
3. Selecione **DevTest Labs** na lista.
4. Na lista de laboratórios, selecione suas **laboratório**.  
5. Selecione **configuração e políticas** na **configurações** seção no menu à esquerda. 
6. Selecione **configurações de laboratório** no menu à esquerda. 
7. Selecione **todas as máquinas virtuais em um grupo de recursos**. 
8. Selecione um grupo de recursos existente na lista suspensa lista (ou) selecione **criar novo**, insira um **nome** para o grupo de recursos e selecione **Okey**. 

    ![Selecione o grupo de recursos para todas as VMs de laboratório](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Usar o PowerShell 
O exemplo a seguir mostra como usar um script do PowerShell para criar todas as máquinas virtuais de laboratório em um novo grupo de recursos.

```powershell
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

Invocar o script usando o comando a seguir. ResourceGroup.ps1 é o arquivo que contém o script anterior:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Usar um modelo do Azure Resource Manager
Se você estiver usando um modelo do Azure Resource Manager para criar um laboratório, use o **vmCreationResourceGroupId** propriedade na seção de propriedades do laboratório do seu modelo, conforme mostrado no exemplo a seguir:

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


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API para configurar um grupo de recursos para VMs do laboratório
Você tem as seguintes opções como um proprietário de laboratório ao usar essa API:

- Escolha o **grupo de recursos do laboratório** todas as máquinas virtuais.
- Escolha uma **grupo de recursos existente** que não seja o grupo de recursos do laboratório para todas as máquinas virtuais.
- Insira um **novo grupo de recursos** nome para todas as máquinas virtuais.
- Continue a usar o comportamento existente, no qual um grupo de recursos é criado para cada VM no laboratório.
 
Essa configuração se aplica a novas máquinas virtuais criadas no laboratório. As VMs mais antigas em seu laboratório que foram criadas em seus próprios grupos de recursos não serão afetadas. Ambientes são criados em seu laboratório continuam permanecem em seus próprios grupos de recursos.

Como usar essa API:
- Versão de API de uso **2018_10_15_preview**.
- Se você especificar um novo grupo de recursos, certifique-se de que você tenha **permissões de gravação em grupos de recursos** em sua assinatura. Se você não tem permissões de gravação, a criação de novas máquinas virtuais no grupo de recursos especificado falhará.
- Ao usar a API, passe a **ID completa do grupo de recursos**. Por exemplo: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Verifique se o grupo de recursos na mesma assinatura que o laboratório. 


## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos: 

- [Definir políticas para um laboratório](devtest-lab-get-started-with-lab-policies.md)
- [Perguntas frequentes](devtest-lab-faq.md)
