<properties 
	pageTitle="Registrar para autenticação Microsoft - Serviços Móveis" 
	description="Saiba como registrar-se para autenticação da Microsoft em seu aplicativo de serviços móveis do Azure." 
	authors="ggailey777" 
	services="mobile-services" 
	documentationCenter="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Registrar seus aplicativos para usar um logon de Conta da Microsoft

Este tópico mostra como registrar seu aplicativo para usar o Live Connect como um provedor de autenticação para os Serviços Móveis do Azure. 

>[AZURE.NOTE]Para configurar a autenticação de Conta da Microsoft para um aplicativo Windows universal ou para fornecer uma experiência de logon único para um aplicativo da Windows Store, consulte [Registrar seu pacote do aplicativo da Windows Store para a autenticação da Microsoft](/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication).

1. Navegue até o site <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Meus Aplicativos</a> na Central de Desenvolvedores do Live Connect e faça logon com sua conta da Microsoft, se necessário. 

2. Clique em **Criar aplicativo**, digite um **Nome de aplicativo** e clique em **Aceito**.

   	![][1] 

   	Isso registra o aplicativo no Live Connect.

3. Clique em **configurações da API**, forneça um valor de `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` na **URL de redirecionamento**, em seguida, clique em **Salvar**.

	>[AZURE.NOTE]Para um serviço móvel de back-end do .NET publicado no Azure usando o Visual Studio, a URL de redirecionamento é a URL do seu serviço móvel anexada com o caminho _signin-microsoft_ do seu serviço móvel como um serviço .NET, por exemplo, <code>https://todolist.azure-mobile.net/signin-microsoft</code>.  

	![][3]

	Isto habilita a autenticação de conta da Microsoft do seu aplicativo.

	>[AZURE.NOTE]Para um registro do aplicativo Live Connect existente, você talvez tenha que primeiro habilitar a **Segurança de redirecionamento reforçada**.

4. Clique em **Configurações do Aplicativo** e anote os valores de **ID do Cliente** e **Segredo do Cliente**. 

   	![][2]

    > [AZURE.NOTE] O segredo do cliente é uma credencial de segurança importante. Não compartilhe o segredo do cliente com ninguém nem o distribua com seu aplicativo.

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

<!--HONumber=47-->
