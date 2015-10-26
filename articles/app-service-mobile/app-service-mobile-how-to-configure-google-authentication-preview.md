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

# Como configurar seu aplicativo para usar o logon do Google

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Este tópico mostra como configurar aplicativos móveis para usar o Google como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Faça logon no [Portal de Gerenciamento do Azure] e navegue para o seu aplicativo móvel. Copie a sua **URL**. Você a usará posteriormente com seu aplicativo Google.
 
2. Clique em **Configurações**, **Autenticação de usuário** e clique em **Google**. Copie o **URI de redirecionamento**. Você o usará para configurar o seu aplicativo Google.

3. Vá até o site [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) faça logon com suas credenciais de conta do Google, clique em **Criar projeto**, forneça um **Nome do projeto** e clique em **Criar**.

4. Na barra de navegação à esquerda, clique em **API e Autenticação**, clique em **APIs Sociais** e clique em **API do Google+** > **Habilitar API**.

5. Clique em **API e Autenticação** > **Credenciais** > **Tela de consentimento de OAuth**, selecione seu **Endereço de email**, insira um **Nome do Produto** e clique em **Salvar**.

6. Na guia **Credenciais**, clique em **Adicionar credenciais** > **ID do cliente OAuth 2.0** e selecione **Aplicativo Web**.

7. Cole a **URL** dos Aplicativos Móveis que você copiou anteriormente em **Origens autorizadas JavaScript** e cole o **URI de redirecionamento** que você copiou anteriormente em **URI de redirecionamento autorizado**. Esse URI é o gateway de aplicativo móvel acrescentado do caminho _/signin-google_. Por exemplo: `https://contosogateway.azurewebsites.net/signin-google`. Certifique-se de que você está usando o esquema HTTPS. Em seguida, clique em **Criar**.

8. Na próxima tela, anote os valores de ID do cliente e de segredo do cliente.

    > [AZURE.IMPORTANT]O segredo do cliente é uma credencial de segurança importante. Não compartilhe essa senha com ninguém nem distribua-a em um aplicativo cliente.

9. No [Portal de Gerenciamento do Azure], na folha de configurações do Google para seu aplicativo móvel, cole os valores de ID e de segredo do cliente que você obteve anteriormente. Em seguida, clique em **Salvar**.

     ![][1]

Agora você está pronto para usar o Google para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Portal de Gerenciamento do Azure]: https://portal.azure.com/
 

<!---HONumber=Oct15_HO3-->