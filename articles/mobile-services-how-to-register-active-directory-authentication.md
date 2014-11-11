<properties linkid="develop-mobile-how-to-guides-register-for-active-directory-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Mobile Services application." title="Register your account to use an Azure Active Directory account login" authors="wesmc" services="mobile-services" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Registrar seus aplicativos para usar um logon de conta do Active Directory do Azure

Este tópico mostra como registrar seus aplicativos para poderem usar o Active Directory do Azure como um provedor de autenticação para os Serviços Móveis do Azure.

> [WACOM.NOTE] Se você deseja fornecer autenticação controlada por cliente para o Logon Único (SSO) com o Active Directory do Azure, consulte o tutorial [Autenticar o seu aplicativo com Logon Único com a biblioteca de autenticação do Active Directory][Autenticar o seu aplicativo com Logon Único com a biblioteca de autenticação do Active Directory].

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

    ![][0]

2.  Clique na guia **Identidade** de seu serviço móvel.

    ![][1]

3.  Role para baixo até a seção do provedor de identidade do **Active Directory do Azure** e copie a **URL do aplicativo** da lista.

    ![][2]

4.  Navegue para **Active Directory** no Portal de Gerenciamento e clique em seu diretório.

    ![][3]

5.  Clique na guia **Aplicativos** na parte superior e clique para **ADICIONAR** um aplicativo.

    ![][4]

6.  Clique em **Adicionar um aplicativo que a minha organização está desenvolvendo**.

7.  No Assistente de Adicionar Aplicativo, insira um **Nome** para seu aplicativo e clique no tipo **Aplicativo Web e/ou API Web**. Em seguida, clique em continuar.

    ![][5]

8.  Na caixa **URL DE LOGON**, cole a ID do aplicativo copiada nas configurações do provedor de identidade do Active Directory de seu serviço móvel. Insira o mesmo identificador de recurso exclusivo na caixa **URI da ID do Aplicativo**. Em seguida, clique em continuar.

    ![][6]

9.  Depois que o aplicativo tiver sido adicionado, clique na guia **Configurar**. Em seguida, clique para copiar a **ID do cliente** do aplicativo.

    Se você criou o serviço móvel para usar o back-end do .Net para seu serviço móvel, além disso edite a **URL de resposta** em **Logon Único** para ser a URL do seu serviço móvel acrescentada ao caminho, *signin-aad*. Por exemplo, `https://todolist.azure-mobile.net/signin-aad`

    ![][7]

10. Retorne para a guia **Identidade** do serviço móvel. Na parte inferior, cole a configuração de **ID do Cliente** no provedor de identidade do Active Directory do Azure.

11. Na lista **Locatários permitidos**, você precisa adicionar o domínio do diretório no qual você registrou o aplicativo (por exemplo, contoso.onmicrosoft.com). Você pode encontrar seu nome de domínio padrão clicando na guia **Domínios** no seu Active Directory.

    ![][8]

    Adicione seu nome de domínio à lista **Locatários permitidos** depois clique em **Salvar**.

    ![][9]

Agora você está pronto para usar um Active Directory do Azure para autenticação em seu aplicativo.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Autenticar o seu aplicativo com Logon Único com a biblioteca de autenticação do Active Directory]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
  [1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
  [2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
  [3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
  [4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png
  [5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
  [6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
  [7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
  [8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-default-domain.png
  [9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
