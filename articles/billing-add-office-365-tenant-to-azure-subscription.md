<properties
	pageTitle="Usar o locatário do Office 365 com a assinatura do Azure | Microsoft Azure"
	description="Saiba como adicionar um diretório do Office 365 (locatário) a uma assinatura do Azure para fazer a associação."
	services="billing"
	documentationCenter=""
	authors="jiangchen79"
	manager="mbaldwin"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2016"
	ms.author="cjiang"/>

# Associar um locatário do Office 365 com uma assinatura do Azure
Cenários: você adquiriu as assinaturas do Azure e do Office 365 separadamente no passado, e deseja ser capaz de acessar o locatário do Office 365 a partir da assinatura do Azure. Este artigo mostra como é fácil conseguir isso.

> [AZURE.NOTE] Este artigo não se aplica aos clientes do EA (Enterprise Agreement).

## Guia rápido
Se você deseja associar seu locatário do Office 365 à assinatura do Azure, use sua conta do Azure para adicionar o locatário do Office 365 e, depois, associe sua assinatura do Azure ao locatário do Office 365. Consulte as etapas detalhadas.

## Etapas detalhadas
Nesse cenário, Clara Barbosa é um usuário que tem uma assinatura do Azure na conta kelly.wall@outlook.com. Clara também tem uma assinatura do Office 365 sob a conta kelley.wall@contoso.onmicrosoft.com. Agora, Clara quer acessar o locatário do Office 365 com a assinatura do Azure.

![msa-aad-status](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![office-365-user](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

**Pré-requisitos:**

- Você precisa de credenciais do administrador do serviço da assinatura do Azure. Coadministradores não podem executar um subconjunto das etapas.
- Você precisa de credenciais de um administrador global do locatário do Office 365.
- O endereço de email do administrador do serviço não deve estar contido no locatário do Office 365.
- O endereço de email do administrador do serviço não deve ser igual ao do administrador global do locatário do Office 365.
- Se você estiver usando um endereço de email que é uma conta da Microsoft e uma conta organizacional, recomendamos que você altere temporariamente o administrador do serviço de sua assinatura do Azure para usar outra conta da Microsoft. Isso reduzirá as limitações conhecidas do procedimento. Você pode criar uma nova conta da Microsoft na [Página de inscrição de conta da Microsoft](https://signup.live.com/).

	Para alterar o administrador do serviço, execute estas etapas:

	1. Efetue logon no [Portal de Gerenciamento de Conta](https://account.windowsazure.com/subscriptions).
	2. Selecione a assinatura que deseja alterar.
	3. No lado direito, clique em **Editar detalhes da assinatura**.

		![azure-edit-subscription-details](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)

	4. Na caixa **ADMINISTRADOR DE SERVIÇOS**, insira o endereço de email do novo administrador de serviços.

		![change-subscription-service-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

Para associar o locatário do Office 365 à assinatura do Azure, siga estas etapas:

1. 	Entre no [Portal de Gerenciamento de Conta](https://account.windowsazure.com/subscriptions) com as credenciais de administrador do serviço.
2.	Clique em **ACTIVE DIRECTORY** no painel esquerdo.

	![classic-portal-active-directory-entry](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

	> [AZURE.NOTE] Você não deverá ver o locatário do Office 365. Se você o vir, pule a próxima etapa.

	![aad-tenant-default](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)

3. Adicione o locatário do Office 365 à sua assinatura do Azure.
	1. Clique em **NOVO** > **DIRETÓRIO** > **CRIAÇÃO PERSONALIZADA**.

		![aad-custom-create](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)

	2. Na página **Adicionar diretório**, selecione **Usar diretório existente** em **DIRETÓRIO**, clique para selecionar **Estou pronto para sair agora**, e clique em **Concluir** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

		![add-directory-use-existing](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)

	3. Depois de sair, entre com as credenciais do administrador global do seu locatário do Office 365.

		![sign-in-global-admin-office-365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)

	4. Clique em **Continuar**.

		![use-contoso-directory-azure-verify](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)

	5. Clique em **Sair agora**.

		![use-contoso-directory-azure-confirm-and-sign-out](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)

	6. Entre no [Portal de Gerenciamento de Conta](https://account.windowsazure.com/subscriptions) com as credenciais de administrador do serviço.

		![azure-sign-in-service-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

	7. Você deverá ver seu locatário do Office 365 no painel de controle.

		![office-365-tenant-appear-in-azure](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

4. Altere o diretório associado à sua assinatura do Azure.

	1. Clique em **Configurações**.

		![azure-classic-portal-settings-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)

	2. Clique em sua assinatura do Azure e clique em **EDITAR DIRETÓRIO**.

		![azure-subscription-edit-directory](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)

	3. Clique em **Próximo** ![next-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).

		![azure-change-associated-directory](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)

		> [AZURE.WARNING] Você receberá um aviso de que todos os coadministradores serão removidos.

		![azure-confirm-directory-mapping](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)

		>[AZURE.WARNING] Além disso, todos os usuários de [RBAC (Controle de Acesso Baseado em Função)](./active-directory/role-based-access-control-configure.md) com acesso Atribuído nos grupos de recursos existente também serão removidos. No entanto, o aviso que você recebe apenas menciona a remoção dos coadministradores.

		![assigned-users-removed-resource-groups](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)

	4. Clique em **Concluído** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

5. Agora você pode adicionar suas contas organizacionais do Office 365 como coadministradores ao locatário do AAD.

	1. Clique na guia **ADMINISTRADORES** e, em seguida, clique em **ADICIONAR**.

		![azure-classic-portal-settings-administrators](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)

	2. Insira uma conta organizacional de seu locatário do Office 365, clique para selecionar a assinatura do Azure e, em seguida, clique em **Concluir** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

		![azure-add-co-administrator](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)

	3. Volte para a guia **ADMINISTRADORES**, e você deverá ver a conta organizacional exibida como coadministrador.

		![azure-co-administrator-added](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)

6. Depois, você pode testar o acesso com o coadministrador.

	1. Saia do Portal de Gerenciamento de Conta.
	2. Abra o [Portal de Gerenciamento de Conta](https://account.windowsazure.com/subscriptions) ou o [Portal do Azure](https://portal.azure.com/).
	3. Se a página de entrada do Azure tiver um link para **Entrar com a conta de sua organização**, clique no link. Caso contrário, pule essa etapa.

		![Azure-sign-in-with-orgid-link](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)

	4. Insira as credenciais do coadministrador e, em seguida, clique em **Entrar**.

		![Azure-sign-in-with-co-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## Próximas etapas
Esses são cenários relacionados nos quais você já tem uma assinatura do Office 365 e está pronto para uma assinatura do Azure, mas deseja usar as contas de usuário existentes do Office 365 para sua assinatura do Azure. Outra opção seria se você é assinante do Azure e deseja obter uma assinatura do Office 365 para os usuários no Azure Active Directory existentes. Para saber como realizar essas tarefas, confira [Use sua conta existente do Office 365 com sua assinatura do Azure ou vice-versa](billing-use-existing-office-365-account-azure-subscription.md).

<!---HONumber=AcomDC_0622_2016-->