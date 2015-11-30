<properties
	pageTitle="Como configurar a autenticação do Google para seu aplicativo de Serviços de Aplicativos"
	description="Saiba como configurar a autenticação do Google para seu aplicativo de Serviços de Aplicativos."
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
	ms.date="08/28/2015"
	ms.author="mahender"/>

# Como configurar seu aplicativo do Serviço de Aplicativo para usar o logon do Google

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Este tópico mostra como configurar o Serviço de Aplicativo do Azure para usar o Google como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Registre seu aplicativo com o Google

1. Faça logon no [Portal de Gerenciamento do Azure] e navegue até o seu aplicativo. Copie a **URL**. Você o usará para configurar o seu aplicativo Google.
 
2. Vá até o site [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) faça logon com suas credenciais de conta do Google, clique em **Criar projeto**, forneça um **Nome do projeto** e clique em **Criar**.

3. Na barra de navegação esquerda, clique em **API e Autenticação** e, em **APIs Sociais**, clique em **API do Google+** > **Habilitar API**.

4. Clique em **API e Autenticação** > **Credenciais** > **Tela de consentimento do OAuth**, selecione seu **Endereço de email**, insira um **Nome do Produto** e clique em **Salvar**.

5. Na guia **Credenciais**, clique em **Adicionar credenciais** > **ID do cliente do OAuth 2.0** e selecione **Aplicativo Web**.

6. Cole a **URL** do Serviço de Aplicativo que você copiou anteriormente em **Origens Autorizadas do JavaScript** e cole o **URI de Redirecionamento** que você copiou anteriormente em **URI de Redirecionamento Autorizado**. O URI de redirecionamento é a URL do seu aplicativo acrescentada com o caminho _/.auth/login/google/callback_. Por exemplo: `https://contoso.azurewebsites.net/.auth/login/google/callback`. Certifique-se de que você está usando o esquema HTTPS. Em seguida, clique em **Criar**.


	> [AZURE.NOTE]Se você estiver usando o Gateway do Serviço de Aplicativo em vez do recurso de Autenticação/Autorização do Serviço de Aplicativo, a URL de redirecionamento usará a URL de gateway com o caminho _/signin-google_.


7. Na próxima tela, anote os valores de ID do cliente e de segredo do cliente.


    > [AZURE.IMPORTANT]O segredo do cliente é uma credencial de segurança importante. Não compartilhe essa senha com ninguém nem distribua-a em um aplicativo cliente.


## <a name="secrets"> </a>Adicionar informações do Google ao seu aplicativo


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Google**. Paste in the values you obtained earlier and click **Save**.


8. De volta ao [Portal de Gerenciamento do Azure], navegue até o aplicativo. Clique em **Configurações** e em **Autenticação/Autorização**.

9. Se o recurso Autenticação/Autorização não estiver habilitado, mude a opção para **Ativado**.

10. Clique em **Google**. Cole os valores de ID do Aplicativo e de Segredo do Aplicativo que você obteve anteriormente e, opcionalmente, habilite os escopos exigidos pelo seu aplicativo. Em seguida, clique em **OK**.

    ![][1]
	
11. Por padrão, o Serviço de Aplicativo fornece logon, mas não restringe o acesso ao conteúdo do site e às APIs - isso é responsabilidade do código do aplicativo. Se você quiser ter um site totalmente protegido pelo logon do Google, altere a lista suspensa **Ação a ser tomada quando a solicitação não for autenticada** para usar a opção **Google**. Isso exigirá que todas as solicitações sejam autenticadas; as solicitações não autenticadas serão redirecionadas para fazer logon com o Google.

12. Clique em **Salvar**.

Agora você está pronto para usar o Google para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Portal de Gerenciamento do Azure]: https://portal.azure.com/
 

<!---HONumber=Nov15_HO4-->