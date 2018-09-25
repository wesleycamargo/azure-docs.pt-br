---
title: Adicionar uma nova conta de locatário do Azure Stack no Active Directory do Azure | Microsoft Docs
description: Depois de implantar o Kit de desenvolvimento do Microsoft Azure Stack, você precisará criar a conta de usuário de pelo menos um locatário para que você possa explorar o portal de locatário.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 9a4d7200a2bc2445fcdfefc0332d67a045b5a2e1
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038010"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Adicionar uma nova conta de locatário do Azure Stack no Active Directory do Azure

Após [Implantando o Kit de desenvolvimento do Azure Stack](azure-stack-run-powershell-script.md), será necessário uma conta de usuário do locatário para que você possa explorar o portal de locatário e suas ofertas e planos de teste. Você pode criar uma conta de locatário [usando o portal do Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) ou pelo [usando o PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Criar uma conta de locatário do Azure Stack usando o portal do Azure

É necessário ter uma assinatura do Azure para usar o portal do Azure.

1. Entrar no [Azure](https://portal.azure.com).
2. Na barra de navegação à esquerda, selecione **do Active Directory** e alternar para o diretório que você deseja usar para o Azure Stack ou crie um novo.
3. Selecione **Azure Active Directory** > **usuários** > **novo usuário**.

    ![Usuários - página de todos os usuários com o novo usuário realçado](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. Sobre o **usuário** página, preencha as informações necessárias.

    ![Adicionar novo usuário, página de usuário com informações do usuário](media/azure-stack-add-new-user-aad/new-user-user.png)

    - **Nome (obrigatório).** O primeiro e último nome do novo usuário. Por exemplo, Mary Parker.
    - **Nome de usuário (obrigatório).** O nome de usuário do novo usuário. Por exemplo, mary@contoso.com.
        A parte do nome de usuário do domínio deve usar o padrão inicial nome de domínio, <_nome_do_domínio_>. onmicrosoft.com ou um nome de domínio personalizado, como contoso.com. Para obter mais informações sobre como criar um nome de domínio personalizado, consulte [como adicionar um nome de domínio personalizado ao Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md).
    - **Perfil.** Opcionalmente, você pode adicionar mais informações sobre o usuário. Você também pode adicionar informações do usuário em um momento posterior. Para obter mais informações sobre como adicionar informações do usuário, consulte [como adicionar ou alterar informações de perfil do usuário](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md).
    - **Função de diretório.**  escolher **usuário**.

5. Verifique **Mostrar senha** e copie a senha gerada automaticamente fornecida na **senha** caixa. Você precisará dessa senha para o processo de logon inicial.

6. Selecione **Criar**.

    O usuário é criado e adicionado ao seu locatário do Azure AD.

7. Entre no portal do Microsoft Azure com a nova conta. Altere a senha, quando solicitado.
8. Entrar para `https://portal.local.azurestack.external` com a nova conta para ver o portal de locatário.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Criar uma conta de locatário do Azure Stack usando o PowerShell

Se você não tiver uma assinatura do Azure, você não pode usar o portal do Azure para adicionar uma conta de usuário do locatário. Nesse caso, você pode usar o módulo Azure Active Directory para Windows PowerShell em vez disso.

> [!NOTE]
> Se você estiver usando Account da Microsoft (Live ID) para implantar o Kit de desenvolvimento do Azure Stack, você não pode usar o PowerShell do AAD para criar a conta de locatário. 
> 
> 

1. Instalar o [Assistente de conexão para os profissionais de TI RTW do Microsoft Online Services](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Instalar o [módulo Azure Active Directory para Windows PowerShell (versão de 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297) e abri-lo.
3. Execute os seguintes cmdlets:

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Entrar no Microsoft Azure com a nova conta. Altere a senha, quando solicitado.
2. Entrar para `https://portal.local.azurestack.external` com a nova conta para ver o portal de locatário.

