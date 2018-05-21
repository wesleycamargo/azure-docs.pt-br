---
title: Use a CLI do Azure para criar uma atribuição de política para identificar recursos sem conformidade em seu ambiente do Azure
description: Use o PowerShell para criar uma atribuição de Política do Azure para identificar recursos sem conformidade.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: d4d92dc56a9320a4deb0adf611edded0c018df3f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Crie uma atribuição de política para identificar recursos sem conformidade em seu ambiente do Azure com a CLI do Azure

A primeira etapa para compreender a conformidade no Azure é identificar o status de seus recursos. Este guia de início rápido orienta você no processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão usando discos gerenciados.

No final deste processo, você identificará com êxito quais máquinas virtuais não estão usando discos gerenciados. Eles *não estão em conformidade* com a atribuição da política.

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este guia usa a CLI do Azure para criar uma atribuição de política e para identificar recursos sem conformidade em seu ambiente do Azure.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Este início rápido exige a execução da CLI do Azure versão 2.0.4 ou posterior para instalar e usar a CLI localmente. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>pré-requisitos

Registre o provedor de recursos Informações de Política usando a CLI do Azure. O registro do provedor de recursos garante o funcionamento da assinatura com ele. Para registrar um provedor de recursos, você deve ter permissão para executar a operação de ação de registro para o provedor de recursos. Esta operação está incluída nas funções de Colaborador e de Proprietário. Execute o seguinte comando para registrar o provedor de recursos:

```azurecli-interactive
az provider register –-namespace 'Microsoft.PolicyInsights'
```

Para saber mais sobre como registrar e exibir provedores de recursos, consulte [Provedores de recursos e tipos](../azure-resource-manager/resource-manager-supported-services.md)

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste guia de início rápido, crie uma atribuição de política e atribua a definição Auditar Máquinas Virtuais sem Managed Disks. Esta definição de política identifica recursos que não são compatíveis com as condições configuradas na definição de política.

Execute o comando a seguir para criar uma atribuição de política:

```azurecli-interactive
az policy assignment create --name 'Audit Virtual Machines without Managed Disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

O comando anterior usa as seguintes informações:

- **Nome**: nome de exibição da atribuição de política. Nesse caso, você está usando *Auditar Máquinas Virtuais sem atribuição de Managed Disks*.
- **Política**: a ID de definição da política, com base naquela que você está usando para criar a atribuição. Nesse caso, é a definição de política – *Auditar Máquinas Virtuais sem Managed Disks*. Para obter a ID de definição da política, execute este comando: `az policy definition show --name 'Audit Virtual Machines without Managed Disks Assignment'`
- **Escopo**: um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta. Pode variar de uma assinatura a grupos de recursos. Substitua o &lt;escopo&gt; pelo nome do seu grupo de recursos.

## <a name="identify-non-compliant-resources"></a>Identificar recursos sem conformidade

Para exibir os recursos que não são compatíveis com essa nova atribuição, obtenha a ID de atribuição de política executando os comandos a seguir:

```azurepowershell-interactive
$policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit Virtual Machines without Managed Disks' }
$policyAssignment.PolicyAssignmentId
```

Para saber mais sobre as IDs de atribuição de política, consulte [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

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

Outros guias desta coleção dão continuidade a este guia de início rápido. Se você planeja continuar trabalhando com os tutoriais mais recentes, não limpe os recursos criados neste guia de início rápido. Se você não planeja continuar, exclua a atribuição criada executando este comando:

```azurecli-interactive
az policy assignment delete –name 'Audit Virtual Machines without Managed Disks Assignment' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você atribuiu uma definição de política para identificar recursos sem conformidade em seu ambiente do Azure.

Para saber mais sobre a atribuição de políticas e para garantir que os recursos que você criar no **futuro** estejam em conformidade, continue com o tutorial:

> [!div class="nextstepaction"]
> [Criando e gerenciando políticas](create-manage-policy.md)