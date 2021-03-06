---
title: Adicionar uma nova conta de locatário do Azure Stack no Active Directory do Azure | Microsoft Docs
description: Depois de implantar o Kit de desenvolvimento do Microsoft Azure Stack, você precisará criar a conta de usuário de pelo menos um locatário para que você possa explorar o portal de locatário.
services: azure-stack
documentationcenter: ''
author: patricka
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: d7dc690f719dd9265317b2bba3b87521e9a00d6b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58106569"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Adicionar uma nova conta de locatário do Azure Stack no Active Directory do Azure

Após [Implantando o Kit de desenvolvimento do Azure Stack](azure-stack-run-powershell-script.md), será necessário uma conta de usuário do locatário para que você possa explorar o portal de locatário e suas ofertas e planos de teste. Você pode criar uma conta de locatário [usando o portal do Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) ou por meio do PowerShell.

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Criar uma conta de locatário do Azure Stack usando o portal do Azure

É necessário ter uma assinatura do Azure para usar o portal do Azure.

1. Entrar no [Azure](https://portal.azure.com).
2. Na barra de navegação à esquerda, selecione **do Active Directory** e alternar para o diretório que você deseja usar para o Azure Stack ou crie um novo.
3. Selecione **Azure Active Directory** > **usuários** > **novo usuário**.

    ![Usuários - página Todos os usuários com o novo usuário destacado](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. Na página **Usuário**, preencha as informações necessárias.

    ![Adicionar novo usuário, página do usuário com informações do usuário](media/azure-stack-add-new-user-aad/new-user-user.png)

   - **Nome (obrigatório).** O primeiro e último nome do novo usuário. Por exemplo, Mary Parker.
   - **Nome de usuário (obrigatório).** O nome de usuário do novo usuário. Por exemplo, mary@contoso.com.
       A parte do domínio do nome de usuário deve usar o nome de domínio padrão inicial, <_yourdomainname_>. Onmicrosoft.com ou um nome de domínio personalizado, como contoso.com. Para obter mais informações sobre como criar um nome de domínio personalizado, consulte [Como adicionar um nome de domínio personalizado ao Active Directory do Azure](../active-directory/fundamentals/add-custom-domain.md).
   - **Perfil.** Opcionalmente, você pode adicionar mais informações sobre o usuário. Você também pode adicionar informações do usuário posteriormente. Para obter mais informações sobre como adicionar informações do usuário, consulte [Como adicionar ou alterar as informações do perfil do usuário](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md).
   - **Função de diretório.**  escolher **usuário**.

5. Verifique **Mostrar senha** e copie a senha gerada automaticamente fornecida na **senha** caixa. Você precisará dessa senha para o processo de logon inicial.

6. Selecione **Criar**.

    O usuário é criado e adicionado ao seu locatário do Azure AD.

7. Entre no portal do Microsoft Azure com a nova conta. Altere a senha, quando solicitado.
8. Entrar para `https://portal.local.azurestack.external` com a nova conta para ver o portal de locatário.

## <a name="create-an-azure-stack-user-account-using-powershell"></a>Criar uma conta de usuário do Azure Stack usando o PowerShell

Se você não tiver uma assinatura do Azure, você não pode usar o portal do Azure para adicionar uma conta de usuário do locatário. Nesse caso, você pode usar o módulo Azure Active Directory para Windows PowerShell em vez disso.

> [!NOTE]
> Se você estiver usando Account da Microsoft (Live ID) para implantar o Kit de desenvolvimento do Azure Stack, você não pode usar o PowerShell do AAD para criar a conta de locatário. 

1. Instale o [Assistente de Conexão do Microsoft Online Services para Profissionais de TI RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Instalar o [módulo Azure Active Directory para Windows PowerShell (versão de 64 bits)](https://go.microsoft.com/fwlink/p/?linkid=236297) e abri-lo.
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

## <a name="next-steps"></a>Próximas etapas

[Adicionar usuários do Azure Stack no AD FS](azure-stack-add-users-adfs.md)
