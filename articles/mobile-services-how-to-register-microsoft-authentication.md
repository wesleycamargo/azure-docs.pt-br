<properties pageTitle="Registrar para autenticação Microsoft - Serviços Móveis" metaKeywords="Azure registering application, Azure Microsoft authentication, application authenticate, authenticate mobile services" description="Learn how to register for Microsoft authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use a Microsoft Account login" authors="glenga" services="mobile-services" documentationCenter="Mobile" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Registrar seus aplicativos para usar um logon de Conta da Microsoft

Este tópico mostra como registrar seu aplicativo para usar o Live Connect como um provedor de autenticação para os Serviços Móveis do Azure. 

>[WACOM.NOTE]Para configurar a autenticação de Conta da Microsoft para um aplicativo Windows universal ou para fornecer uma experiência de logon único para um aplicativo da Windows Store, consulte[Registrar seu pacote do aplicativo da Windows Store para a autenticação da Microsoft](/pt-br/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication).

1. Navegue até a página <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Meus Aplicativos</a> na Central de Desenvolvedores do Live Connect e faça logon com sua conta da Microsoft, se necessário. 

2. Clique em **Criar aplicativo**, digite um **Nome de aplicativo** e clique em **Aceito**.

   	![][1] 

   	Isso registra o aplicativo no Live Connect.

3. Clique em **Configurações de API**, forneça um valor de `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` em **Redirecionar URL** e clique em **Salvar**.

	>[WACOM.NOTE]Para um serviço móvel de back-end do .NET publicado no Azure usando o Visual Studio, a URL de redirecionamento é a URL do seu serviço móvel anexada com o caminho _signin-microsoft_ do seu serviço móvel como um serviço .NET, por exemplo, <code>https://todolist.azure-mobile.net/signin-microsoft</code>.  

	![][3]

	Isto habilita a autenticação de conta da Microsoft do seu aplicativo.

	>[WACOM.NOTE]Para um registro do aplicativo Live Connect existente, você talvez tenha que primeiro habilitar a **Segurança de redirecionamento reforçada**.

4. Clique em **Configurações da API** e anote os valores da **ID do cliente** e **Segredo do cliente**. 

   	![][2]

    <div class="dev-callout"><b>Observação de Segurança</b>
	<p>O segredo do cliente é uma credencial de segurança importante. Não compartilhe o segredo do cliente com ninguém nem o distribua com seu aplicativo.</p>
    </div>

Agora você está pronto para usar uma Conta da Microsoft para autenticação em seu aplicativo, fornecendo os valores da ID do cliente e do segredo do cliente aos Serviços Móveis.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
[3]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png

<!-- URLs. -->

[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

<!--HONumber=35.1-->
