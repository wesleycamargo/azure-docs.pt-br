<properties 
	pageTitle="Introdução à Microsoft Azure Multi-Factor Authentication na nuvem" 
	description="Esta é a página da Microsoft Azure Multi-Factor Authentication que descreve como começar a usar o Azure MFA na nuvem." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="femila" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="08/15/2016" 
	ms.author="billmath"/>

# Introdução ao Servidor Azure Multi-Factor Authentication na nuvem
No seguinte artigo, você aprenderá como começar a usar o Azure Multi-Factor Authentication na nuvem.

> [AZURE.NOTE]  A documentação a seguir fornece informações sobre como habilitar os usuários usando o **Portal Clássico do Azure**. Se você estiver procurando informações sobre como configurar a Autenticação Multifator do Azure para os usuários O365, confira [Configurar a autenticação multifator para o Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=pt-BR&rs=pt-BR&ad=US)

![MFA na Nuvem](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## Pré-requisitos
Os seguintes pré-requisitos são necessários antes de habilitar a Autenticação Multifator do Azure para seus usuários.




- [Inscreva-se para ter uma assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/) - se você ainda não tem uma assinatura do Azure, precisará inscrever-se para ter uma. Se você estiver apenas começando a usar o Azure MFA, poderá usar uma assinatura de avaliação.
2. [Crie um Provedor de Autenticação Multifator](multi-factor-authentication-get-started-auth-provider.md) e atribua-o a seu diretório ou [atribua licenças aos usuários](multi-factor-authentication-get-started-assign-licenses.md)

> [AZURE.NOTE]  As licenças estão disponíveis para os usuários que têm o Azure MFA, Azure AD Premium ou EMS (Enterprise Mobility Suite). O MFA está incluído no Azure AD Premium e no EMS. Se você possui licenças suficientes, não precisa criar um provedor de autenticação.
		

## Ativar a autenticação multifator para os usuários
Para ativar a autenticação multifator para um usuário, simplesmente altere o estado do usuário de desabilitado para habilitado. Para obter mais informações sobre os estados do usuário, confira [Estados do Usuário no Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md)

Use o procedimento a seguir para habilitar o MFA para os usuários.

### Para ativar o Multi-Factor Authentication
--------------------------------------------------------------------------------
1.  Entre no **portal clássico do Azure** como Administrador.
2.  À esquerda, clique em **Active Directory**.
3.  Em **Diretório**, clique no diretório do usuário que deseja habilitar. ![Clicar no Diretório](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Na parte superior, clique em **Usuários**.
5.  Na parte inferior da página, clique em **Gerenciar Multi-Factor Auth**. ![Clicar no Diretório](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Isso abrirá uma nova guia do navegador. Localize o usuário que deseja habilitar para o Multi-Factor Authentication. Talvez seja necessário alterar o modo de exibição na parte superior. Verifique se o status é **desabilitado.** ![Habilitar o usuário](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Coloque uma **marca** na caixa ao lado do nome.
7.  À direita, clique em **Habilitar**. ![Habilitar o usuário](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Clique em **Habilitar autenticação multifator**. ![Habilitar o usuário](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Você deve observar se o estado do usuário foi alterado de **desabilitado** para **habilitado**. ![Habilitar os Usuários](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  Após habilitar seus usuários, recomendamos a notificação por email. Ele também deve informá-los de como eles podem usar seus aplicativos sem navegador para evitar o bloqueio.


## Ativação automática da autenticação multifator usando o PowerShell

Para alterar o [estado](multi-factor-authentication-whats-next.md) usando o [PowerShell do Azure AD](../powershell-install-configure.md), você poderá usar o seguinte. É possível alterar `$st.State` para ser igual a um dos estados a seguir:


- Habilitado
- Imposto
- Desabilitado

> [AZURE.IMPORTANT]  Saiba que se você for diretamente do estado Desabilitar para o estado Imposto, os clientes de autenticação não modernos irão parar de funcionar porque o usuário não passou pelo registro MFA e obteve uma [senha do aplicativo](multi-factor-authentication-whats-next.md#app-passwords). Se você tiver clientes de autenticação não modernos e exigir senhas do aplicativo, então, será recomendável que vá de um estado Desabilitado para Habilitado. Isso permitirá que os usuários se registrem e obtenham suas senhas do aplicativo.
		
		$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
		$st.RelyingParty = "*"
		$st.State = “Enabled”
		$sta = @($st)
		Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Usar o PowerShell seria uma opção para habilitar os usuários em massa. Atualmente, não há nenhum recurso de habilitação em massa no portal do Azure e você precisa selecionar cada usuário individualmente. Se você tiver muitos usuários, poderá ser uma grande tarefa. Criando um script do PowerShell usando o método acima, você poderá percorrer uma lista de usuários e habilitá-los. Aqui está um exemplo:
    
    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
    	$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
    	$st.RelyingParty = "*"
    	$st.State = “Enabled”
    	$sta = @($st)
    	Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Para obter mais informações sobre os estados do usuário, confira [Estados do Usuário no Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md)

## Próximas etapas
Agora que você tem de configurar a autenticação multifator na nuvem, poderá configurar e instalar sua implantação. Consulte [Configurando a Autenticação Multifator do Azure.]

<!---HONumber=AcomDC_0817_2016-->