<properties 
	pageTitle="Como configurar a autenticação do Active Directory do Azure para seu aplicativo de Serviços de Aplicativos" 
	description="Saiba como configurar a autenticação do Active Directory do Azure para seu aplicativo de Serviços de Aplicativos." 
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
	ms.date="10/29/2015" 
	ms.author="mahender"/>

# Como configurar seu aplicativo para usar o logon do Active Directory do Azure

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Este tópico mostra como configurar os Serviços de Aplicativos do Azure para usar o Active Directory do Azure como um provedor de autenticação.

## <a name="register"> </a>Registrar seu aplicativo com o Active Directory do Azure

1. Faça logon no [Portal de Gerenciamento do Azure de Visualização] e navegue para o seu aplicativo móvel.

2. Em **Configurações**, clique em **Autenticação móvel** e em **Active Directory do Azure**. Copie a **URL do aplicativo** e a **URL de resposta** listada ali. Você as usará mais tarde. Verifique se a **URL do aplicativo** e a **URL de resposta** estão usando o esquema HTTPS.

    ![][1]

3. Entre no [Portal de Gerenciamento do Azure] e navegue até o **Active Directory**.

    ![][2]

4. Selecione o diretório e, em seguida, selecione a guia **Aplicativos** na parte superior. Clique em **ADICIONAR** na parte inferior para criar um novo registro de aplicativo.

5. Clique em **Adicionar um aplicativo que a minha organização está desenvolvendo**.

6. No Assistente de Adicionar Aplicativo, insira um **Nome** para seu aplicativo e clique no tipo **Aplicativo Web e/ou API Web**. Em seguida, clique em continuar.

7. Na caixa **URL DE LOGON**, cole a ID do aplicativo copiada nas configurações do provedor de identidade do Active Directory de seu aplicativo móvel. Insira o mesmo identificador de recurso na caixa **URI da ID do Aplicativo**. Em seguida, clique em continuar.

8. Depois que o aplicativo tiver sido adicionado, clique na guia **Configurar**. Edite o **URL de resposta** em **Logon único** para ser o URL de resposta dos aplicativos móveis copiados anteriormente. Deve ser o gateway do aplicativo móvel acrescido de _/signin-aad_. Por exemplo: `https://contosogateway.azurewebsites.net/signin-aad`. Certifique-se de que você está usando o esquema HTTPS.

    ![][3]

9. Clique em **Salvar**. Em seguida, copie a **ID do cliente** para o aplicativo.

## <a name="secrets"> </a>Adicionar informações do Active Directory do Azure para seu aplicativo móvel

1. Retornar ao portal de gerenciamento de visualização e à folha de configurações **Active Directory do Azure** do seu aplicativo móvel. Cole a configuração de **ID do Cliente** no provedor de identidade do Active Directory do Azure.
  
2. Na lista **Locatários permitidos**, você precisa adicionar o domínio do diretório no qual você registrou o aplicativo (por exemplo, contoso.onmicrosoft.com). Você pode encontrar o nome do domínio padrão clicando na guia **Domínios** no locatário do Active Directory do Azure. Adicione seu nome de domínio à lista **Locatários permitidos** depois clique em **Salvar**.

Agora você está pronto para usar um Active Directory do Azure para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

Autentique os usuários do aplicativo móvel com o logon único do Active Directory do Azure: [iOS][ios-adal]

<!-- Images. -->

[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[Portal de Gerenciamento do Azure de Visualização]: https://portal.azure.com/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-xamarin-ios-aad-sso.md

<!---HONumber=Nov15_HO2-->