---
title: "Remover uma atribuição de usuário ou grupo de um aplicativo empresarial no Azure Active Directory | Microsoft Docs"
description: "Como remover a atribuição de acesso de um usuário ou grupo de um aplicativo empresarial no Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 7b2d365b-ae92-477f-9702-353cc6acc5ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: markvi
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 084ffcbe473290a8734b1c8b8847b517dac4f6b6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Remover a atribuição de um usuário ou grupo de um aplicativo empresarial no Azure Active Directory
É fácil impedir que um usuário ou grupo receba acesso a um de seus aplicativos empresariais no Azure AD (Azure Active Directory). Você deve ter as permissões apropriadas para gerenciar o aplicativo empresarial, além de ser um administrador global do diretório.

> [!NOTE]
> Para aplicativos da Microsoft (como aplicativos do Office 365), use o PowerShell para remover usuários em um aplicativo empresarial.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Como fazer para remover uma atribuição de usuário ou grupo em um aplicativo empresarial no Portal do Azure?
1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Escolha **Mais serviços**, insira **Azure Active Directory** na caixa de texto e selecione **Enter**.
3. Na página **Azure Active Directory - *nomedodiretório*** (ou seja, a página do Azure Active Directory para o diretório que você está gerenciando), selecione **Aplicativos empresariais**.

    ![Abrir aplicativos empresariais](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)
4. Na página **Aplicativos empresariais**, selecione **Todos os aplicativos**. Você verá uma lista dos aplicativos que pode gerenciar.
5. Na página **Aplicativos empresariais – Todos os aplicativos**, selecione um aplicativo.
6. Na página ***nomedoaplicativo*** (ou seja, a página com o nome do aplicativo selecionado no título), selecione **Usuários e Grupos**.

    ![Seleção de usuários ou de grupos](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)
7. Na página ***nomedoaplicativo*** **- Atribuição de Usuário e Grupo**, selecione um dos usuários ou grupos e, em seguida, selecione o comando **Remover**. Confirme sua decisão na solicitação.

    ![Seleção do comando Remover](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Como fazer para remover uma atribuição de usuário ou grupo em um aplicativo empresarial usando o PowerShell?
1. Abra um prompt elevado do Windows PowerShell.

    >[!NOTE] 
    > Você precisa instalar o módulo do Azure AD (use o comando `Install-Module -Name AzureAD`). Se solicitado a instalar um módulo do NuGet ou o novo módulo do Azure Active Directory V2 PowerShell, digite Y e pressione ENTER.

2. Execute `Connect-AzureAD` e entre com uma conta de usuário de Administrador Global.
3. Use o script abaixo para atribuir um usuário e uma função a um aplicativo:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ``` 
## <a name="next-steps"></a>Próximas etapas

- [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
- [Atribuir um usuário ou um grupo a um aplicativo empresarial](active-directory-coreapps-assign-user-azure-portal.md)
- [Desabilitar as entradas de usuário em um aplicativo empresarial](active-directory-coreapps-disable-app-azure-portal.md)
- [Alterar o nome ou logotipo de um aplicativo empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)
