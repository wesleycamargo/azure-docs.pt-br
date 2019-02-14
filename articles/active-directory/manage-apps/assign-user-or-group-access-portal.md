---
title: Atribuir um usuário ou grupo a um aplicativo empresarial no Azure Active Directory | Microsoft Docs
description: Como selecionar um aplicativo empresarial par atribuir um usuário ou um grupo a ele no Azure Active Directory
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: celested
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dd3bd1f3f9f175ba7bf841b1fce45bb7938c02b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209499"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Atribuir um usuário ou um grupo a um aplicativo empresarial no Azure Active Directory
Para atribuir um usuário ou grupo a um aplicativo corporativo, você deverá ter as permissões apropriadas para gerenciar o aplicativo empresarial, além de ser um administrador global do diretório.

> [!NOTE]
> Para requisitos de licenciamento para os recursos discutidos neste artigo, consulte a [do Azure Active Directory, página de preços](https://azure.microsoft.com/pricing/details/active-directory).

> [!NOTE]
> Para aplicativos da Microsoft (como aplicativos do Office 365), use o PowerShell para atribuir usuários a um aplicativo empresarial.


## <a name="assign-a-user-to-an-app---portal"></a>Atribuir um usuário a um aplicativo - portal
1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **Todos os serviços**, insira Azure Active Directory na caixa de texto e, em seguida, selecione **Enter**.
3. Selecione **Aplicativos empresariais**.

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

## <a name="allow-all-users-to-access-an-app---portal"></a>Permitir que todos os usuários acessem um aplicativo - portal
Permitir que todos os usuários acessem um aplicativo:

1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **Todos os serviços**, insira Azure Active Directory na caixa de texto e, em seguida, selecione **Enter**.
3. Selecione **Aplicativos empresariais**.
4. Na folha **Aplicativos empresariais**, escolha **Todos os aplicativos**. Lista os aplicativos que você pode gerenciar.
5. Na folha **Aplicativos empresariais – Todos os aplicativos** , escolha um aplicativo.
6. Na folha ***appname***, selecione **Propriedades**.
7. Em  ***appname* - folha Propriedades**, defina o **Atribuição de usuário necessária?** definindo como **não**. 

**Atribuição de usuário obrigatória?** opção:

- Não afeta se um aplicativo é exibido ou não no painel de acesso do aplicativo. Para mostrar o aplicativo no painel de acesso, você precisará atribuir um grupo ou usuário apropriado para o aplicativo.
- Funciona apenas com os aplicativos de nuvem que estão configurados para logon único SAML e aplicativos locais configurados com o Proxy do Aplicativo. Consulte [logon único para aplicativos](what-is-single-sign-on.md).
- Requer os usuários de consentimento para um aplicativo. Um administrador pode conceder consentimento para todos os usuários.  Consulte [Configurar a forma com que os usuários finais concedem um aplicativo](configure-user-consent.md).


## <a name="assign-a-user-to-an-app---powershell"></a>Atribuir um usuário a um aplicativo - PowerShell

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
* [Remover uma atribuição de usuário ou grupo de um aplicativo empresarial](remove-user-or-group-access-portal.md)
* [Desabilitar as entradas de usuário em um aplicativo empresarial](disable-user-sign-in-portal.md)
* [Alterar o nome ou logotipo de um aplicativo empresarial](change-name-or-logo-portal.md)
