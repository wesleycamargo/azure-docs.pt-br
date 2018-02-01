---
title: "Usar o PowerShell para criar uma atribuição de política para identificar recursos sem conformidade em seu ambiente do Azure | Microsoft Docs"
description: "Use o PowerShell para criar uma atribuição de Política do Azure para identificar recursos sem conformidade."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 1/17/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 67c779b96dab088d810d22ad3053ade106aec56a
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-using-powershell"></a>Crie uma atribuição de política para identificar recursos sem conformidade em seu ambiente do Azure usando o PowerShell

A primeira etapa para compreender a conformidade no Azure é identificar o status de seus recursos. Este guia de início rápido orienta você no processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão usando discos gerenciados.

No final deste processo, você identificará com êxito quais máquinas virtuais não estão usando discos gerenciados. Eles *não estão em conformidade* com a atribuição da política.

O PowerShell é usado para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este guia descreve com detalhes o uso PowerShell para criar uma atribuição de política para identificar recursos sem conformidade em seu ambiente do Azure.

Este guia exige o módulo do Azure PowerShell, versão 4.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa instalar ou atualizar, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

Antes de começar, verifique se a versão mais recente do PowerShell está instalada. Para obter informações detalhadas, confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.


## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste guia de início rápido, crie uma atribuição de política e atribua a definição *Auditar máquinas virtuais sem Managed Disks*. Esta definição de política identifica recursos que não são compatíveis com as condições configuradas na definição de política.

Siga estas etapas para criar uma nova atribuição de política.

1. Para garantir que sua assinatura funcione com o provedor de recursos, registre o provedor de recursos de Informações de Política. Para registrar um provedor de recursos, você deve ter permissão para executar a operação de ação de registro para o provedor de recursos. Esta operação está incluída nas funções de Colaborador e de Proprietário.

    Execute o seguinte comando para registrar o provedor de recursos:

    ```
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.PolicyInsights
```

    Você não poderá cancelar o registro de um provedor de recursos enquanto ainda tiver tipos de recursos do provedor de recursos em sua assinatura.

    Para obter mais detalhes sobre como registrar e exibir provedores de recursos, consulte [Provedores de recursos e tipos](../azure-resource-manager/resource-manager-supported-services.md).

2. Após o registro do provedor de recursos, execute o seguinte comando para exibir todas as definições de política e encontrar a que você deseja atribuir:

    ```powershell
$definition = Get-AzureRmPolicyDefinition
```

    A Política do Azure vem com definições de políticas internas que você pode usar. Você verá definições de políticas internas como:

    - Impor marca e seu valor
    - Aplicar marca e seu valor
    - Requer o SQL Server versão 12.0

3. Em seguida, atribua a definição de política ao escopo desejado usando o cmdlet `New-AzureRmPolicyAssignment`.

Para este tutorial, use as informações a seguir para o comando:

- **Nome** de exibição da atribuição de política. Nesse caso, use Auditar máquinas virtuais sem Managed Disks.
- **Política**: a definição da política, com base naquela que você está usando para criar a atribuição. Nesse caso, é a definição de política – *Auditar máquinas virtuais sem Managed Disks*
- Um **escopo** – um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta. Pode variar de uma assinatura a grupos de recursos. Neste exemplo, você está atribuindo a definição de política ao grupo de recursos **FabrikamOMS**.
- **$definition**: é necessário fornecer a ID do recurso da definição de política. Neste caso, você usa a ID da definição de política *Auditar máquinas virtuais sem Managed Disks*.

```powershell
$rg = Get-AzureRmResourceGroup -Name "FabrikamOMS"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

Agora, você está pronto para identificar recursos sem conformidade para compreender o estado de conformidade de seu ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos sem conformidade

1. Navegue de volta até a página de aterrissagem da Política do Azure.
2. Selecione **Conformidade** no painel esquerdo e pesquise a **Atribuição de Política** criada por você.

   ![Conformidade da política](media/assign-policy-definition/policy-compliance.png)

   Se houver recursos sem conformidade com essa nova atribuição, aparecem na guia **Recursos sem conformidade**.

## <a name="clean-up-resources"></a>Limpar recursos

Outros guias desta coleção dão continuidade a este guia de início rápido. Se você planeja continuar trabalhando com os tutoriais subsequentes, não limpe os recursos criados neste guia de início rápido. Se você não planeja continuar, exclua a atribuição criada executando este comando:

```powershell
Remove-AzureRmPolicyAssignment -Name “Audit Virtual Machines without Managed Disks Assignment” -Scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você atribuiu uma definição de política para identificar recursos sem conformidade em seu ambiente do Azure.

Para saber mais sobre a atribuição de políticas, para garantir que recursos que você criar no **futuro** estejam em conformidade, continue com o tutorial:

> [!div class="nextstepaction"]
> [Criando e gerenciando políticas](./create-manage-policy.md)
