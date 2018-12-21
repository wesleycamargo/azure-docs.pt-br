---
title: Exemplo – Auditar configurações de auditoria do SQL Server
description: Esta política de amostra audita as configurações de auditoria do servidor SQL.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 09/18/2018
ms.author: dacoulte
ms.openlocfilehash: 7a9f5779b8a0c853d938734f82f3bd63e7f0a45b
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53307862"
---
# <a name="audit-sql-server-audit-settings"></a>Auditar as configurações de auditoria do SQL Server

Essa política interna audita o SQL Server para verificar se as configurações de auditoria estão habilitadas.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Modelo de exemplo

```json
{
  "if": {
    "field": "type",
    "equals": "Microsoft.SQL/servers"
  },
  "then": {
    "effect": "auditIfNotExists",
    "details": {
      "type": "Microsoft.SQL/servers/auditingSettings",
      "name": "default",
      "existenceCondition": {
        "allOf": [
          {
            "field": "Microsoft.Sql/auditingSettings.state",
            "equals": "[parameters('setting')]"
          }
        ]
      }
    }
  }
}
```

Você pode implantar este modelo usando o [portal do Azure](#deploy-with-the-portal), com [PowerShell](#deploy-with-powershell) ou com o [CLI do Azure](#deploy-with-azure-cli). Para obter a política interna, use a ID `a6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9`.

## <a name="parameters"></a>parâmetros

Para passar o valor do parâmetro, use o seguinte formato:

```json
{"setting": {"value":"enabled"}}
```

## <a name="deploy-with-the-portal"></a>Implantar com o portal

Ao atribuir uma política, selecione **Auditar configuração de auditoria no nível do SQL Server** nas definições internas disponíveis.

## <a name="deploy-with-powershell"></a>Implantação com o PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/a6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9

New-AzureRmPolicyAssignment -name "SQL Audit audit" -PolicyDefinition $definition -PolicyParameter '{"setting": {"value":"enabled"}}' -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Limpar a implantação do PowerShell

Execute o seguinte comando para remover a atribuição de política.

```azurepowershell-interactive
Remove-AzureRmPolicyAssignment -Name "SQL Audit audit" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "SQL Audit audit" --policy a6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9 --params '{"setting": {"value":"enabled"}}'
```

### <a name="clean-up-azure-cli-deployment"></a>Limpar a implantação da CLI do Azure

Execute o seguinte comando para remover a atribuição de política.

```azurecli-interactive
az policy assignment delete --name "SQL Audit audit" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

- Examine mais exemplos nos [exemplos do Azure Policy](index.md)