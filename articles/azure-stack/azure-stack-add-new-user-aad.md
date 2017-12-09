---
title: "Adicionar uma nova conta de locatário do Azure pilha no Active Directory do Azure | Microsoft Docs"
description: "Depois de implantar o Kit de desenvolvimento de pilha do Microsoft Azure, você precisará criar conta de usuário de pelo menos um locatário, por isso você pode explorar o portal de locatário."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: b7fd3c36825746a009c01c97fb8664e04278159f
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2017
---
*Aplica-se a: Kit de desenvolvimento de pilha do Azure*

# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Adicionar uma nova conta de locatário do Azure Stack no Active Directory do Azure
Depois de [Implantando o Kit de desenvolvimento do Azure pilha](azure-stack-run-powershell-script.md), você precisará de uma conta de usuário do locatário para que você possa explorar o portal de locatário e suas ofertas e planos de teste. Você pode criar uma conta de locatário por [usando o portal do Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) ou [usando o PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Criar uma conta de locatário do Azure Stack usando o portal do Azure
É necessário ter uma assinatura do Azure para usar o portal do Azure.

1. Faça logon no [Azure](https://portal.azure.com).
2. Na barra de navegação à esquerda do Microsoft Azure, clique em **Active Directory**.
3. Na lista de diretórios, clique no diretório que deseja usar para o Azure Stack ou crie um novo.
4. Nesta página de diretório, clique em **Usuários**.
5. Clique em **Adicionar usuário**.
6. No **adicionar usuário** no Assistente de **tipo de usuário** , escolha **novo usuário na sua organização**.
7. Na caixa **Nome de usuário** , digite um nome para o usuário.
8. No assistente **@** , escolha a entrada apropriada.
9. Clique na seta avançar.
10. No **perfil de usuário** página do assistente, digite um **nome**, **Sobrenome**, e **nome de exibição**.
11. No **função** , escolha **usuário**.
12. Clique na seta avançar.
13. Sobre o **obter senha temporária** , clique em **criar**.
14. Copie a **Nova senha**.
15. Faça logon no Microsoft Azure com a nova conta. Altere a senha, quando solicitado.
16. Faça logon no `https://portal.local.azurestack.external` com a nova conta para ver o portal de locatário.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Criar uma conta de locatário do Azure Stack usando o PowerShell
Se você não tiver uma assinatura do Azure, você não pode usar o portal do Azure para adicionar uma conta de usuário do locatário. Nesse caso, você pode usar o Azure módulo Active Directory para Windows PowerShell em vez disso.

> [!NOTE]
> Se você estiver usando Account da Microsoft (Live ID) para implantar o Kit de desenvolvimento de pilha do Azure, você não pode usar o PowerShell do AAD para criar a conta de locatário. 
> 
> 

1. Instalar o [Assistente de conexão para profissionais de TI RTW do Microsoft Online Services](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Instalar o [Azure módulo Active Directory para Windows PowerShell (versão de 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297) e abri-lo.
3. Execute os seguintes cmdlets:

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Entre Microsoft Azure com a nova conta. Altere a senha, quando solicitado.
2. Entrar para `https://portal.local.azurestack.external` com a nova conta para ver o portal de locatário.

