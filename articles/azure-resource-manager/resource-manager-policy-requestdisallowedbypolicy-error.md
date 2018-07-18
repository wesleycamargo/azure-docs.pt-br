---
title: Erro RequestDisallowedByPolicy com a política de recurso do Azure | Microsoft Docs
description: Descreve a causa do erro RequestDisallowedByPolicy.
services: azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: genli
ms.openlocfilehash: 474400d92660b68fd7fef906216b8e37c6e8c94d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Erro RequestDisallowedByPolicy com a política de recurso do Azure

Este artigo descreve a causa do erro RequestDisallowedByPolicy e também fornece a solução para esse erro.

## <a name="symptom"></a>Sintoma

Durante a implantação, você pode receber um erro **RequestDisallowedByPolicy**, que impede a criação dos recursos. O exemplo a seguir mostra o erro:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>solução de problemas

Para recuperar detalhes sobre a política que bloqueou a implantação, use um dos seguintes métodos:

### <a name="powershell"></a>PowerShell

No PowerShell, forneça o identificador de política como o parâmetro `Id` para recuperar detalhes sobre a política que bloqueou sua implantação.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>CLI do Azure

Na CLI 2.0 do Azure, forneça o nome da definição de política:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Solução

Para segurança ou conformidade, os administradores de sua assinatura podem atribuir políticas que limitam como os recursos são implantados. Por exemplo, sua assinatura pode ter uma política que impede a criação de endereços IP Públicos, Grupos de Segurança de Rede, Rodas Definidas pelo Usuário ou tabelas de rota. A mensagem de erro na seção **Sintomas** mostra o nome da política.
Para resolver esse problema, examine as políticas de recursos e determine como implantar recursos que estão em conformidade com essas políticas.

Para obter mais informações, consulte os seguintes artigos:

- [O que é o Azure Policy?](../azure-policy/azure-policy-introduction.md)
- [Criar e gerenciar políticas para impor a conformidade](../azure-policy/create-manage-policy.md)
