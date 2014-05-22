<properties linkid="develop-mobile-how-to-guides-register-for-microsoft-waad-authentication" urlDisplayName="Registrar-se para autenticação do Active Directory do Azure" pageTitle="Registrar-se para autenticação do Active Directory do Azure - Serviços Móveis" metaKeywords="aplicativo de registro do Azure, autenticação do Active Directory do Azure, aplicativo de autenticação, autenticação dos serviços móveis" description="Saiba como registrar-se para autenticação do Active Directory do Azure em seu aplicativo de Serviços Móveis do Azure." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Registrar seus aplicativos para usarem um logon de Conta do Active Directory do Azure" authors="" />


# Registrar seus aplicativos para usar um logon de conta do Active Directory do Azure

Este tópico mostra como registrar seus aplicativos para poderem usar o Active Directory do Azure como um provedor de autenticação para os Serviços Móveis do Azure. 

<div class="dev-callout"><b>Observação</b>
<p>Quando você também planeja fornecer autenticação controlada por cliente para SSO (logon único) ou enviar notificações por push de um aplicativo da Windows Store, considere também registrar seu aplicativo na Windows Store. Para obter mais informações, consulte <a href="/pt-br/develop/mobile/how-to-guides/register-for-single-sign-on">Registrar seus aplicativos da Windows Store para autenticação do Windows Live Connect</a>.</p>
</div>

1.  Faça logon no [Portal de Gerenciamento do Azure]. 

2. Navegue para **Active Directory** no Portal de Gerenciamento e clique em seu diretório.

   ![][1] 

3. Clique na guia **Aplicativos** e, em seguida, clique em **Adicionar um Aplicativo**. 

   ![][2]


4. Siga as instruções do assistente de novo aplicativo escolhendo **Aplicativo Web e/ou API Web** para o XXX. Habilite o Logon Único Quando a**URL do Aplicativo** for solicitada, cole a URL do aplicativo de serviços móveis.


5. *** MAIS A SER INCLUÍDO ***


Agora você está pronto para usar um Active Directory do Azure para autenticação em seu aplicativo fornecendo os valores de ID do cliente e de segredo do cliente aos Serviços Móveis.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-app-active-directory-login/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-app-active-directory-login/mobile-live-connect-app-api-settings.png

<!-- URLs. -->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

