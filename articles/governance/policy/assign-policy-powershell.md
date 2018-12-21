---
title: Criar uma política para identificar recursos sem conformidade com o Azure PowerShell
description: Use o Azure PowerShell para criar uma atribuição do Azure Policy para identificar recursos sem conformidade.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 32fe811c80fd34b4ea3390a3f46a1d36aba7534e
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310701"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Criar uma atribuição de política para identificar recursos sem conformidade usando o Azure PowerShell

A primeira etapa para compreender a conformidade no Azure é identificar o status de seus recursos. Neste guia de início rápido, você cria uma atribuição de política para identificar máquinas virtuais que não estão usando discos gerenciados. Ao concluir, você identificará máquinas virtuais que *não estão em conformidade* com a atribuição da política.

O módulo do PowerShell do AzureRM é usado para criar e gerenciar recursos do Azure da linha de comando do ou em scripts. Este guia explica como usar o AzureRM para criar uma atribuição de política. A política identifica recursos sem conformidade em seu ambiente do Azure.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Caso ainda não tenha feito, instale o [ARMClient](https://github.com/projectkudu/ARMClient). É uma ferramenta que envia solicitações HTTP para APIs baseadas no Azure Resource Manager.
- Antes de começar, verifique se a versão mais recente do PowerShell está instalada. Para obter informações detalhadas, confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).
- Atualize seu módulo do PowerShell do AzureRM para a versão mais recente. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).
- Registre o provedor de recursos Informações de Política usando o Azure PowerShell. O registro do provedor de recursos garante o funcionamento da assinatura com ele. Para registrar um provedor de recursos, você deve ter permissão para registrar a operação do provedor de recursos. Esta operação está incluída nas funções de Colaborador e de Proprietário. Execute o seguinte comando para registrar o provedor de recursos:

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Para saber mais sobre como registrar e exibir provedores de recursos, consulte [Provedores de recursos e tipos](../../azure-resource-manager/resource-manager-supported-services.md)

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste guia de início rápido, crie uma atribuição de política e atribua a definição *Auditar VMs sem discos gerenciados*. Esta definição de política identifica recursos que não estão em conformidade com as condições configuradas na definição de política.

Execute o comando a seguir para criar uma nova atribuição de política:

```azurepowershell-interactive
$rg = Get-AzureRmResourceGroup -Name '<resourceGroupName>'
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }
New-AzureRmPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Os comandos anteriores usam as seguintes informações:

- **Nome** - O nome real da atribuição.  Neste exemplo, usamos *audit-vm-manageddisks*.
- **DisplayName** - O nome de exibição da atribuição de política. Nesse caso, você está usando *Auditar VMs sem atribuição de discos gerenciados*.
- **Definição**: a definição da política, com base naquela que você está usando para criar a atribuição. Nesse caso, é a ID da definição de política *Auditar VMs que não usam discos gerenciados*.
- **Escopo**: um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta. Pode variar de uma assinatura a grupos de recursos. Substitua o &lt;escopo&gt; pelo nome do seu grupo de recursos.

Agora, você está pronto para identificar recursos sem conformidade para compreender o estado de conformidade de seu ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos sem conformidade

Use as informações a seguir para identificar recursos que não são compatíveis com a atribuição de política que você criou. Execute os seguintes comandos:

```azurepowershell-interactive
$policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

Para saber mais sobre as IDs de atribuição de política, consulte [Get-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Em seguida, execute o seguinte comando para obter as IDs de recurso dos recursos não compatíveis produzidos em um arquivo JSON:

```
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Seus resultados devem se parecer com o exemplo a seguir:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
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

Use o seguinte comando para remover a atribuição criada:

```azurepowershell-interactive
Remove-AzureRmPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você atribuiu uma definição de política para identificar recursos sem conformidade em seu ambiente do Azure.

Para saber mais sobre a atribuição de políticas para validar que novos recursos estejam em conformidade, continue com o tutorial para:

> [!div class="nextstepaction"]
> [Criando e gerenciando políticas](./tutorials/create-and-manage.md)