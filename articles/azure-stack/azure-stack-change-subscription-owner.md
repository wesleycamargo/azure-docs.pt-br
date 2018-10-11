---
title: Atualizar o proprietário de assinatura de usuário do Azure Stack | Microsoft Docs
description: Altere o proprietário de cobrança para assinaturas de usuário do Azure STack.
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
ms.topic: get-started-article
ms.date: 06/12/2018
ms.author: sethm
ms.reviewer: shnatara
ms.openlocfilehash: a784f169bfdf23255b27d50f0e135384db6b2b88
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077622"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Alterar o proprietário para uma assinatura de usuário do Azure Stack

Operadores do Azure Stack podem usar o PowerShell para alterar o proprietário de cobrança de uma assinatura de usuário. Um motivo para alterar o proprietário é substituir um usuário que deixa a sua organização.   

Há dois tipos de *proprietários* que são atribuídos a uma assinatura:

- **Proprietário de cobrança** – por padrão, o proprietário de cobrança é a conta de usuário que obtém a assinatura de uma oferta e, em seguida, possui a relação de cobrança para essa assinatura. Essa conta também é um administrador da assinatura.  Somente uma conta de usuário pode ter essa designação em uma assinatura. Um proprietário de cobrança geralmente é um líder de equipe ou organização. 

  Use o cmdlet PowerShell do **AzsUserSubscription conjunto** para alterar o proprietário de cobrança.  

- **Proprietários adicionados por meio de funções RBAC** – usuários adicionais podem ser concedidos a função de proprietário usando a [controle de acesso baseado em função](azure-stack-manage-permissions.md) system (RBAC).  Qualquer número de contas de usuário adicionais pode ser adicionado como proprietários para complementar o proprietário de cobrança. Proprietários adicionais também são administradores da assinatura e tem todos os privilégios para a assinatura exceto as permissões para excluir o proprietário de cobrança. 

  Você pode usar o PowerShell para gerenciar proprietários adicionais, consulte [o Azure PowerShell para gerenciar o controle de acesso baseado em função]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="change-the-billing-owner"></a>Alterar o proprietário de cobrança
Execute o seguinte script para alterar o proprietário de cobrança de uma assinatura de usuário.  O computador que você usa para executar o script deve se conectar ao Azure Stack e executar o módulo do Azure Stack PowerShell 1.3.0 ou posterior. Para obter mais informações, consulte [instalar o Azure Stack PowerShell](azure-stack-powershell-install.md). 

> [!Note]  
>  Em uma pilha do Azure multilocatário, o novo proprietário deve ser no mesmo diretório que o proprietário existente. Antes de poder fornecer a propriedade da assinatura a um usuário que está em outro diretório, você deve primeiro [convidar esse usuário como um convidado em seu diretório](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator). 


Substitua os seguintes valores no script antes da execução: 
 
- **$ArmEndpoint** – especifique o ponto de extremidade do Gerenciador de recursos para o seu ambiente.  
- **$TenantId** -especificar sua ID de locatário. 
- **$SubscriptionId** – especificar sua ID de assinatura.
- **$OwnerUpn** -especifique uma conta como *user@example.com* adicionar como o novo proprietário de cobrança.  


```PowerSHell   
# Setup Azure Stack Admin Environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select Admin Subscriptionr
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context the Default Provider Subscription: $providerSubscriptionId" 
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change User Subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```


## <a name="next-steps"></a>Próximas etapas
[Gerenciar controle de acesso baseado em função](azure-stack-manage-permissions.md)
