---
title: Conceder acesso para criar assinaturas do Azure Enterprise | Microsoft Docs
description: Aprenda a conferir a um usuário ou entidade de serviço a capacidade de criar programaticamente assinaturas do Azure Enterprise.
services: azure-resource-manager
author: adpick
manager: adpick
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: adpick
ms.openlocfilehash: 3577edff19788ed9f0925876e3de737eb749b90e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490916"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Conceder acesso para criar assinaturas do Azure Enterprise (versão prévia)

Como cliente do Azure no [EA (Enterprise Agreement)](https://azure.microsoft.com/pricing/enterprise-agreement/), você pode conceder a outro usuário ou entidade de serviço permissão para criar assinaturas cobradas de sua conta. Neste artigo, você aprenderá a usar o [RBAC (Controle de Acesso Baseado em Função)](../active-directory/role-based-access-control-configure.md) para compartilhar a capacidade de criar assinaturas, e aprenderá a fazer auditoria das criações de assinatura. É necessário ter a função Proprietário na conta que deseja compartilhar.

Para criar programaticamente uma assinatura, confira [Criar programaticamente assinaturas do Azure Enterprise (versão prévia)](programmatically-create-subscription.md).

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Delegue acesso a uma conta de registro usando o RBAC

Para conceder a outro usuário ou entidade de serviço a capacidade de criar assinaturas em uma conta específica, [dê a eles uma função de Proprietário de RBAC no escopo da conta do registro](../active-directory/role-based-access-control-manage-access-rest.md). O exemplo a seguir fornece um usuário no locatário com `principalId` de `<userObjectId>` (para SignUpEngineering@contoso.com) uma função de Proprietário da conta do registro. Para localizar a ID da conta de registro e a ID da entidade, confira [Criar programaticamente assinaturas do Azure Enterprise (versão prévia)](programmatically-create-subscription.md).

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>"
  }
}
```

Quando a função de Proprietário é atribuída com êxito no escopo da conta do registro, o Azure responde com informações sobre a atribuição de função:

```json
{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>",
    "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "createdOn": "2018-03-05T08:36:26.4014813Z",
    "updatedOn": "2018-03-05T08:36:26.4014813Z",
    "createdBy": "<assignerObjectId>",
    "updatedBy": "<assignerObjectId>"
  },
  "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "<roleAssignmentGuid>"
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Use [New-AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) para dar a outro usuário acesso de Proprietário na sua conta do registro.

```azurepowershell-interactive
New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o [criar atribuição de função az](../active-directory/role-based-access-control-manage-access-azure-cli.md) para dar acesso de Proprietário de outro usuário na sua conta do registro.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

Depois que um usuário se torna um Proprietário de RBAC para sua conta de inscrição, eles podem criar programaticamente assinaturas sob ela. Uma assinatura criada por um usuário delegado ainda tem o Proprietário da Conta original como Administrador de Serviço, mas ele também tem o usuário delegado como um Proprietário por padrão. 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Audite quem criou assinaturas usando logs de atividade

Para controlar as assinaturas criadas por meio desta API, use o [API de Locatário do Log de Atividade](/rest/api/monitor/tenantactivitylogs). No momento não é possível usar o PowerShell, CLI ou o portal do Azure para controlar a criação da assinatura.

1. Como um administrador de locatários do locatário do AD do Azure, [eleve o acesso](../active-directory/role-based-access-control-tenant-admin-access.md), em seguida, atribua uma função de leitor para o usuário de auditoria sobre o escopo `/providers/microsoft.insights/eventtypes/management`.
1. Como o usuário de auditoria, chame o [API de Locatário do Log de Atividade](/rest/api/monitor/tenantactivitylogs) para ver as atividades de criação de assinatura. Exemplo:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> Para chamar convenientemente esta API da linha de comando, tente [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Próximas etapas

* Agora que o usuário ou entidade de serviço tem permissão para criar uma assinatura, use essa identidade para [criar programaticamente as assinaturas do Azure Enterprise](programmatically-create-subscription.md).
* Para obter um exemplo sobre como criar assinaturas usando .NET, consulte [código de exemplo no GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Para saber mais sobre o Azure Resource Manager, confira [Visão geral do Azure Resource Manager](resource-group-overview.md).
* Para saber mais sobre como gerenciar grandes números de assinaturas usando grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](management-groups-overview.md)
* Para ver uma abrangente diretriz de práticas recomendadas para grandes organizações no controle de assinatura, consulte [scaffold enterprise do Azure - governança prescritivas de assinatura](/azure/architecture/cloud-adoption-guide/subscription-governance)
