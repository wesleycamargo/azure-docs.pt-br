<properties urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Registrar-se para autenticação do Active Directory do Azure - Serviços Móveis" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Saiba como se registrar para a autenticação do Active Directory do Azure em seu aplicativo de serviços móveis." title="Register your account to use an Azure Active Directory account login" authors="wesmc" services="mobile-services" documentationCenter="Mobile" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Registrar seus aplicativos para usar um logon de conta do Active Directory do Azure

Este tópico mostra como registrar seus aplicativos para poderem usar o Active Directory do Azure como um provedor de autenticação para os Serviços Móveis do Azure. 


>[AZURE.NOTE] As etapas descritas neste tópico devem ser usadas com o tutorial [Adicionar autenticação ao aplicativo de Serviços Móveis](/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/) quando você desejar usar [operações de logon voltadas a serviços](http://msdn.microsoft.com/pt-br/library/azure/dn283952.aspx) com o aplicativo. Como alternativa, se seu aplicativo exigir [operações de logon voltadas a cliente](http://msdn.microsoft.com/pt-br/library/azure/jj710106.aspx) para o Active Directory do Azure e um serviço móvel de back-end do .NET, você deve começar com o tutorial [Autenticar o seu aplicativo com Logon Único da biblioteca de autenticação do Active Directory](/pt-br/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/).


1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e depois no serviço móvel.

    ![][1]

2. Clique na guia **Identidade** do serviço móvel. 

    ![][2]

3. Role para baixo até a seção do **Active Directory do Azure** do provedor de identidade e copie a **URL do aplicativo** listada lá.

    ![][3]

4. Navegue para **Active Directory** no Portal de Gerenciamento e clique em seu diretório.

    ![][4] 

5. Clique na guia **Aplicativos** na parte superior e clique para **ADICIONAR** um aplicativo. 

    ![][10]

6. Clique em **Adicionar um aplicativo que minha organização está desenvolvendo**.

7. No Assistente de Adicionar Aplicativo, insira um **Nome** para seu aplicativo e clique no tipo   **Aplicativo Web e/ou API Web**. Em seguida, clique em continuar.

    ![][5]

8. Na caixa **URL DE LOGON**, cole a ID do aplicativo copiada nas configurações do provedor de identidade do Active Directory de seu serviço móvel. Insira o mesmo identificador de recurso exclusivo na caixa **URI da ID do Aplicativo**. Em seguida, clique em continuar.
 
    ![][6]


9. Depois que o aplicativo tiver sido adicionado, clique na guia **Configurar**. Em seguida, clique para copiar a **ID do cliente** do aplicativo.

    Se você criou o serviço móvel para usar o back-end do .Net para seu serviço móvel, além disso edite a **URL de resposta** em **Logon Único** para ser a URL do seu serviço móvel acrescentada ao caminho, _signin-aad_. Por exemplo, `https://todolist.azure-mobile.net/signin-aad`

    ![][8]


10. Retorne para a guia **Identidade** do serviço móvel. Na parte inferior, cole a configuração de **ID do Cliente** no provedor de identidade do Active Directory do Azure.

  
11. Na lista **Locatários permitidos**, você precisa adicionar o domínio do diretório no qual você registrou o aplicativo (por exemplo, contoso.onmicrosoft.com). Você pode encontrar seu nome de domínio padrão clicando na guia **Domínios** no seu Active Directory.

    ![][11]
 
    Adicione seu nome de domínio à lista **Locatários permitidos** depois clique em **Salvar**.    


    ![][9]



Agora você está pronto para usar um Active Directory do Azure para autenticação em seu aplicativo. 



<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
[2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
[3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
[4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
[5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
[6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
[7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-3-waad-auth.png
[8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
[9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
[10]:./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png
[11]:./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-default-domain.png

<!-- URLs. -->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

