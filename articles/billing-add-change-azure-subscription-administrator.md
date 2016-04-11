<properties
	pageTitle="Como adicionar ou alterar funções de administrador do Azure | Microsoft Azure"
	description="Descreve como adicionar ou alterar o Coadministrador, Administrador de Serviços e Administrador da Conta do Azure "
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor="meerak"
	tags="billing"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="genli"/>

# Como adicionar ou alterar funções de administrador do Azure

Há três tipos de funções de administrador no Microsoft Azure:

| Função administrativa | Limite | Descrição
| ------------- | ------------- |---------------|
|AA (Administrador da Conta) | 1 por conta do Azure |Essa é a pessoa que se inscreveu ou que comprou assinaturas do Azure e que está autorizada a acessar o [Centro de Contas](https://account.windowsazure.com/Home/Index) e realizar várias tarefas de gerenciamento. A capacidade de criar assinaturas, cancelar assinaturas, alterar a cobrança para uma assinatura e alterar o Administrador de Serviços são algumas dessas tarefas.
| SA (Administrador de Serviços) | 1 por assinatura do Azure |Essa função está autorizada a gerenciar serviços no [Portal do Azure](https://portal.azure.com). Por padrão, para uma nova assinatura, o Administrador da Conta também é o Administrador de Serviços.|
|Coadministrador (CA) no [portal clássico do Azure](https://manage.windowsazure.com)|200 por assinatura| Essa função tem os mesmos privilégios de acesso que o Administrador de Serviços, mas não pode alterar a associação de assinaturas nos diretórios do Azure. |

> [AZURE.NOTE] O RBAC (Controle de Acesso Baseado em Função) do Active Directory do Azure permite que os usuários sejam adicionados a várias funções. Para obter mais informações, confira [Controle de acesso baseado em função do Active Directory do Azure](./active-directory/role-based-access-control-configure.md).

## Como adicionar um administrador para uma assinatura

**Portal do Azure**

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu Ajuda, selecione **Assinatura** > *a assinatura que você deseja que o administrador acesse*.

	![newselectsub](./media/billing-add-change-azure-subscription-administrator/newselectsub.png)

3. Na folha Assinatura, selecione **Configurações**> **Usuários**.

	![newsettings](./media/billing-add-change-azure-subscription-administrator/newsettings.png)
4. Na folha Usuários, selecione **Adicionar**>**Selecionar uma função** > **Proprietário**.

	![newselectrole](./media/billing-add-change-azure-subscription-administrator/newselectrole.png)

	**Observação**
	- A função Proprietário tem os mesmos privilégios de acesso da função Coadministrador. Essa função não tem privilégios de acesso à [Central de Contas Azure](https://account.windowsazure.com/subscriptions).
	- Os proprietários adicionados por meio de [Portal do Azure](https://portal.azure.com) não podem gerenciar serviços no [portal clássico do Azure](https://manage.windowsazure.com).  

5. Digite o endereço de email do usuário que deseja adicionar como proprietário, clique no usuário e, em seguida, clique em **Selecionar**.

	![newadduser](./media/billing-add-change-azure-subscription-administrator/newadduser.png)

**Portal clássico do Azure**

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com/).

2. No painel de navegação, selecione **Configurações**> **Administradores**> **Adicionar**. </br>

	![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)

3. Digite o endereço de email da pessoa que deseja adicionar como Coadministrador e, em seguida, selecione a assinatura à qual deseja que o Coadministrador tenha acesso.</br>

	![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

O seguinte endereço de email pode ser adicionado como Coadministrador:

* **Conta da Microsoft** (anteriormente Windows Live ID) </br>: você pode usar uma Conta da Microsoft para entrar em todos os produtos e serviços de nuvem da Microsoft orientados ao consumidor, como Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone e Xbox LIVE.
* **Conta organizacional**</br>: uma conta organizacional é uma conta criada no Active Directory do Azure. O endereço de conta organizacional será semelhante ao seguinte: user@&lt;your domain&gt;.onmicrosoft.com

### Limitações e restrições

 * Cada assinatura é associada a um diretório do AD do Azure (também conhecido como Diretório Padrão). Para localizar o Diretório Padrão ao qual a assinatura está associada, acesse o [Portal clássico do Azure](https://manage.windowsazure.com/), selecione **Configurações** > **Assinaturas**. Verifique a ID da assinatura para localizar o Diretório Padrão.

 * Se estiver conectado com uma Conta da Microsoft, você poderá apenas adicionar outras Contas da Microsoft ou usuários no Diretório Padrão como Coadministrador.

 * Se você estiver conectado a uma conta organizacional, você pode adicionar outras contas institucionais em sua organização como Coadministrador. Por exemplo, abby@contoso.com pode adicionar bob@contoso.com como Administrador de Serviços ou Coadministrador, mas não pode adicionar john@notcontoso.com, a menos que john@noncontoso.com seja o usuário no Diretório Padrão. Usuários conectados usando contas da organização podem continuar adicionando usuários da Conta da Microsoft como Coadministradores ou Administradores de Serviços.

 * Agora que é possível fazer logon no Azure com uma conta organizacional, estas são as alterações de requisitos de conta de administrador de serviço e coadministrador:

	Método de logon| Adicionar Conta da Microsoft ou usuários no Diretório Padrão como CA ou SA? |Adicionar conta organizacional na mesma organização como CA ou SA? |Adicionar conta organizacional em uma organização diferente como CA ou SA?
	------------- | ------------- |---------------|---------------
	Conta da Microsoft |Sim|Não|Não
	Conta organizacional|Sim|Sim|Não

## Como alterar o Administrador de Serviços de uma assinatura

Somente o Administrador da Conta pode alterar o Administrador de Serviços de uma assinatura.

1. Efetue logon na [Central de Contas do Azure](https://account.windowsazure.com/subscriptions) usando o administrador da conta.

2. Selecione a assinatura que deseja alterar.

3. No lado direito, clique em **Editar detalhes da assinatura**. </br>

	![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)

4. Na caixa **ADMINISTRADOR DE SERVIÇOS**, insira o endereço de email do novo administrador de serviços. </br>

	![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## Como alterar o Administrador da Conta

Para transferir a propriedade da conta do Azure para outra conta, confira [Transferindo uma assinatura do Azure](billing-subscription-transfer.md).

## Próximas etapas

* Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, confira [Noções básicas sobre o acesso aos recursos do Azure](./active-directory/active-directory-understanding-resource-access.md)

* Para saber mais sobre como o Active Directory do Azure está relacionado à sua assinatura do Azure, confira [Como as assinaturas do Azure estão associadas ao Active Directory do Azure] (./active-directory/active-directory-how-subscriptions-associated directory.md)

* Para saber mais sobre como o Azure Active Directory está relacionado à sua assinatura do Azure, confira [Atribuindo funções de administrador no Azure Active Directory (Azure AD)](./active-directory/active-directory-assign-admin-roles.md)

<!---HONumber=AcomDC_0330_2016-->