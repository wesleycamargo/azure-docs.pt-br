---
title: Criar programaticamente assinaturas do Azure Enterprise | Microsoft Docs
description: Aprenda a criar programaticamente assinaturas adicionais do Azure Enterprise ou Enterprise Dev/Test.
services: azure-resource-manager
author: jlian
manager: jlian
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/30/2018
ms.author: jlian
ms.openlocfilehash: f55f878d53b3813ea2ff2510998d47820de76a6a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Criar programaticamente assinaturas do Azure Enterprise (versão prévia)

Como um cliente do Azure em [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), você pode criar EA (MS-AZR-0017P) e assinaturas de desenvolvimento/teste de EA (MS-AZR-0148P) programaticamente. Para conceder a permissão para criar assinaturas cobradas à sua conta a outro usuário ou entidade de serviço, conceda a eles acesso [Controle de Acesso Baseado em Função (RBAC)](../active-directory/role-based-access-control-configure.md) à sua conta do registro. 

> [!IMPORTANT]
> As assinaturas do Azure criadas por meio dessa API são administradas pelo contrato sob a qual você obteve serviços do Microsoft Azure da Microsoft ou de um revendedor autorizado. Para saber mais, confira [Informações Legais do Microsoft Azure](https://azure.microsoft.com/support/legal/).

Neste artigo, você irá:

> [!div class="checklist"]
> * Aprender a criar assinaturas programaticamente usando o Azure Resource Manager
> * Entender como usar o RBAC para compartilhar a capacidade de criar assinaturas cobradas para sua conta EA

Também, consulte o [exemplo de código .NET no GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).

## <a name="ask-your-ea-enrollment-admin-to-add-you-as-account-owner"></a>Peça ao seu administrador de registro de EA para adicioná-lo como proprietário da conta

Para começar, peça ao seu administrador de registro para [adicioná-lo como um proprietário de conta usando o portal EA](https://ea.azure.com/helpdocs/addNewAccount) (log de entrada necessário). Siga as instruções no email de convite que você recebe para criar manualmente uma assinatura inicial.

> [!IMPORTANT]
> Você deve confirmar a propriedade de conta e criar manualmente uma assinatura de EA inicial antes de prosseguir para a próxima etapa. Apenas adicionar a conta para o registro não é suficiente.

## <a name="find-accounts-you-have-access-to"></a>Localize as contas às quais você tem acesso

Depois que você for adicionado a um registro de EA do Azure como proprietário da conta, o Azure usa a relação de registro de conta para determinar onde cobrar os encargos de assinatura. Para criar assinaturas, primeiro saiba quais contas de registro que você tem acesso. Se você atualmente é um proprietário de conta de EA e você tentar usar essa API, o Azure verifica as seguintes condições:

- Sua conta foi adicionada a um registro de EA
- Você tem uma ou mais assinaturas EA ou EA desenvolvimento/teste, o que significa que você tenha passado pela inscrição manual pelo menos uma vez
- Você está conectado ao *diretório base* do proprietário da conta, que é o diretório em que as assinaturas são criadas por padrão

Se as três condições acima forem atendidas, um `enrollmentAccount` recurso é retornado e você pode iniciar a criação de assinaturas sob esta conta. Todas as assinaturas criadas sob a conta serão cobradas na direção que a conta está no registro EA.

# <a name="resttabrest"></a>[REST](#tab/rest)

Solicitação para listar todas as contas de registro:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure responde com uma lista de todas as contas de registro a que você tem acesso:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Use o [comando Get-AzureRmEnrollmentAccount](/powershell/module/azurerm.billing/get-azurermenrollmentaccount) para listar todas as contas de registro às quais você tem acesso.

```azurepowershell-interactive
Get-AzureRmEnrollmentAccount
```

O Azure responde com uma lista de IDs de Objetos e endereços de e-mails de contas.

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Use o comando [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) para listar todas as contas de registro às quais você tem acesso.

```azurecli-interactive 
az billing enrollment-account list
```
O Azure responde com uma lista de IDs de Objetos e endereços de e-mails de contas.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

---

Use a `principalName` propriedade para identificar a conta na qual você deseja que as assinaturas sejam cobradas. Use o `id` como o valor `enrollmentAccount` que você usa para criar a assinatura na próxima etapa.

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Crie assinaturas em uma conta de registro específico 

O exemplo a seguir cria uma solicitação para criar a assinatura chamada *assinatura da equipe de desenvolvimento* e a oferta de assinatura é *MS-AZR-0017P* (regular EA). A conta de registro é `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (valor de placeholder, este é um GUID), que é a conta de registro para SignUpEngineering@contoso.com. Ele também adiciona dois usuários como Proprietários RBAC para a assinatura.

# <a name="resttabrest"></a>[REST](#tab/rest)

Use o `id` do `enrollmentAccount` no caminho da solicitação para criar a assinatura.

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Nome do elemento  | Obrigatório | type   | DESCRIÇÃO                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Não       | Cadeia de caracteres | O nome de exibição da assinatura. Se não for especificado, ele é definido como o nome da oferta, como "Microsoft Azure Enterprise."                                 |
| `offerType`   | sim      | Cadeia de caracteres | A oferta da assinatura. As duas opções para EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desenvolvimento/teste, precisa ser [ativado usando o portal EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Não        | Cadeia de caracteres | A ID de objeto de qualquer usuário que você deseja adicionar como um proprietário de RBAC na assinatura quando ela é criada.  |

Em resposta, você obtém um objeto `subscriptionOperation` para monitoramento. Quando terminar a criação de assinatura, o objeto `subscriptionOperation` retornaria um objeto `subscriptionLink`, que tem a ID da assinatura.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Para usar este módulo de visualização, instale-o executando `Install-Module AzureRM.Subscription -AllowPrerelease` primeiro. Para certificar-se de que `-AllowPrerelease` funciona, instale uma versão recente do PowerShellGet do [Módulo Get do PowerShellGet](/powershell/gallery/psget/get_psget_module).

Use o [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview) juntamente com a `enrollmentAccount` ID do bojeto como o parâmetro `EnrollmentAccountObjectId` para criar uma nova assinatura. 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| Nome do elemento  | Obrigatório | type   | DESCRIÇÃO                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Não       | Cadeia de caracteres | O nome de exibição da assinatura. Se não for especificado, ele é definido como o nome da oferta, como "Microsoft Azure Enterprise."                                 |
| `OfferType`   | sim      | Cadeia de caracteres | A oferta da assinatura. As duas opções para EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desenvolvimento/teste, precisa ser [ativado usando o portal EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | sim       | Cadeia de caracteres | A ID de Objeto da conta do registro que a assinatura é criada e cobrada. Este é um valor de GUID que você obteve de `Get-AzureRmEnrollmentAccount`. |
| `OwnerObjectId`      | Não        | Cadeia de caracteres | A ID de objeto de qualquer usuário que você deseja adicionar como um proprietário de RBAC na assinatura quando ela é criada.  |
| `OwnerSignInName`    | Não        | Cadeia de caracteres | O endereço de e-mail de qualquer usuário que você gostaria de adicionar como um Proprietário RBAC na assinatura quando ela é criada. Você pode usar este parâmetro em vez de `OwnerObjectId`.|
| `OwnerApplicationId` | Não        | Cadeia de caracteres | O ID do aplicativo de qualquer serviço principal que você gostaria de adicionar como um Proprietário RBAC na assinatura quando ela é criada. Você pode usar este parâmetro, em vez de `OwnerObjectId`.| 

Para ver uma lista completa de todos os parâmetros, consulte [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para usar esta extensão de visualização, instale-a executando `az extension add --name subscription` primeiro.

Use o [criar conta az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) juntamente com a ID do objeto `enrollmentAccount` como o parâmetro `enrollment-account-object-id` para criar uma nova assinatura.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nome do elemento  | Obrigatório | type   | DESCRIÇÃO                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Não       | Cadeia de caracteres | O nome de exibição da assinatura. Se não for especificado, ele é definido como o nome da oferta, como "Microsoft Azure Enterprise."                                 |
| `offer-type`   | sim      | Cadeia de caracteres | A oferta da assinatura. As duas opções para EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desenvolvimento/teste, precisa ser [ativado usando o portal EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | sim       | Cadeia de caracteres | A ID de Objeto da conta do registro que a assinatura é criada e cobrada. Este é um valor de GUID que você obteve de `az billing enrollment-account list`. |
| `owner-object-id`      | Não        | Cadeia de caracteres | A ID de objeto de qualquer usuário que você deseja adicionar como um proprietário de RBAC na assinatura quando ela é criada.  |
| `owner-upn`    | Não        | Cadeia de caracteres | O endereço de e-mail de qualquer usuário que você gostaria de adicionar como um Proprietário RBAC na assinatura quando ela é criada. Você pode usar este parâmetro em vez de `owner-object-id`.|
| `owner-spn` | Não        | Cadeia de caracteres | O ID do aplicativo de qualquer serviço principal que você gostaria de adicionar como um Proprietário RBAC na assinatura quando ela é criada. Você pode usar este parâmetro em vez de `owner-object-id`.| 

Para ver uma lista completa de todos os parâmetros, consulte [criar conta az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Delegue acesso a uma conta de registro usando o RBAC

Para conceder a outro usuário ou entidade de serviço a capacidade de criar assinaturas em uma conta específica, [dê a eles uma função de Proprietário de RBAC no escopo da conta do registro](../active-directory/role-based-access-control-manage-access-rest.md). O exemplo a seguir fornece um usuário no locatário com `principalId` de `<userObjectId>` (para SignUpEngineering@contoso.com) uma função de Proprietário da conta do registro. 

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

Use o [New-AzureRmRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) para dar acesso de Proprietário de outro usuário na sua conta do registro.

```azurepowershell-interactive
New-AzureRmRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
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

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitações da API de criação de assinatura do Azure Enterprise

- Somente as assinaturas do Azure Enterprise podem ser criadas usando esta API.
- Há um limite de 50 assinaturas por conta. Depois disso, as assinaturas só podem ser criadas usando o Centro de Contas.
- Precisa ter pelo menos um EA ou EA desenvolvimento/teste assinaturas sob a conta, o que significa que o Proprietário da Conta tenha se inscrito manualmente pelo menos uma vez.
- Os usuários que não são Proprietários da Conta, mas foram adicionados a uma conta de registro por meio do RBAC, não podem criar assinaturas usando o Centro de Contas.
- Você não pode selecionar o locatário para a assinatura a ser criada. A assinatura é sempre criada no locatário inicial do Proprietário da Conta. Para mover a assinatura para um locatário diferente, consulte [alterar o locatário da assinatura](..\active-directory\active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Próximas etapas

* Para obter um exemplo sobre como criar assinaturas usando .NET, consulte [código de exemplo no GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Para saber mais sobre o Azure Resource Manager, confira [Visão geral do Azure Resource Manager](resource-group-overview.md).
* Para saber mais sobre grupos grandes de gerenciamento de assinaturas usando Grupos de Gerenciamento, consulte [Organize seus recursos com Grupos de Gerenciamento do Azure](management-groups-overview.md)
* Para ver uma abrangente diretriz de práticas recomendadas para grandes organizações no controle de assinatura, consulte [scaffold enterprise do Azure - governança prescritivas de assinatura](resource-manager-subscription-governance.md)
