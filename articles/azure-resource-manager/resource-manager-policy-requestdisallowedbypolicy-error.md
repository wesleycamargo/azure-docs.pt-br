---
title: "Erro RequestDisallowedByPolicy com a política de recurso do Azure | Microsoft Docs"
description: Descreve a causa do erro RequestDisallowedByPolicy.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 182a27e444c2f5db66d518a1a0c608d3e319d553
ms.contentlocale: pt-br
ms.lasthandoff: 07/13/2017

---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Erro RequestDisallowedByPolicy com a política de recurso do Azure

Este artigo descreve a causa do erro RequestDisallowedByPolicy e também fornece a solução para esse erro.

## <a name="symptom"></a>Sintoma

Quando você tenta executar uma ação durante a implantação, poderá receber um erro **RequestDisallowedByPolicy** que impede que a ação seja executada. Esta é uma amostra do erro:

```
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Solucionar problemas

Para recuperar detalhes sobre a política que bloqueou a implantação, use um dos seguintes métodos:

### <a name="method-1"></a>Método 1

No PowerShell, forneça o identificador de política como o parâmetro **Id** para recuperar detalhes sobre a política que bloqueou a implantação.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>Método 2 

Na CLI 2.0 do Azure, forneça o nome da definição de política: 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Solução

Para segurança ou conformidade, seu departamento de TI pode impor uma política de recurso que proíbe a criação de Endereços IP públicos, Grupos de Segurança de Rede, Rotas Definidas pelo Usuário ou tabelas de rotas. Na amostra da mensagem de erro descrita na seção “Sintomas”, a política é chamada **regionPolicyDefinition**, mas pode ser diferente.
Para resolver esse problema, trabalhe com o departamento de TI para examinar as políticas de recurso e determinar como executar a ação solicitada em conformidade com essas políticas.


Para obter mais informações, consulte os seguintes artigos:

- [Visão geral da política de recurso](resource-manager-policy.md)
- [Erros comuns de implantação – RequestDisallowedByPolicy](resource-manager-common-deployment-errors.md#requestdisallowedbypolicy)

 



