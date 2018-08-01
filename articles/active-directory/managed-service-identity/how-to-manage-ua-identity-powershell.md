---
title: Como criar, listar e excluir uma MSI atribuída pelo usuário usando o Azure PowerShell
description: Instruções passo a passo sobre como criar, listar e excluir uma Identidade de Serviço Gerenciada atribuída pelo usuário usando o Azure PowerShell
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: def5788b83116ce0843f1fdd86933830cabc9ee2
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187982"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-azure-powershell"></a>Criar, listar ou excluir uma identidade atribuída pelo usuário usando o Azure PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que seja compatível com a autenticação do Azure Active Directory sem precisar ter as credenciais no seu código. 

Neste artigo, você aprenderá a criar, listar e excluir uma identidade atribuída pelo usuário usando o Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a [seção de visão geral](overview.md). **Verifique se examinou a [diferença entre uma identidade atribuída pelo sistema e uma atribuída pelo usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [a versão mais recente do Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) se ainda não o fez.
- Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.7.0 ou posterior. Execute ` Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure.
- Para realizar as operações de gerenciamento deste artigo, sua conta precisará das seguintes atribuições de função:
    - [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) função para criar, ler (listar), atualizar e excluir uma identidade atribuída ao usuário.
    - Função de [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para ler (listar) as propriedades de uma identidade atribuída ao usuário.

## <a name="create-a-user-assigned-identity"></a>Criar uma identidade atribuída pelo usuário

Para criar uma identidade atribuída pelo usuário, use o comando [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity). O parâmetro `ResourceGroupName` especifica o grupo de recursos no qual a identidade atribuída pelo usuário é criada, e o parâmetro `-Name` especifica seu nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Listar identidades atribuídas pelo usuário

Para listar identidades atribuídas pelo usuário, use o comando [Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity).  O parâmetro `-ResourceGroupName` especifica o grupo de recursos onde a identidade atribuída pelo usuário foi criada. Substitua o `<RESOURCE GROUP>` por seu próprio valor:

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
Na resposta, identidades de usuário têm valor `"Microsoft.ManagedIdentity/userAssignedIdentities"` retornado para a chave, `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-identity"></a>Excluir uma identidade atribuída pelo usuário

Para excluir uma identidade atribuída do usuário, use o comando [Remove-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity).  O parâmetro `-ResourceGroupName` especifica o grupo de recursos onde a identidade definida pelo usuário foi criada e o parâmetro `-Name` especifica seu nome. Substitua os valores de parâmetros `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

 ```azurecli-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Excluir uma identidade atribuída pelo usuário não removerá a referência de nenhum recurso ao qual ela foi atribuída. Atribuições de identidade devem ser removidas separadamente.

## <a name="related-content"></a>Conteúdo relacionado

Para obter uma lista completa e mais detalhes sobre os comandos de MSI do Azure PowerShell, consulte [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity).


 
