---
title: Como exibir a entidade de serviço de uma identidade gerenciada usando o PowerShell
description: Instruções passo a passo para exibir a entidade de serviço de uma identidade gerenciada usando o PowerShell.
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
ms.date: 11/29/2018
ms.author: daveba
ms.openlocfilehash: 0e8558d96803e15da355f083c6d1988d5ee6cb68
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52884311"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Exibir a entidade de serviço de uma identidade gerenciada usando o PowerShell

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprende a exibir a entidade de serviço de uma identidade gerenciada usando o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md).
- Caso ainda não tenha uma conta do Azure, [inscreva-se para obter uma conta gratuita](https://azure.microsoft.com/free/).
- [Habilitar a identidade atribuída pelo sistema em sua máquina virtual](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) ou [aplicativo](/azure/app-service/app-service-managed-service-identity#adding-a-system-assigned-identity).
- Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.7.0 ou posterior. Execute ` Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). 
- Se estiver executando o PowerShell localmente, também será possível: 
    - Execute `Login-AzureRmAccount` para criar uma conexão com o Azure.
    - Instale a [versão mais recente do PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Execute `Install-Module -Name PowerShellGet -AllowPrerelease` para obter a versão de pré-lançamento do módulo `PowerShellGet` (talvez você precise fazer `Exit` da sessão atual do PowerShell depois de executar este comando para instalar o módulo `AzureRM.ManagedServiceIdentity`).
    - Execute `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease` para instalar a versão de pré-lançamento do módulo `AzureRM.ManagedServiceIdentity` para executar as operações de identidade gerenciada atribuídas ao usuário neste artigo.

## <a name="view-the-service-principal"></a>Exibir a entidade de serviço

O comando a seguir demonstra como exibir a entidade de serviço de um aplicativo ou VM com identidade atribuída pelo sistema habilitada. Substitua `<VM or application name>` pelos seus próprios valores.

```PowerShell
Get-AzureRmADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como exibir as entidades de serviço do Azure AD usando o PowerShell, consulte [Get-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal).


