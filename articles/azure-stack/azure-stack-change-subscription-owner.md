---
title: Atualizar o proprietário de assinatura de usuário do Azure Stack | Microsoft Docs
description: Altere o proprietário de cobrança para assinaturas de usuário do Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: shnatara
ms.lastreviewed: 10/19/2018
ms.openlocfilehash: 02c5d063246844d5ed8945aba38aaada87691627
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763190"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Alterar o proprietário para uma assinatura de usuário do Azure Stack

Operadores do Azure Stack podem usar o PowerShell para alterar o proprietário de cobrança de uma assinatura de usuário. Por exemplo, um motivo para alterar o proprietário é substituir um usuário que deixa a sua organização.

Há dois tipos de *proprietários* que são atribuídos a uma assinatura:

- **Proprietário de cobrança**: Por padrão, o proprietário de cobrança é a conta de usuário que obtém a assinatura de uma oferta e, em seguida, possui a relação de cobrança para essa assinatura. Essa conta também é um administrador da assinatura. Somente uma conta de usuário pode ter essa designação em uma assinatura. Um proprietário de cobrança geralmente é um líder de equipe ou organização.

  Você pode usar o cmdlet do PowerShell [AzsUserSubscription conjunto](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) para alterar o proprietário de cobrança.  

- **Proprietários adicionados por meio de funções RBAC** – usuários adicionais podem ser concedidos a **proprietário** função usando o [controle de acesso baseado em função](azure-stack-manage-permissions.md) system (RBAC). Qualquer número de contas de usuário adicionais pode ser adicionado como proprietários para complementar o proprietário de cobrança. Proprietários adicionais também são administradores da assinatura e tem todos os privilégios para a assinatura, exceto a permissão para excluir o proprietário de cobrança.

  Você pode usar o PowerShell para gerenciar outros proprietários. Para obter mais informações, consulte [este artigo](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>Alterar o proprietário de cobrança

Execute o seguinte script para alterar o proprietário de cobrança de uma assinatura de usuário. O computador que você usa para executar o script deve se conectar ao Azure Stack e executar o módulo do Azure Stack PowerShell 1.3.0 ou posterior. Para obter mais informações, consulte [instalar o Azure Stack PowerShell](azure-stack-powershell-install.md).

>[!NOTE]
>Em um multilocatário do Azure Stack, o novo proprietário deve ser no mesmo diretório que o proprietário existente. Antes de poder fornecer a propriedade da assinatura a um usuário que está em outro diretório, você deve primeiro [convidar esse usuário como um convidado em seu diretório](../active-directory/b2b/add-users-administrator.md).

Substitua os seguintes valores no script antes da execução:

- **$ArmEndpoint**: O ponto de extremidade do Gerenciador de recursos para o seu ambiente.
- **$TenantId**: ID do locatário.
- **$SubscriptionId**: A ID de sua assinatura.
- **$OwnerUpn**: Uma conta, por exemplo **user@example.com**, para adicionar como o novo proprietário de cobrança.

```powershell
# Set up Azure Stack admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar controle de acesso baseado em função](azure-stack-manage-permissions.md)
