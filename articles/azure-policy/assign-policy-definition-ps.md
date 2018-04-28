---
title: 'Início Rápido: Usar o PowerShell para criar uma atribuição de política para identificar recursos sem conformidade em seu ambiente do Azure | Microsoft Docs'
description: Neste início rápido, use o PowerShell para criar uma atribuição do Azure Policy para identificar recursos sem conformidade.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/10/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: f39177f5a2e5878570ed750a42009c2203257b13
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-the-azure-rm-powershell-module"></a>Início Rápido: crie uma atribuição de política para identificar recursos sem conformidade usando o módulo do PowerShell do Azure RM

A primeira etapa para compreender a conformidade no Azure é identificar o status de seus recursos. Neste guia de início rápido, você cria uma atribuição de política para identificar máquinas virtuais que não estão usando discos gerenciados. Ao concluir, você identificará máquinas virtuais que *não estão em conformidade* com a atribuição da política.

O módulo do PowerShell do AzureRM é usado para criar e gerenciar recursos do Azure da linha de comando do ou em scripts. Este guia explica como usar o AzureRM para criar uma atribuição de política. A política identifica recursos sem conformidade em seu ambiente do Azure.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>pré-requisitos

- Antes de começar, verifique se a versão mais recente do PowerShell está instalada. Para obter informações detalhadas, confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).
- Atualize seu módulo do PowerShell do AzureRM para a versão mais recente. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).
- Registre o provedor de recursos Informações de Política usando o Azure PowerShell. O registro do provedor de recursos garante o funcionamento da assinatura com ele. Para registrar um provedor de recursos, você deve ter permissão para executar a operação de ação de registro para o provedor de recursos. Esta operação está incluída nas funções de Colaborador e de Proprietário. Execute o seguinte comando para registrar o provedor de recursos:

  ```
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.PolicyInsights
  ```

  Para saber mais sobre como registrar e exibir provedores de recursos, consulte [Provedores de recursos e tipos](../azure-resource-manager/resource-manager-supported-services.md)

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste guia de início rápido, crie uma atribuição de política e atribua a definição *Auditar máquinas virtuais sem Managed Disks*. Esta definição de política identifica recursos que não são compatíveis com as condições configuradas na definição de política.

Execute o comando a seguir para criar uma nova atribuição de política:

```powershell
$rg = Get-AzureRmResourceGroup -Name "<resourceGroupName>"
$definition = Get-AzureRmPolicyDefinition | where {$_.properties.displayName -eq "Audit VMs that do not use managed disks"}
New-AzureRMPolicyAssignment -Name "Audit Virtual Machines without Managed Disks" -Scope $rg.ResourceId -PolicyDefinition $definition -Sku @{Name='A1';Tier='Standard'}
```

Os comandos anteriores usam as seguintes informações:

- **Nome**: nome de exibição da atribuição de política. Nesse caso, você está usando *Auditar Máquinas Virtuais sem atribuição de Managed Disks*.
- **Definição**: a definição da política, com base naquela que você está usando para criar a atribuição. Nesse caso, é a definição de política – *Auditar Máquinas Virtuais sem Managed Disks*.
- **Escopo**: um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta. Pode variar de uma assinatura a grupos de recursos. Substitua o &lt;escopo&gt; pelo nome do seu grupo de recursos.
- **SKU**: esse comando cria uma atribuição de política com o nível standard. O nível standard permite que você atinja correção, avaliação de conformidade e gerenciamento em escala. Para obter detalhes adicionais sobre tipos de preço, consulte [Preços do Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy).


Agora, você está pronto para identificar recursos sem conformidade para compreender o estado de conformidade de seu ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos sem conformidade

Use as informações a seguir para identificar recursos que não são compatíveis com a atribuição de política que você criou. Execute os seguintes comandos:

```powershell
$policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Virtual Machines without Managed Disks"}
```

```powershell
$policyAssignment.PolicyAssignmentId
```

Para saber mais sobre as IDs de atribuição de política, consulte [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Em seguida, execute o seguinte comando para obter as IDs de recurso dos recursos não compatíveis produzidos em um arquivo JSON:

```powershell
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```
Seus resultados devem se parecer com o exemplo a seguir:


```
{
"@odata.context":"https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
"@odata.count": 3,
"value": [
{
    "@odata.id": null,
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
    },
    {
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
         },
{
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
         }

]
}
```

Os resultados são comparáveis aos que você geralmente vê listados em **Recursos não compatível** na exibição do Portal do Azure.


## <a name="clean-up-resources"></a>Limpar recursos

Outros guias desta coleção dão continuidade a este guia de início rápido. Se você planeja continuar trabalhando com os outros tutoriais, não limpe os recursos criados neste guia de início rápido. Se você não planeja continuar, exclua a atribuição criada executando este comando:

```powershell
Remove-AzureRmPolicyAssignment -Name "Audit Virtual Machines without Managed Disks Assignment" -Scope /subscriptions/<subscriptionID>/<resourceGroupName>
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você atribuiu uma definição de política para identificar recursos sem conformidade em seu ambiente do Azure.

Para saber mais sobre a atribuição de políticas, e para garantir que os recursos que você criar no **futuro** estejam em conformidade, continue com o tutorial:

> [!div class="nextstepaction"]
> [Criando e gerenciando políticas](./create-manage-policy.md)
