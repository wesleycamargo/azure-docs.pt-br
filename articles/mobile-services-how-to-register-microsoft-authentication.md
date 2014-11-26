<properties pageTitle="Register for Microsoft authentication - Mobile Services" metaKeywords="Azure registering application, Azure Microsoft authentication, application authenticate, authenticate mobile services" description="Learn how to register for Microsoft authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use a Microsoft Account login" authors="glenga" services="mobile-services" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Registrar seus aplicativos para usar um logon de Conta da Microsoft

Este tópico mostra como registrar seu aplicativo para poder usar o Live Connect como um provedor de autenticação para os Serviços Móveis do Azure.

> [WACOM.NOTE]Para configurar a autenticação da conta da Microsoft para um aplicativo do Windows 8.1 ou Windows Phone 8.1, consulte [Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft][Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft].

1.  Navegue até a página [Meus Aplicativos][Meus Aplicativos] na Central de Desenvolvedores do Live Connect e faça logon com sua conta da Microsoft, se necessário.

2.  Clique em **Criar aplicativo**, digite um **Nome de aplicativo** e clique em **Aceito**.

    ![][0]

    Isso registra o aplicativo no Live Connect.

3.  Clique em **Configurações de API**, forneça um valor de `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` em **URL de redirecionamento** e clique em **Salvar**.

    > [WACOM.NOTE]Para um serviço móvel de back-end do .NET publicado no Azure usando o Visual Studio, a URL de redirecionamento é a URL do seu serviço móvel acrescentada ao caminho *signin-microsoft* e o seu serviço móvel é um serviço .NET, por exemplo, . `https://todolist.azure-mobile.net/signin-microsoft`.

    ![][1]

    Isto habilita a autenticação de conta da Microsoft do seu aplicativo.

    > [WACOM.NOTE]Para um registro do aplicativo Live Connect existente, você talvez tenha que primeiro habilitar a **Segurança de redirecionamento reforçada**.

4.  Clique em **Configurações da API** e anote os valores da **ID do cliente** e **Segredo do cliente**.

    ![][2]

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o de Seguran&ccedil;a</b>
<p>O segredo do cliente &eacute; uma credencial de seguran&ccedil;a importante. N&atilde;o compartilhe o segredo do cliente com ningu&eacute;m nem o distribua com seu aplicativo.</p>
</div>

Agora você está pronto para usar uma Conta da Microsoft para autenticação em seu aplicativo, fornecendo os valores da ID do cliente e do segredo do cliente aos Serviços Móveis.

 
 


  [Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft]: /pt-br/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
  [Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  [0]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
  [1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png
  [2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
