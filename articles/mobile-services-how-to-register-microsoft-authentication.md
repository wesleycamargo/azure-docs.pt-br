<properties pageTitle="Registre-se na autenticação da Microsoft - Serviços Móveis" metaKeywords="Azure registrando aplicativo, autenticação do Microsoft Azure, aplicativo autenticar, autenticar serviços móveis" description="Saiba como registrar-se para autenticação da Microsoft em seu aplicativo de Serviços Móveis do Azure." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Registrar seus aplicativos para usarem um logon de Conta da Microsoft" authors="" />

# Registrar seus aplicativos para usar um logon de Conta da Microsoft

Este tópico mostra como registrar seus aplicativos para poderem usar o Live Connect como um provedor de autenticação para os Serviços Móveis do Azure. 

>[WACOM.NOTE]Quando você também planeja fornecer autenticação controlada por cliente para SSO (logon único) ou enviar notificações por push de um aplicativo da Windows Store, considere também registrar seu aplicativo na Windows Store. Para obter mais informações, consulte <a href="/pt-br/develop/mobile/how-to-guides/register-for-single-sign-on">Registrar seus aplicativos da Windows Store para autenticação do Windows Live Connect</a>.


1. Navegue até a página <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Meus Aplicativos</a> na Central de Desenvolvedores do Live Connect e faça logon com sua conta da Microsoft, se necessário. 

2. Clique em **Criar aplicativo**, digite um **Nome de aplicativo** e clique em **Aceito**.

   	![][1] 

   	Isso registra o aplicativo no Live Connect.

10. Clique na **página Configurações do aplicativo**, em seguida, em **Configurações da API** e anote os valores da **ID do Cliente** e **Segredo do Cliente**. 

   	![][2]

    <div class="dev-callout"><b>Observação de Segurança</b>
	<p>O segredo do cliente é uma credencial de segurança importante. Não compartilhe o segredo do cliente com ninguém nem o distribua com seu aplicativo.</p>
    </div>

11. Em **Domínio de redirecionamento**, digite a URL do seu serviço móvel e clique em **Salvar**.

Agora você está pronto para usar uma Conta da Microsoft para autenticação em seu aplicativo, fornecendo os valores da ID do cliente e do segredo do cliente aos Serviços Móveis.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png
<!-- URLs. -->
[Logon único para aplicativos da Windows Store usando o Live Connect]: /pt-br/develop/mobile/how-to-guides/register-for-single-sign-on/
[Enviar uma página de aplicativo:]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet/
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-dotnet/
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-users-js/

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

