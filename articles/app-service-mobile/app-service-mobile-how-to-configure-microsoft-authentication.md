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
	ms.date="10/30/2015"
	ms.author="mahender"/>

# Como configurar seu aplicativo do Serviço de Aplicativo para usar o logon da Conta da Microsoft

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Este tópico mostra como configurar o Serviço de Aplicativo do Azure para usar a conta da Microsoft como um provedor de autenticação.


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Registrar seu aplicativo com a conta da Microsoft

1. Faça logon no [Portal de Gerenciamento do Azure] e navegue até o seu aplicativo. Copie a **URL**. Você usará isso para configurar o aplicativo de sua Conta da Microsoft.

2. Navegue até a página [Meus Aplicativos] no Centro de Desenvolvedoras da conta da Microsoft e faça logon com sua conta da Microsoft, se necessário.

4. Clique em **Criar aplicativo**, digite um **Nome de aplicativo** e clique em **Aceito**.

5. Clique em **Configurações da API**. Selecione **Sim** para **Aplicativo cliente móvel ou de desktop**. No campo **URL de Redirecionamento**, insira a **URL de Redirecionamento** do seu aplicativo e clique em **Salvar**. O URI de redirecionamento é a URL do seu aplicativo acrescentada com o caminho _/.auth/login/microsoftaccount/callback_. Por exemplo: `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Certifique-se de que você está usando o esquema HTTPS.

	![][0]


	> [AZURE.NOTE]Se você estiver usando o Gateway do Serviço de Aplicativo em vez do recurso de Autenticação/Autorização do Serviço de Aplicativo, a URL de redirecionamento usará a URL de gateway com o caminho _/signin-microsoft_.


6. Clique em **Configurações da API** e anote os valores da **ID do cliente** e **Segredo do cliente**.


    > [AZURE.NOTE]O segredo do cliente é uma credencial de segurança importante. Não compartilhe o segredo do cliente com ninguém nem distribua-o em um aplicativo cliente.
	

## <a name="secrets"> </a>Adicionar informações da Conta da Microsoft ao seu aplicativo


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Microsoft Account**. Paste in the values you obtained earlier and click **Save**.


7. De volta ao [Portal de Gerenciamento do Azure], navegue até o aplicativo. Clique em **Configurações** e em **Autenticação/Autorização**.

8. Se o recurso Autenticação/Autorização não estiver habilitado, mude a opção para **Ativado**.

9. Clique em **Conta da Microsoft**. Cole os valores de ID do Aplicativo e de Segredo do Aplicativo que você obteve anteriormente e, opcionalmente, habilite os escopos exigidos pelo seu aplicativo. Em seguida, clique em **OK**.

    ![][1]
	
11. Por padrão, o Serviço de Aplicativo fornece logon, mas não restringe o acesso ao conteúdo do site e às APIs - isso é responsabilidade do código do aplicativo. Se você quiser ter um site totalmente protegido pelo logon da Conta da Microsoft, altere a lista suspensa **Ação a ser tomada quando a solicitação não for autenticada** para usar a opção **Conta da Microsoft**. Isso exigirá que todas as solicitações sejam autenticadas; as solicitações não autenticadas serão redirecionadas para fazer logon com a Conta da Microsoft.

11. Clique em **Salvar**.


Agora você está pronto para usar uma conta da Microsoft para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Portal de Gerenciamento do Azure]: https://portal.azure.com/

<!---HONumber=Nov15_HO4-->