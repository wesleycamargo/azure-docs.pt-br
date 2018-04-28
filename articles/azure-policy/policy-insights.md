---
title: Criar políticas por meio de programação e exibir dados de conformidade com o Azure Policy | Microsoft Docs
description: Este artigo orienta você na criação e gerenciamento por meio de programação de políticas no Azure Policy.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2018
ms.topic: article
ms.service: azure-policy
manager: carmonm
ms.custom: ''
ms.openlocfilehash: bd0dbb1b6b44b34fc86b8c73fa586b1b4cf880f3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Criar políticas por meio de programação e exibir dados de conformidade

Este artigo orienta você na criação e gerenciamento por meio de programação de políticas. Ele também mostra como exibir estados de conformidade do recurso e as políticas. As definições de políticas impõem diferentes regras e ações em seus recursos. A imposição garante que os recursos permaneçam em conformidade com seus padrões empresariais e contratos de nível de serviço.

## <a name="prerequisites"></a>pré-requisitos

Antes de começar, verifique se os seguintes pré-requisitos são cumpridos:

1. Caso ainda não tenha feito, instale o [ARMClient](https://github.com/projectkudu/ARMClient). É uma ferramenta que envia solicitações HTTP para APIs baseadas no Azure Resource Manager.
2. Atualize seu módulo do PowerShell do AzureRM para a versão mais recente. Para obter mais informações sobre a versão mais recente, consulte o Azure PowerShell https://github.com/Azure/azure-powershell/releases.
3. Registre o provedor de recursos de Informações de Política usando Azure PowerShell para garantir que sua assinatura funcione com o provedor de recursos. Para registrar um provedor de recursos, você deve ter permissão para executar a operação de ação de registro para o provedor de recursos. Esta operação está incluída nas funções de Colaborador e de Proprietário. Execute o seguinte comando para registrar o provedor de recursos:

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.PolicyInsights
  ```

  Para saber mais sobre como registrar e exibir provedores de recursos, consulte [Provedores de recursos e tipos](../azure-resource-manager/resource-manager-supported-services.md).
4. Se ainda não fez, instale a CLI do Azure. Você pode obter a versão mais recente em [Instalar a CLI 2.0 do Azure no Windows](/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-and-assign-a-policy-definition"></a>Criar e atribuir uma definição de política

A primeira etapa para obter melhor visibilidade de seus recursos é criar e atribuir políticas sobre seus recursos. A próxima etapa é aprender a criar e atribuir uma política por meio de programação. O exemplo de política realiza auditoria nas contas de armazenamento que estão abertas para todas as redes públicas usando PowerShell, CLI do Azure e solicitações HTTP.

Os seguintes comandos criam definições de política para a camada Standard. A camada Standard ajuda você a obter correção, avaliação de conformidade e gerenciamento em escala. Para obter mais informações sobre os tipos de preços, consulte [Preços do Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy).

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Criar e atribuir uma definição de política com o PowerShell

1. Use o trecho JSON a seguir para criar um arquivo JSON com o nome AuditStorageAccounts.json.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

  Para obter mais informações sobre a criação de uma definição de política, consulte [Estrutura da definição do Azure Policy](policy-definition.md).
2. Execute o seguinte comando para criar uma definição de política usando os arquivos AuditStorageAccounts.json.

  ```azurepowershell-interactive
  New-AzureRmPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy AuditStorageAccounts.json
  ```

  O comando cria uma definição de política denominada _Auditar Contas de Armazenamento Abertas para Redes Públicas_. Para obter mais informações sobre outros parâmetros que você pode usar, consulte [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition).
3. Depois de criar sua definição de política, você pode criar uma atribuição de política executando os seguintes comandos:

  ```azurepowershell-interactive
  $rg = Get-AzureRmResourceGroup -Name 'ContosoRG'

  $Policy = Get-AzureRmPolicyDefinition -Name 'AuditStorageAccounts'

  New-AzureRmPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId –Sku @{Name='A1';Tier='Standard'}
  ```

  Substitua _ContosoRG_ pelo nome do grupo de recursos desejado.

Para obter mais informações sobre como gerenciar políticas de recursos usando o módulo PowerShell do Azure Resource Manager, consulte [AzureRM.Resources](/powershell/module/azurerm.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Criar e atribuir uma definição de política usando ARMClient

Use o procedimento a seguir para criar uma definição de política.

1. Copie o trecho JSON a seguir para criar um arquivo JSON. Você chamará o arquivo na próxima etapa.

  ```json
  "properties": {
      "displayName": "Audit Storage Accounts Open to Public Networks",
      "policyType": "Custom",
      "mode": "Indexed",
      "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
      "parameters": {},
      "policyRule": {
          "if": {
              "allOf": [{
                      "field": "type",
                      "equals": "Microsoft.Storage/storageAccounts"
                  },
                  {
                      "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                      "equals": "Allow"
                  }
              ]
          },
          "then": {
              "effect": "audit"
          }
      }
  }
  ```

2. Crie a definição de política usando a seguinte chamada:

  ```
  armclient PUT "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
  ```

  Substitua o &lt;subscriptionId&gt; anterior pela ID da assinatura pretendida.

Para obter mais informações sobre a estrutura da consulta, consulte [Definições de Políticas – Criar ou Atualizar](/rest/api/resources/policydefinitions/createorupdate).

Use o procedimento a seguir para criar uma atribuição de política e atribuir a definição de política no nível do grupo de recursos.

1. Copie o trecho JSON a seguir para criar um arquivo de atribuição de política JSON. Substitua as informações de exemplo nos símbolos &lt;&gt; pelos seus próprios valores.

  ```json
  {
      "properties": {
          "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
          "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
          "parameters": {},
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
          "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
      },
      "sku": {
          "name": "A1",
          "tier": "Standard"
      }
  }
  ```

2. Crie a atribuição de política usando a seguinte chamada:

  ```
  armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
  ```

  Substitua as informações de exemplo nos símbolos &lt;&gt; pelos seus próprios valores.

  Para obter mais informações sobre como fazer chamadas HTTP para a API REST, consulte [Recursos da API REST do Azure](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Criar e atribuir uma definição de política com a CLI do Azure

Para criar uma definição de política, use o procedimento a seguir:

1. Copie o trecho JSON a seguir para criar um arquivo de atribuição de política JSON.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

2. Execute o seguinte comando para criar uma definição de política:

  ```azurecli-interactive
az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
  ```

3. Use o comando a seguir para criar uma atribuição de política. Substitua as informações de exemplo nos símbolos &lt;&gt; pelos seus próprios valores.

  ```azurecli-interactive
  az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>' --sku 'standard'
  ```

Você pode obter a ID de definição da política usando o PowerShell com o seguinte comando:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

A ID de definição de política para a definição de política que você criou deve se assemelhar ao exemplo a seguir:

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Para obter mais informações sobre como gerenciar políticas de recursos com a CLI do Azure, consulte [Políticas de Recursos da CLI do Azure](/cli/azure/policy?view=azure-cli-latest).

## <a name="identify-non-compliant-resources"></a>Identificar recursos sem conformidade

Em uma atribuição, um recurso não está em conformidade se ele não segue as regras de iniciativa ou política. A tabela a seguir mostra como as diferentes ações da política funcionam com a avaliação da condição para o estado de conformidade resultante:

| **Estado do recurso** | **Ação** | **Avaliação da política** | **Estado de conformidade** |
| --- | --- | --- | --- |
| Exists | Negar, Auditoria, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Sem conformidade |
| Exists | Negar, Auditoria, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Falso | Em conformidade |
| Novo | Auditoria, AuditIfNotExist\* | True | Sem conformidade |
| Novo | Auditoria, AuditIfNotExist\* | Falso | Em conformidade |

\* As ações de Acrescentar, DeployIfNotExist e AuditIfNotExist exigem que a instrução IF seja TRUE. As ações também exigem que a condição de existência seja FALSE para não estar em conformidade. Quando TRUE, a condição IF dispara a avaliação da condição de existência para os recursos relacionados.

Para entender melhor como os recursos são sinalizados como sem conformidade, vamos usar o exemplo de atribuição de política criado anteriormente.

Por exemplo, suponha que você tenha um grupo de recursos – ContsoRG, com algumas contas de armazenamento (realçadas em vermelho) que são expostas para redes públicas.

![Contas de armazenamento expostas a redes públicas](./media/policy-insights/resource-group01.png)

Neste exemplo, você precisa estar atento aos riscos de segurança. Agora que você criou uma atribuição de política, ela é avaliada em relação a todas as contas de armazenamento no grupo de recursos ContosoRG. Ela realiza auditoria nas três contas de armazenamento sem conformidade, consequentemente alterando seus estados para **sem conformidade**.

![Auditoria de contas de armazenamento sem conformidade](./media/policy-insights/resource-group03.png)

Use o procedimento a seguir para identificar recursos em um grupo de recursos que não estejam em conformidade com a atribuição de política. No exemplo, os recursos são contas de armazenamento no grupo de recursos ContosoRG.

1. Obtenha a ID de atribuição da política executando os seguintes comandos:

  ```azurepowershell-interactive
  $policyAssignment = Get-AzureRmPolicyAssignment | Where-Object {$_.Properties.displayName -eq 'Audit Storage Accounts with Open Public Networks'}

  $policyAssignment.PolicyAssignmentId
  ```

  Para saber mais sobre a obtenção da ID de atribuição da política, consulte [Get-AzureRMPolicyAssignment](https://docs.microsoft.com/powershell/module/azurerm.resources/Get-AzureRmPolicyAssignment).

2. Execute o seguinte comando para obter as IDs de recurso dos recursos sem conformidade copiados para um arquivo JSON:

  ```
  armclient POST "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
  ```

3. Os resultados deverão se assemelhar ao exemplo a seguir:

  ```json
  {
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
      "@odata.count": 3,
      "value": [{
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount1Id>"
          },
          {
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount2Id>"
          },
          {
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount3ID>"
          }
      ]
  }
  ```

Os resultados são equivalentes aos que você geralmente veria listados em **Recursos sem conformidade** na [exibição do Portal do Azure](assign-policy-definition.md#identify-non-compliant-resources).

Atualmente, os recursos sem conformidade somente são identificados usando o Portal do Azure e com solicitações HTTP. Para obter mais informações sobre como consultar os estados de política, consulte o artigo de referência de API [Estado da Política](/rest/api/policy-insights/policystates).

## <a name="view-policy-events"></a>Exibir eventos de política

Quando um recurso é criado ou atualizado, um resultado da avaliação da política é gerado. Os resultados são chamados _Eventos de Política_. Execute a consulta a seguir para exibir todos os eventos de política associados à atribuição de política.

```
armclient POST "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2017-12-12-preview"
```

Seus resultados devem se parecer com o exemplo a seguir:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 3
    }]
}
```

Como os estados de política, você só pode exibir eventos de política com solicitações HTTP. Para obter mais informações sobre como consultar os eventos de política, consulte o artigo de referência de [Eventos de Política](/rest/api/policy-insights/policyevents).

## <a name="change-a-policy-assignments-pricing-tier"></a>Alterar o tipo de preço de uma atribuição de política

Você pode usar o cmdlet do PowerShell *Set-AzureRmPolicyAssignment* para atualizar o tipo de preço para Standard ou Gratuito em uma atribuição de política existente. Por exemplo: 

```azurepowershell-interactive
Set-AzureRmPolicyAssignment -Id '/subscriptions/<subscriptionId/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/<policyAssignmentID>' -Sku @{Name='A1';Tier='Standard'}
```

Para obter mais informações sobre o cmdlet, consulte [Set-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/Set-AzureRmPolicyAssignment).

## <a name="next-steps"></a>Próximas etapas

Examine os seguintes artigos para obter mais informações sobre os comandos e consultas neste artigo.

- [Recursos da API REST do Azure](/rest/api/resources/)
- [Módulos do PowerShell do Azure RM](/powershell/module/azurerm.resources/#policies)
- [Comandos de Política da CLI do Azure](/cli/azure/policy?view=azure-cli-latest)
- [Referência da API REST do provedor de recursos de Informações de Política](/rest/api/policy-insights)
