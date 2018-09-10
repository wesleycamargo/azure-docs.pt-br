---
title: Atribuir um usuário ou grupo a um aplicativo empresarial no Azure Active Directory | Microsoft Docs
description: Como selecionar um aplicativo empresarial par atribuir um usuário ou um grupo a ele no Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: barbkess
ms.reviewer: luleon
ms.openlocfilehash: f23c9976dacc1ca696772d6bf02b5d59e3e0b4d5
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368684"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Atribuir um usuário ou um grupo a um aplicativo empresarial no Azure Active Directory
Para atribuir um usuário ou grupo a um aplicativo corporativo, você deverá ter as permissões apropriadas para gerenciar o aplicativo empresarial, além de ser um administrador global do diretório.

> [!NOTE]
> Os recursos discutidos neste artigo requerem uma licença do Azure Active Directory Premium P1 ou Premium P2. Para obter mais informações, confira a [Página de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

> [!NOTE]
> Para aplicativos da Microsoft (como aplicativos do Office 365), use o PowerShell para atribuir usuários a um aplicativo empresarial.


## <a name="how-do-i-assign-user-access-to-an-enterprise-app-in-the-azure-portal"></a>Como fazer para atribuir acesso de usuário a um aplicativo empresarial no portal do Azure?
1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **Todos os serviços**, insira Azure Active Directory na caixa de texto e, em seguida, selecione **Enter**.
3. Na folha **Azure Active Directory – *nomedodiretório*** (ou seja, a folha do Azure AD para o diretório que você está gerenciando), escolha **Aplicativos empresariais**.

    ![Abrir aplicativos empresariais](./media/assign-user-or-group-access-portal/open-enterprise-apps.png)
4. Na folha **Aplicativos empresariais**, escolha **Todos os aplicativos**. Lista os aplicativos que você pode gerenciar.
5. Na folha **Aplicativos empresariais – Todos os aplicativos** , escolha um aplicativo.
6. Na folha ***appname*** (ou seja, a folha com o nome do aplicativo selecionado no título), selecione **Usuários e Grupos**.

    ![Seleção do comando todos os aplicativos](./media/assign-user-or-group-access-portal/select-app-users.png)
7. Na folha ***appname*** **- Usuário e Atribuição de Grupos**, selecione o comando **Adicionar**.
8. Na folha **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![Atribuir um usuário ou um grupo ao aplicativo](./media/assign-user-or-group-access-portal/assign-users.png)
9. Na folha **Usuários e grupos**, selecione um ou mais usuários ou grupos na lista, em seguida, pressione o botão **Selecionar** na parte inferior da folha.
10. Na folha **Adicionar Atribuição**, selecione **Função**. Então, na folha **Selecionar Função**, escolha uma função para aplicar nos usuários ou grupos selecionados, em seguida, selecione o botão **OK** na parte inferior da folha.
11. Na folha **Adicionar Atribuição**, selecione o botão **Atribuir** na parte inferior da folha. Os usuários ou os grupos atribuídos têm as permissões definidas pela função selecionada para esse aplicativo empresarial.

## <a name="how-do-i-assign-a-user-to-an-enterprise-app-using-powershell"></a>Como atribuir um usuário a um aplicativo empresarial usando o PowerShell?

1. Abra um prompt elevado do Windows PowerShell.

    >[!NOTE] 
    > Você precisa instalar o módulo do Azure AD (use o comando `Install-Module -Name AzureAD`). Se solicitado a instalar um módulo do NuGet ou o novo módulo do Azure Active Directory V2 PowerShell, digite Y e pressione ENTER.

2. Execute `Connect-AzureAD` e entre com uma conta de usuário de Administrador Global.
3. Use o script abaixo para atribuir um usuário e uma função a um aplicativo:

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"
    
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```     

Para saber mais sobre como atribuir um usuário a uma função de aplicativo, consulte a documentação sobre [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Para atribuir um grupo a um aplicativo empresarial, é preciso substituir `Get-AzureADUser` por `Get-AzureADGroup`.

### <a name="example"></a>Exemplo

Este exemplo atribui a usuária Brenda Fernandes ao aplicativo [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) usando o PowerShell.

1. No PowerShell, atribua os valores correspondentes às variáveis $username, app_name $ e $app_role_name. 

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

2. Neste exemplo, não sabemos o nome exato da função de aplicativo que desejamos atribuir à Brenda Fernandes. Execute os comandos a seguir para obter o usuário ($user) e a entidade de serviço ($sp) usando o nome UPN do usuário e o nome de exibição da entidade de serviço.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
        
3. Execute o comando `$sp.AppRoles` para exibir as funções disponíveis para o aplicativo Workplace Analytics. Neste exemplo, queremos atribuir a Brenda Fernandes a função de Analista (acesso limitado).
    
    ![Função do Workplace Analytics](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

4. Atribua o nome de função à variável `$app_role_name`.
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

5. Execute o seguinte comando para atribuir o usuário à função de aplicativo:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>Próximas etapas
* [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](remove-user-or-group-access-portal.md)
* [Desabilitar as entradas de usuário em um aplicativo empresarial](disable-user-sign-in-portal.md)
* [Alterar o nome ou logotipo de um aplicativo empresarial](change-name-or-logo-portal.md)
