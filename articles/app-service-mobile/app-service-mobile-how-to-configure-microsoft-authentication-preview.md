<properties
	pageTitle="Como configurar a autenticação de conta da Microsoft para o seu aplicativo de Serviços de Aplicativos"
	description="Saiba como configurar a autenticação de conta da Microsoft para o seu aplicativo de Serviços de Aplicativos."
	authors="mattchenderson" 
	services="app-service\mobile"
	documentationCenter=""
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="mahender"/>

# Como configurar seu aplicativo para usar o logon da conta da Microsoft

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Este tópico mostra como configurar os aplicativos móveis do Azure para usar a conta da Microsoft como um provedor de autenticação.

## <a name="register"> </a>Registrar seu aplicativo com a conta da Microsoft

1. Faça logon no [Portal de Gerenciamento do Azure] e navegue para o seu aplicativo móvel.

2. Clique em **Configurações**, **Autenticação de usuário**, e, em seguida, clique em **Conta Microsoft**. Copie a **URL de Redirecionamento**. Você a usará para configurar um novo aplicativo para sua Conta Microsoft.

3. Navegue até a página [Meus Aplicativos] no Centro de Desenvolvedoras da conta da Microsoft e faça logon com sua conta da Microsoft, se necessário.

4. Clique em **Criar aplicativo**, digite um **Nome de aplicativo** e clique em **Aceito**.

5. Clique em **Configurações da API**. Selecione **Sim** para **Aplicativo cliente móvel ou de desktop**. No campo **URL de redirecionamento**, insira a **URL de redirecionamento** copiada anteriormente. É o gateway do aplicativo móvel acrescido de _/signin-microsoft_. Por exemplo: `https://contosogateway.azurewebsites.net/signin-microsoft`. Certifique-se de que você está usando o esquema HTTPS. Depois de inserir a URL de redirecionamento, clique em **Salvar**.

	![][0]

	>[AZURE.NOTE]Para um registro de aplicativo da conta da Microsoft existente, você precisará primeiro habilitar a **Segurança de redirecionamento avançada**.

4. Clique em **Configurações da API** e anote os valores da **ID do cliente** e **Segredo do cliente**.

    > [AZURE.NOTE]O segredo do cliente é uma credencial de segurança importante. Não compartilhe o segredo do cliente com ninguém nem distribua-o em um aplicativo cliente.


## <a name="secrets"> </a>Adicionar informações da conta da Microsoft ao seu aplicativo móvel

1. No [Portal de gerenciamento], na folha de configurações da Conta Microsoft para seu aplicativo móvel, cole os valores de ID do cliente e segredo do cliente que você obteve anteriormente. Em seguida, clique em **Salvar**.

    ![][1]

Agora você está pronto para usar uma conta da Microsoft para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication-preview/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication-preview/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Portal de Gerenciamento do Azure]: https://portal.azure.com/
[Portal de gerenciamento]: https://portal.azure.com/
 

<!---HONumber=August15_HO8-->