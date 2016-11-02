<properties
    pageTitle="Usar um locatário do Office 365 com uma assinatura do Azure | Microsoft Azure"
    description="Saiba como adicionar um diretório do Office 365 (locatário) a uma assinatura do Azure para fazer a associação."
    services=""
    documentationCenter=""
    authors="JiangChen79"
    manager="mbaldwin"
    editor=""
    tags="billing,top-support-issue"/>

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="cjiang"/>


# <a name="associate-an-office-365-tenant-with-an-azure-subscription"></a>Associar um locatário do Office 365 com uma assinatura do Azure
Se você adquiriu as assinaturas do Azure e do Office 365 separadamente no passado e agora deseja acessar o locatário do Office 365 a partir da assinatura do Azure, é fácil fazê-lo. Este artigo mostra como fazer isso.

> [AZURE.NOTE] Este artigo não se aplica aos clientes do EA (Enterprise Agreement).

## <a name="quick-guidance"></a>Guia rápido
Para associar seu locatário do Office 365 à assinatura do Azure, use sua conta do Azure para adicionar o locatário do Office 365 e, depois, associe sua assinatura do Azure ao locatário do Office 365.

## <a name="detailed-steps"></a>Etapas detalhadas
Nesse cenário, Clara Barbosa é um usuário que tem uma assinatura do Azure na conta kelley.wall@outlook.com. Clara também tem uma assinatura do Office 365 na conta kelley.wall@contoso.onmicrosoft.com. Agora, Clara quer acessar o locatário do Office 365 com a assinatura do Azure.

![Captura de tela do status do Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![Captura de tela de usuários ativos do centro de administração do Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

### <a name="prerequisites"></a>Pré-requisitos
Para que a associação funcione corretamente, os seguintes pré-requisitos são necessários:

- Você precisa das credenciais do administrador do serviço da assinatura do Azure. Coadministradores não podem executar um subconjunto das etapas.
- Você precisa das credenciais de um administrador global do locatário do Office 365.
- O endereço de email do administrador do serviço não deve estar contido no locatário do Office 365.
- O endereço de email do administrador do serviço não deve ser igual ao do administrador global do locatário do Office 365.
- Se você estiver usando um endereço de email que é uma conta da Microsoft e uma conta organizacional, altere temporariamente o administrador do serviço de sua assinatura do Azure para usar outra conta da Microsoft. Você pode criar uma nova conta da Microsoft na [Página de inscrição de conta da Microsoft](https://signup.live.com/).


Para alterar o administrador do serviço, execute estas etapas:

1. Entre no [Portal de Gerenciamento de Conta](https://account.windowsazure.com/subscriptions).
2. Selecione a assinatura que deseja alterar.
3. Selecione **Editar detalhes da assinatura**.

    ![Captura de tela das informações de assinatura do Azure, com "Editar detalhes da assinatura" realçado](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)

4. Na caixa **ADMINISTRADOR DE SERVIÇOS** , insira o endereço de email do novo administrador de serviços.

    ![Captura de tela da caixa de diálogo "Editar sua assinatura"](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

### <a name="associate-the-office-365-tenant-with-the-azure-subscription"></a>Associar o locatário do Office 365 à assinatura do Azure
Para associar o locatário do Office 365 à assinatura do Azure, siga estas etapas:

1.  Entre no [Portal de Gerenciamento de Conta](https://account.windowsazure.com/subscriptions) com as credenciais de administrador do serviço.
2.  No painel esquerdo, selecione **ACTIVE DIRECTORY**.

    ![Captura de tela da entrada do Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

    > [AZURE.NOTE] Você não deverá ver o locatário do Office 365. Se você o vir, pule a próxima etapa.

    ![Captura de tela do diretório padrão do Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)

3. Adicione o locatário do Office 365 à sua assinatura do Azure.

    a. Selecione **NOVO** > **DIRETÓRIO** > **CRIAÇÃO PERSONALIZADA**.

    ![Captura de tela da criação personalizada do Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)

    b. Na página **Adicionar diretório** em **DIRETÓRIO**, selecione **Usar diretório existente**. Em seguida, selecione **estou pronto para sair agora** e selecione **Concluir** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

    ![Captura de tela de "Usar diretório existente"](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)

    c. Depois de sair, entre com as credenciais do administrador global do seu locatário do Office 365.

    ![Captura de tela de entrada no administrador global do Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)

    d. Selecione **Continuar**.

    ![Captura de tela de verificação](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)

    e. Selecione **Sair agora**.

    ![Captura de tela de saída](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)

    f. Entre no [Portal de Gerenciamento de Conta](https://account.windowsazure.com/subscriptions) com as credenciais de administrador do serviço.

    ![Captura de tela de entrada do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

    g. Você deverá ver seu locatário do Office 365 no painel de controle.

    ![Captura de tela do painel](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

4. Altere o diretório associado à sua assinatura do Azure.

    a. Escolha a opção **Configurações**.

    ![Captura de tela do ícone de configurações do Portal Clássico do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)

    b. Selecione sua assinatura do Azure e selecione **EDITAR DIRETÓRIO**.
    ![Captura de tela do diretório de edição de assinatura do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)

    c. Selecione **próximo** ![next-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).

    ![Captura de tela de "Alterar o diretório associado"](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)

    > [AZURE.WARNING] Você receberá um aviso de que todos os coadministradores serão removidos.

    ![azure-confirm-directory-mapping](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)

    >[AZURE.WARNING] Além disso, todos os usuários de [RBAC (Controle de Acesso Baseado em Função)](./active-directory/role-based-access-control-configure.md) com acesso Atribuído nos grupos de recursos existente também serão removidos. No entanto, o aviso que você recebe apenas menciona a remoção dos coadministradores.

    ![assigned-users-removed-resource-groups](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)

    d. Selecione **Concluir** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

5. Agora você pode adicionar suas contas organizacionais do Office 365 como coadministradores ao locatário do Azure Active Directory.

    a. Selecione a guia **ADMINISTRADORES** e, em seguida, selecione **ADICIONAR**.

    ![Captura de tela da guia de administradores de configurações do Portal Clássico do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)

    b. Insira uma conta organizacional de seu locatário do Office 365, clique para selecionar a assinatura do Azure e, em seguida, clique em **Concluir** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

    ![Captura de tela de caixa de diálogo de adicionar coadministrador ao Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)

    c. Volte para a guia **ADMINISTRADORES**. Você deverá ver a conta organizacional exibida como coadministrador.

    ![Captura de tela da guia administradores](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)

6. Depois, você pode testar o acesso com o coadministrador.

    a. Saia do portal de Gerenciamento de Conta.

    b. Abra o [portal de Gerenciamento de Conta](https://account.windowsazure.com/subscriptions) ou o [portal do Azure](https://portal.azure.com/).

    c. Se a página de entrada do Azure tiver um link para **Entrar com a conta de sua organização**, selecione o link. Caso contrário, pule essa etapa.

    ![Captura de tela de página de entrada do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)

    d. Insira as credenciais do coadministrador e, em seguida, selecione **Entrar**.

    ![Captura de tela de página de entrada do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="next-steps"></a>Próximas etapas
Cenários relacionados incluem:

- Se você já tem uma assinatura do Office 365 e estiver pronto para uma assinatura do Azure, mas desejar usar as contas de usuário existentes do Office 365 para sua assinatura do Azure.
- Se você é assinante do Azure e deseja obter uma assinatura do Office 365 para os usuários na instância do Azure Active Directory existente.

Para saber como realizar essas tarefas, confira [Use sua conta existente do Office 365 com sua assinatura do Azure ou vice-versa](billing-use-existing-office-365-account-azure-subscription.md).



<!--HONumber=Oct16_HO2-->


