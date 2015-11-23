<properties
	pageTitle="Como adicionar ou alterar o Coadministrador, Administrador de Serviços e Administrador da Conta do Azure | Microsoft Azure"
	description="Descreve como adicionar ou alterar o Coadministrador, Administrador de Serviços e Administrador da Conta do Azure "
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="jarrettr"
	editor="meerak"
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="genli"/>

# Como adicionar ou alterar o Coadministrador, Administrador de Serviços e Administrador da Conta do Azure
## Funções de administrador

Há três tipos de funções de administrador no Microsoft Azure:

| Função administrativa | Limite | Descrição
| ------------- | ------------- |---------------|
|Administrador da Conta | 1 por conta do Azure |Essa é a pessoa que se inscreveu ou que comprou assinaturas do Azure e que está autorizada a acessar o Centro de Contas e realizar várias tarefas de gerenciamento. A capacidade de criar assinaturas, cancelar assinaturas, alterar a cobrança para uma assinatura e alterar o Administrador de Serviços são algumas dessas tarefas.
| Administrador de serviços | 1 por assinatura do Azure |Essa pessoa está autorizada a acessar o Portal de Gerenciamento do Azure para as assinaturas na conta atribuída a elas. Por padrão, para uma nova assinatura, o Administrador da Conta também é o Administrador de Serviços.|
|Coadministrador|200 por assinatura (além do Administrador de Serviços)|Essa pessoa tem os mesmos privilégios de acesso que o Administrador de Serviços, mas não pode alterar a associação de assinaturas nos diretórios do Azure.|

## Adicionar um Coadministrador para uma assinatura
1. Entre no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/).
2. No painel de navegação, selecione **Configurações**> **Administradores**> **Adicionar**. </br>![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. Digite o endereço de email da pessoa que deseja adicionar como Coadministrador e, em seguida, selecione a assinatura à qual deseja que o Coadministrador tenha acesso.</br> ![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

O seguinte endereço de email pode ser adicionado como Coadministrador:

* **Conta da Microsoft** (anteriormente Windows Live ID) </br> Você pode usar uma Conta da Microsoft para entrar em todos os produtos e serviços de nuvem da Microsoft orientados ao consumidor, como Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone e Xbox LIVE.
* **Conta organizacional**</br> Uma conta organizacional é uma conta criada no Active Directory do Azure. O endereço de conta organizacional será semelhante ao seguinte: usuário@<your domain>.onmicrosoft.com

**Observação**

 * Se você fizer logon com uma Conta da Microsoft, você poderá adicionar apenas outras Contas da Microsoft como Coadministrador. Essa é uma consideração de segurança para impedir que contas não organizacionais descubram se certas contas (por exemplo, janedoe@contoso.com) são contas válidas.
 * Se você estiver conectado a uma conta organizacional, você pode adicionar outras contas institucionais em sua organização como Coadministrador. Por exemplo, abby@contoso.com pode adicionar bob@contoso.com como Administrador de Serviços ou Coadministrador, mas não pode adicionar o john@notcontoso.com. Usuários conectados usando contas da organização podem continuar adicionando usuários da conta da Microsoft com Administradores ou Coadministradores de serviço.
 * Agora que é possível fazer logon no Azure com uma conta organizacional, estas são as alterações de requisitos de conta de administrador de serviço e coadministrador:

| Método de logon| Adicionar Conta da Microsoft como Coadministrador ou Administrador de Serviços? |Adicionar uma conta organizacional na mesma organização como Coadministrador ou Administrador de Serviços? |Adicionar uma conta organizacional em uma organização diferente como Coadministrador ou Administrador de Serviços?
| ------------- | ------------- |---------------|---------------|
|Conta da Microsoft |Sim|Não|Não|
|Conta organizacional|Sim|Sim|Não|

## Alterar o Administrador de Serviços de uma assinatura
Somente o Administrador da Conta pode alterar o Administrador de Serviços de uma assinatura.

1. Faça logon no [Portal de Gerenciamento da Conta](https://account.windowsazure.com/subscriptions) usando o Administrador da Conta.
2. Selecione a assinatura que deseja alterar.
3. No lado direito, clique em **Editar detalhes da assinatura**. </br> ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)

4. Na caixa **ADMINISTRADOR DE SERVIÇOS**, digite o endereço de email do novo administrador de serviços. ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## Altere o administrador da conta.

Para transferir a propriedade da conta do Azure para outra conta, consulte [Transferir uma assinatura do Azure](../billing-subscription-transfer.md).

<!---HONumber=Nov15_HO3-->