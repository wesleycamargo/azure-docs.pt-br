<properties linkid="develop-mobile-how-to-guides-register-for-microsoft-waad-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use an Azure Active Directory Account login" authors="dwrede" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="dwrede" />

# Registrar seus aplicativos para usar um logon de conta do Active Directory do Azure

Este tópico mostra como registrar seus aplicativos para poderem usar o Active Directory do Azure como um provedor de autenticação para os Serviços Móveis do Azure.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Quando voc&ecirc; tamb&eacute;m planeja fornecer autentica&ccedil;&atilde;o controlada por cliente para SSO (logon &uacute;nico) ou enviar notifica&ccedil;&otilde;es por push de um aplicativo da Windows Store, considere tamb&eacute;m registrar seu aplicativo na Windows Store. Para obter mais informa&ccedil;&otilde;es, consulte <a href="/pt-br/develop/mobile/how-to-guides/register-for-single-sign-on">Registrar seus aplicativos da Windows Store para autentica&ccedil;&atilde;o do Windows Live Connect</a>.</p>
</div>

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

2.  Navegue para **Active Directory** no Portal de Gerenciamento e clique em seu diretório.

	![][0]

1.  Clique na guia **Aplicativos** e, em seguida, clique em **Adicionar um Aplicativo**.

	![][1]

1.  Siga as instruções do assistente de novo aplicativo escolhendo **Aplicativo Web e/ou API Web** para o XXX. Habilite o Logon Único. Quando a**URL do Aplicativo** for solicitada, cole a URL do aplicativo de serviços móveis.

2.  ** MAIS A SER INCLUÍDO **

Agora você está pronto para usar um Active Directory do Azure para autenticação em seu aplicativo fornecendo os valores de ID do cliente e de segredo do cliente aos Serviços Móveis.

 
 


  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-app-active-directory-login/mobile-services-live-connect-add-app.png
  [1]: ./media/mobile-services-app-active-directory-login/mobile-live-connect-app-api-settings.png
