---
title: Obtenha visibilidade de todo o locatário para o Centro de Segurança do Azure | Microsoft Docs
description: Saiba mais sobre como ganhar visibilidade para todo o locatário na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2018
ms.author: terrylan
ms.openlocfilehash: 800ec83b3599dba716e7a4a015b9b8c1745a0975
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144560"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Obtenha visibilidade de todo o locatário para o Centro de Segurança do Azure
Este artigo ajuda você a começar fazendo várias ações que maximizam os benefícios que o Centro de Segurança do Azure oferece. A execução dessas ações permite que você obtenha visibilidade de todas as assinaturas do Azure vinculadas ao locatário do Azure Active Directory e gerencie com eficiência a postura de segurança da organização em escala, aplicando políticas de segurança em várias assinaturas de maneira agregada.

## <a name="management-groups"></a>Grupos de gerenciamento
Os grupos de gerenciamento do Azure fornecem a capacidade de gerenciar com eficiência o acesso, as políticas e os relatórios sobre grupos de assinaturas, bem como gerenciar com eficácia toda a propriedade do Azure executando ações no grupo de gerenciamento raiz. Cada locatário Azure AD recebe um único grupo de gerenciamento de nível superior chamado grupo de gerenciamento “raiz”. Esse grupo de gerenciamento raiz é compilado na hierarquia para que todos os grupos de gerenciamento e assinaturas sejam dobrados nele. Esse grupo permite que políticas globais e atribuições de RBAC sejam aplicados no nível de diretório. 

O grupo de gerenciamento raiz é criado automaticamente quando você faz qualquer uma das seguintes ações: 
1. Opta por usar os grupos de gerenciamento do Azure navegando para **Grupos de Gerenciamento** no [Portal do Azure](https://portal.azure.com).
2. Cria um grupo de gerenciamento via uma chamada API.
3. Cria um grupo de gerenciamento com PowerShell.

Para obter mais informações sobre grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](../azure-resource-manager/management-groups-overview.md).

## <a name="create-a-management-group-in-the-azure-portal"></a>Criar um Grupo de gerenciamento no Portal do Azure
Você organiza assinaturas em grupos de gerenciamento e aplica as políticas de governança aos grupos de gerenciamento. Todas as assinaturas dentro de um grupo de gerenciamento herdam automaticamente as políticas aplicadas ao grupo de gerenciamento. Embora os grupos de gerenciamento não precisem integrar a Central de Segurança, é altamente recomendável criar pelo menos um grupo de gerenciamento para que o grupo de gerenciamento raiz seja criado. Depois que o grupo é criado, todas as assinaturas sob o seu locatário Azure AD serão conectadas a ele. Para obter instruções sobre o PowerShell e mais informações, consulte [Criar grupos de gerenciamento para gerenciamento de recursos e organização](../azure-resource-manager/management-groups-create.md).

 
1. Entre no [Portal do Azure](http://portal.azure.com).
2. Selecione **Todos os serviços** > **Grupos de gerenciamento**.
3. Na página principal, selecione **Novo grupo de gerenciamento.** 

    ![Grupo Principal](./media/security-center-management-groups/main.png) 
4.  Preencha o campo de ID do grupo de gerenciamento. 
    - **ID do Grupo de Gerenciamento** é o identificador exclusivo do diretório usado para enviar comandos nesse grupo de gerenciamento. Esse identificador não é editável após a criação, visto que é usado em todo o sistema do Azure para identificar esse grupo. 
    - O campo de nome de exibição é o nome exibido no portal do Azure. Um nome de exibição separado é um campo opcional ao criar o gerenciamento de grupo e pode ser alterado a qualquer momento.  

      ![Criar](./media/security-center-management-groups/create_context_menu.png)  
5.  Selecione **Salvar**

### <a name="view-management-groups-in-the-azure-portal"></a>Exibir grupos de gerenciamento no Portal do Azure
1. Entre no [Portal do Azure](http://portal.azure.com).
2. Para visualizar grupos de gerenciamento, selecione **Todos serviços** sob o menu principal do Azure.
3. Sob **Geral**, selecione **Grupos de Gerenciamento**.

    ![Crie um grupo de gerenciamento](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Conceda visibilidade em nível de locatário e a capacidade de atribuir políticas

Para obter visibilidade da postura de segurança de todas as inscrições registradas no locatário do Azure AD, é necessário atribuir uma função RBAC com permissões de leitura suficientes no grupo de gerenciamento raiz.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Elevar o acesso de um Administrador global no Azure Active Directory
Um administrador de locatário do Azure Active Directory não tem acesso direto a assinaturas do Azure. No entanto, como administrador de diretório, eles têm o direito de se elevar a uma função que tenha acesso. Um administrador de locatário do Azure AD precisa se elevar para o administrador de acesso do usuário no nível do grupo de gerenciamento raiz para poder atribuir funções do RBAC. Para obter instruções e informações adicionais do PowerShell, consulte [Elevar o acesso de um administrador global no Active Directory do Azure](../role-based-access-control/elevate-access-global-admin.md). 


1. Entre no [portal do Azure](https://portal.azure.com) ou no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com).

2. Na lista de navegação, clique em **Azure Active Directory**, depois clique em **Propriedades**.

   ![Azure Active Directory – captura de tela](./media/security-center-management-groups/aad-properties.png)

3. Em **Administrador global pode gerenciar Assinaturas do Azure e Grupos de Gerenciamento**, selecione a opção **Sim**.

   ![O Administrador global pode gerenciar Assinaturas do Azure e Grupos de Gerenciamento – captura de tela](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Ao selecionar a opção **Sim**, sua conta de Administrador global (conectada no usuário no momento) é adicionada à função de Administrador de Acesso do Usuário no Azure RBAC no escopo raiz (`/`), o que lhe concede acesso de visualização e relatório em todas as assinaturas do Azure associadas ao seu locatário do Azure AD.

   - Ao selecionar a opção **Não**, sua conta de Administrador global (conectada no usuário no momento) é removida da função de Administrador de Acesso do Usuário no Azure RBAC. Não é possível ver todas as assinaturas do Azure associadas ao locatário do Azure AD, sendo possível visualizar e gerenciar apenas as assinaturas do Azure às quais você recebeu acesso.

4. Clique em **Salvar**, para salvar suas configurações.

    - Essa configuração não é uma propriedade global, aplicando-se somente ao usuário conectado no momento.

5. Execute as tarefas que você precisa fazer com o acesso elevado. Ao terminar, retorne a opção para **Não**.

### <a name="open-or-refresh-security-center"></a>Abra ou atualize o Centro de Segurança
Após elevar o acesso, abra ou atualize a Central de Segurança do Azure para verificar se você tem visibilidade de todas as assinaturas do locatário do Azure AD. 

1. Entre no [Portal do Azure](https://portal.azure.com). 
2. Certifique-se de selecionar todas as assinaturas no seletor de assinatura que você gostaria de exibir na Central de Segurança.
    ![Captura de tela do seletor de assinatura](./media/security-center-management-groups/subscription-selector.png)
1. Selecione **Todos serviços** sob o menu principal do Azure e então selecione **Centro de Segurança**.
2. Na **Visão geral**, há um gráfico de cobertura de assinatura. 
    ![Screenshot tabela de cobertura de assinatura](./media/security-center-management-groups/security-center-subscription-coverage.png)
3. Clique em **Cobertura** para ver a lista de assinaturas cobertas. 
    ![Screenshot lista de cobertura de assinatura](./media/security-center-management-groups/security-center-coverage.png)

### <a name="assign-rbac-roles-to-users"></a>Designe funções RBAC aos usuários
Depois que um administrador de locatário tiver acesso elevado, ele poderá atribuir uma função de RBAC a usuários relevantes no nível do grupo de gerenciamento raiz. A função recomendada para designar é [**Leitor**](../role-based-access-control/built-in-roles.md#reader). A função é requerida para fornecer visibilidade nível de locatário. A função atribuída será automaticamente propagada para todos os grupos de gerenciamento e assinaturas no grupo de gerenciamento raiz. Para mais informações sobre funções RBAC, consulte [funções disponíveis](../active-directory/users-groups-roles/directory-assign-admin-roles.md#available-roles). 

1. Instale o [Azure PowerShell](/powershell/azure/install-azurerm-ps).
2. Execute os seguintes comandos: 

    ```azurepowershell
    # Install Management Groups Powershell module
    Install-Module AzureRM.Resources
    
    # Login to Azure as a Global Administrator user
    Login-AzureRmAccount
    ```

3. Quando solicitado, entre com suas credenciais. 

    ![Logue em screenshot promt](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Conceda permissões de função de leitor executando o seguinte comando:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Para remover a função, use o seguinte comando: 

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

<!-- Currently, PowerShell method only 6/26/18

1. Sign in to the [Azure portal](https://portal.azure.com). 
2. To view management groups, select **All services** under the Azure main menu then select **Management Groups**.
3.  Select a management group and click **details**.

    ![Management Groups details screenshot](./media/security-center-management-groups/management-group-details.PNG)
 
4. Click **Access control (IAM)** then **Add**.
5. Select the role to assign and the user, then click **Save**.  
   
   ![Add Security Reader role screenshot](./media/security-center-management-groups/asc-security-reader.png)
-->

### <a name="remove-elevated-access"></a>Remover acesso elevado 
Uma vez que as funções RBAC tenham sido designadas aos usuários, o administrador locatário deve se remover da função de administrador de acesso de usuário.

1. Entre no [portal do Azure](https://portal.azure.com) ou no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com).

2. Na lista de navegação, clique em **Azure Active Directory**, depois clique em **Propriedades**.

3. Em **Administrador global pode gerenciar Assinaturas do Azure e Grupos de Gerenciamento**, selecione a opção **Não**.

4. Clique em **Salvar**, para salvar suas configurações.



## <a name="adding-subscriptions-to-a-management-groups"></a>Adicionando assinaturas a grupos de gerenciamento
Você consegue adicionar assinaturas a um grupo de gerenciamento que você criou. Estes passos não são obrigatórios para ganhar visibilidade a nível de locatário e política global e gerenciamento de acesso.

1. Em **Grupos de Gerenciamento**, selecione um grupo de gerenciamento para adicionar sua assinatura.

    ![Selecione um grupo de gerenciamento e adicione uma assinatura](./media/security-center-management-groups/management-group-subscriptions.png)

2. Selecione **Adicionar existente**.

    ![Adicione existente](./media/security-center-management-groups/add-existing.png)

3. Digite a assinatura em **Adicionar recurso existente** e clique em **Salvar**.

4. Repita etapas 1 a 3 até você ter adicionado todas as assinaturas no escopo.

 > [!NOTE]
 > Grupos de gerenciamento podem conter ambos assinaturas e grupos de gerenciamento criança. Quando você designa a um usuário uma função RBAC a um grupo de gerenciamento pai, o acesso é herdado pelas assinaturas de grupo de gerenciamento criança. Conjunto de políticas no grupo de gerenciamento pai são também herdadas pela criança. 

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como ganhar visibilidade a nível locatário para Centro de Segurança do Azure. Para saber mais sobre a Central de Segurança, confira estes artigos:

> [!div class="nextstepaction"]
> [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Gerencie e responda a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md)

