<properties 
	pageTitle="Registrar-se para autenticação do Active Directory do Azure - Serviços Móveis" 
	description="Saiba como se registrar para a autenticação do Active Directory do Azure em seu aplicativo de serviços móveis." 
	authors="wesmc7777" 
	services="mobile-services" 
	documentationCenter="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="wesmc"/>

# Registrar seus aplicativos para usar um logon de conta do Active Directory do Azure

Este tópico mostra como registrar seus aplicativos para poderem usar o Active Directory do Azure como um provedor de autenticação para os Serviços Móveis do Azure. 


>[AZURE.NOTE] As etapas descritas neste tópico devem ser usados com o tutorial [Adicionar Autenticação ao aplicativo de Serviços Móveis](/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/) quando você quiser usar [Operações de logon voltadas ao serviço](http://msdn.microsoft.com/library/azure/dn283952.aspx) com seu aplicativo. Como alternativa, se seu aplicativo exigir [operações de logon voltadas ao cliente](http://msdn.microsoft.com/library/azure/jj710106.aspx) para o Active Directory do Azure e um serviço móvel de back-end do .NET, você deve começar com o tutorial [Autenticar o aplicativo com logon único da biblioteca de autenticação do Active Directory](/pt-br/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/).


1. Entre no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis**, e, em seguida, clique em seu serviço móvel.

    ![][1]

2. Clique na guia **Identidade** de seu serviço móvel. 

    ![][2]

3. Role para baixo até a seção do provedor de identidade **Active Directory do Azure** e copie a **URL do aplicativo** aí listada.

    ![][3]

4. Navegue até **Active Directory** no portal de gerenciamento e clique em seu diretório.

    ![][4] 	

5. Clique na guia **Aplicativos** na parte superior e clique em **ADICIONAR** um aplicativo. 

    ![][10]

6. Clique em **Adicionar um aplicativo que minha organização está desenvolvendo**.

7. No Assistente Adicionar Aplicativo, insira um **Nome** para seu aplicativo e clique no tipo **Aplicativo Web e/ou API Web**. Em seguida, clique em continuar.

    ![][5]

8. Na caixa **URL DE LOGON**, cole a ID do aplicativo copiada nas configurações do provedor de identidade do Active Directory de seu serviço móvel. Insira o mesmo identificador de recurso exclusivo na caixa **URI da ID do Aplicativo**. Em seguida, clique em continuar.
 
    ![][6]


9. Depois que o aplicativo tiver sido adicionado, clique na guia **Configurar**. Em seguida, clique para copiar a **ID do cliente** do aplicativo.

    Se você criou o serviço móvel para usar o back-end do .Net para seu serviço móvel, edite também a **URL de Resposta** em **Logon Único** para ser a URL do seu serviço móvel acrescentada ao caminho, _signin-aad_. Por exemplo, `https://todolist.azure-mobile.net/signin-aad`

    ![][8]


10. Retorne à guia **Identidade** do serviço móvel. Na parte inferior, cole a configuração de **ID do Cliente** no provedor de identidade do Active Directory do Azure.

  
11. Na lista **Locatários Permitidos**, você precisa adicionar o domínio do diretório no qual registrou o aplicativo (por exemplo, contoso.onmicrosoft.com). Você pode encontrar o nome do domínio padrão clicando na guia **Domínios** do Active Directory.

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


<!--HONumber=42-->
