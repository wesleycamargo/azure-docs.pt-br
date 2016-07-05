<properties
	pageTitle="Compartilhar um único locatário do Azure AD entre assinaturas do Office 365 e do Azure | Microsoft Azure"
	description="Aprenda a compartilhar seu locatário do Azure AD do Office 365 e seus usuários com sua assinatura do Azure ou vice-versa"
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
	ms.date="06/20/2016"
	ms.author="cjiang"/>

# Use sua conta existente do Office 365 com sua assinatura do Azure ou vice-versa
Cenário: se você já tem uma assinatura do Office 365 e estiver pronto para uma assinatura do Azure, mas deseja usar as contas de usuário existentes do Office 365 para sua assinatura do Azure. Outra opção seria se você é assinante do Azure e deseja obter uma assinatura do Office 365 para os usuários no Azure Active Directory existentes. Este artigo mostra como é fácil conseguir ambos.

> [AZURE.NOTE] Este artigo não se aplica aos clientes do EA (Enterprise Agreement).

## Guia rápido

- Se você já tiver uma assinatura do Office 365 e desejar inscrever-se no Azure, use a opção **Entrar com sua conta organizacional** e prossiga com a inscrição no Azure com sua conta do Office 365. Consulte as [etapas detalhadas](#s1).
- Se você já tiver uma assinatura do Azure e desejar obter uma assinatura do Office 365, entre no Office 365 com sua conta do Azure e prossiga com as etapas de inscrição. Depois de concluído, a assinatura do Office 365 é adicionada ao mesmo Azure Active Directory ao qual sua assinatura do Azure pertence. Consulte as [etapas detalhadas](#s2).

>[AZURE.NOTE] Para obter uma assinatura do Office 365, a conta usada para entrar no sistema deve ser um membro da função de diretório de Administrador Global ou Administrador de Cobrança no seu locatário do Azure AD. [Saiba como conhecer a função do Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory)

Para compreender como as coisas funcionam quando você adiciona uma assinatura a uma conta, consulte [Informações básicas](#background-information) mais adiante no artigo.

## Etapas detalhadas
<a id="s1"></a>
### Cenário 1: usuários do Office 365 pretendem comprar o Azure
Nesse cenário, supomos que Clara Barbosa é um usuário que tem uma assinatura do Office 365 e pretende assinar o Azure. Há dois usuários adicionais ativos, Jane e Tricia. A conta de Clara é admin@contoso.onmicrosoft.com.

![office365-users-admin-center.png](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

Para inscrever-se no Azure, siga estas etapas:

1. Inscreva-se no Azure em [Azure.com](https://azure.microsoft.com/). Clique em **Experimente gratuitamente**. Na página seguinte, clique em **Iniciar agora**.

	![azure-signup-try-free](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)

2. Clique em **Entrar com sua conta institucional**.

	![sign-in-to-azure](./media/billing-use-existing-office-365-account-azure-subscription/3-sign-in-to-azure.png)

3. Entre com sua conta do Office 365. Nesse caso, é a conta do Office 365 é de Clara.

	![sign-in-with-org-account](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)

4. Preencha as informações e conclua o processo de inscrição.

	![azure-sign-up-fill-information](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

5. Clique em **Começar a gerenciar meu serviço** e você está pronto para começar.

	![azure-start-managing-my-service](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

Agora você está pronto. No Portal do Azure, você verá os mesmos usuários aparecendo no mesmo diretório. Para fazer isso, siga essas etapas:

1. Clique em **Começar a gerenciar meu serviço** na captura de tela acima.
2. Clique em **Navegar** e em **Active Directory**.

	![azure-portal-browse-ad](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Clique em **USUÁRIOS**.

	![azure-portal-ad-users-tab](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)

4. Todos os usuários, incluindo Clara, estão listados como esperado.

	![azure-portal-ad-users](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>
### Cenário 2: usuários do Azure pretendem comprar o Office 365

Nesse cenário, Clara Barbosa é um usuário que tem uma assinatura do Azure na conta admin@contoso.onmicrosoft.com. Clara deseja assinar o Office 365 e usar o mesmo diretório que ela já tem com o Azure.

>[AZURE.NOTE] Para obter uma assinatura do Office 365, a conta usada para entrar no sistema deve ser um membro da função de diretório de Administrador Global ou Administrador de Cobrança no seu locatário do Azure AD. [Saiba como conhecer a função do Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory)

![azure-portal-settings-subscription](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![azure-portal-ads-users](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

Para assinar o Office 365, siga estas etapas:

1. Vá para o [página de produto do Office 365](https://products.office.com/business) e selecione um plano é adequado para você.
2. Depois de selecionar o plano, a página a seguir é exibida. Não preencha o formulário. Clique em **Entrar** na parte superior direita da página.

	![office-365-trial-page](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)

3. Entre com as credenciais da sua conta. Nesse caso, é a conta de Clara.

	![office-365-sign-in](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)

4. Clique em **Experimentar agora mesmo**.

	![office-365-confirm-your-order](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)

5. Na página de confirmação do pedido, clique em **Continuar**.

	![office-365-order-receipt](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

Agora você está pronto. No centro de administração do Office 365, você verá os mesmos usuários do diretório Contoso aparecendo como usuários ativos. Para fazer isso, siga essas etapas:

1. Abra o centro de administração do Office 365.
2. Expanda **USUÁRIOS** e clique em **Usuários Ativos**.

	![office-365-admin-center-users](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### Como conhecer sua função no Azure Active Directory

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Navegar** e em **Active Directory**.

	![azure-portal-browse-ad](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Clique em **USUÁRIOS**.

	![azure-portal-default-ad-users](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)

4. Clique no usuário. Neste exemplo, Clara Barbosa.
5. Observe o campo de **FUNÇÃO ORGANIZACIONAL**.

	![azure-portal-user-identity](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## Informações básicas
O Office 365 e o Azure usam o serviço do AAD (Azure Active Directory) para gerenciar usuários e assinaturas. Considere um diretório do Azure como um contêiner para você, seu grupo de usuários e assinaturas. Para usar a mesma conta de usuário que suas assinaturas do Microsoft Azure e o Office 365, você precisa certificar-se de que as assinaturas sejam criadas no mesmo diretório.

- Uma assinatura é criada em um diretório, não o oposto.
- Os usuários pertencem a diretórios, não o oposto.
- Uma assinatura chega ao diretório do usuário que cria a assinatura. Desta forma, sua assinatura do Office 365 está ligada à mesma conta da sua assinatura do Azure quando você usa a conta para criar a assinatura do Office 365.

![background-information](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Consulte [Como assinaturas do Azure são associadas ao Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md) para obter mais informações.

**Observações:**

- As assinaturas do Azure pertencem a usuários individuais no diretório.
- As assinaturas do Office 365 pertencem o próprio diretório. Os usuários dentro do diretório poderão operar nessas assinaturas se eles tiverem as permissões necessárias.

##Próximas etapas
Este é um cenário relacionado no qual você adquiriu as assinaturas do Azure e do Office 365 separadamente no passado, e deseja ser capaz de acessar o locatário do Office 365 a partir da assinatura do Azure. Para saber como realizar essa tarefa, confira [Associar um locatário do Office 365 a uma assinatura do Azure](billing-add-office-365-tenant-to-azure-subscription.md).

<!---HONumber=AcomDC_0622_2016-->