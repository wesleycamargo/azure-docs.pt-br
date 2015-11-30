<properties
	pageTitle="Como configurar a autenticação do Twitter para seu aplicativo de Serviços de Aplicativos"
	description="Saiba como configurar a autenticação do Twitter para seu aplicativo de Serviços de Aplicativos."
	services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="10/29/2015"
	ms.author="mahender"/>

# Como configurar seu aplicativo do Serviço de Aplicativo para usar o logon do Twitter

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Este tópico mostra como configurar o Serviço de Aplicativo do Azure para usar o Twitter como um provedor de autenticação.

Para concluir o procedimento deste tópico, você deve ter uma conta do Twitter com um endereço de email verificado. Para criar uma nova conta do Twitter, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Registre seu aplicativo com o Twitter


1. Faça logon no [Portal de Gerenciamento do Azure] e navegue até o seu aplicativo. Copie a sua **URL**. Você a usará para configurar o seu aplicativo do Twitter.

2. Navegue até o site de [Desenvolvedores do Twitter,] entre com suas credenciais da conta do Twitter e clique em **Criar Novo Aplicativo**.

3. Digite o **Nome** e uma **Descrição** para o novo aplicativo. Cole a **URL** do aplicativo no valor **Site**. Em seguida, em **URL de Callback**, cole a **URL de Callback** copiada anteriormente. Esse é o seu gateway de Aplicativo Móvel, acrescentado com o caminho _/.auth/login/twitter/callback_. Por exemplo: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Certifique-se de que você está usando o esquema HTTPS.

    ![][0]
	

	> [AZURE.NOTE]Se você estiver usando o Gateway do Serviço de Aplicativo em vez do recurso de Autenticação/Autorização do Serviço de Aplicativo, a URL de redirecionamento usará a URL de gateway com o caminho _/signin-twitter_.


3.  Na parte inferior da página, leia e aceite os termos. Em seguida, clique em **Criar seu Aplicativo do Twitter**. Isso registrará o aplicativo e exibirá os detalhes do aplicativo.

4. Clique na guia **Configurações**, marque **Permitir que este aplicativo seja usado para entrar com o Twitter** e, em seguida, clique em **Atualizar Configurações**.

5. Selecione a guia **Chaves e Tokens de Acesso**. Tome nota dos valores da **Chave do Consumidor (Chave de API)** e **Segredo do consumidor (Segredo de API)**.

    > [AZURE.NOTE]O segredo do consumidor é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.


## <a name="secrets"> </a>Adicione informações do Twitter ao seu aplicativo


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Twitter**. Paste in the values you obtained earlier and click **Save**.


13. De volta ao [Portal de Gerenciamento do Azure], navegue até o aplicativo. Clique em **Configurações** e em **Autenticação/Autorização**.

14. Se o recurso Autenticação/Autorização não estiver habilitado, mude a opção para **Ativado**.

15. Clique em **Twitter**. Cole os valores de ID do aplicativo e segredo do aplicativo obtidos anteriormente. Em seguida, clique em **OK**.

    ![][1]
	
16. Por padrão, o Serviço de Aplicativo fornece logon, mas não restringe o acesso ao conteúdo do site e às APIs - isso é responsabilidade do código do aplicativo. Se você quiser ter um site totalmente protegido pelo logon do Twitter, altere a lista suspensa **Ação a ser tomada quando a solicitação não for autenticada** para usar a opção **Twitter**. Isso exigirá que todas as solicitações sejam autenticadas; as solicitações não autenticadas serão redirecionadas para fazer logon com o Twitter.

17. Clique em **Salvar**.

Agora você está pronto para usar o Twitter para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Desenvolvedores do Twitter,]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Portal de Gerenciamento do Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md

<!---HONumber=Nov15_HO4-->