<properties urlDisplayName="Authenticate with single sign-on" pageTitle="Autenticar seu aplicativo com o Live Connect (JavaScript)" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso, Azure JavaScript SSO" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="http://www.windowsazure.com/pt-br/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />

# Autenticar seu aplicativo da Windows Store com o logon único do Live Connect
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on/" title="Windows Store C#">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-single-sign-on/" title="Windows Store JavaScript" class="current">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-phone-single-sign-on/" title="Windows Phone">Windows Phone</a>
</div>	


Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo da Windows Store.  Neste tutorial, você adiciona autenticação ao projeto quickstart usando o Live Connect. Quando autenticado com êxito pelo Live Connect, um usuário conectado é recebido pelo nome e o valor da ID de usuário é exibido.  

>[WACOM.NOTE]Este tutorial demonstra os benefícios do uso da experiência de logon único fornecida pelo Live Connect para aplicativos da Windows Store. Isso permite que você autentique mais facilmente um usuário já conectado com seu serviço móvel. Para obter uma experiência de autenticação mais generalizada que ofereça suporte a vários provedores de autenticação, consulte o tópico <a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-users/">Introdução à autenticação</a>.

Este tutorial explica estas etapas básicas para habilitar a autenticação do Live Connect:

1. [Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]
2. [Restringir permissões de tabela para usuários autenticados]
3. [Adicionar autenticação ao aplicativo]

Este tutorial exige o seguinte:

+ [Live SDK para Windows]
+ Microsoft Visual Studio 2012 Express para Windows 8 RC ou uma versão posterior
+ Você deve primeiro concluir o tutorial [Adicionar Serviços Móveis a um aplicativo existente].

##<a name="register"></a>Registrar seu aplicativo para a Windows Store

Para poder autenticar usuários, você deve enviar seu aplicativo para a Windows Store. Você deve registrar o segredo do cliente para integrar o Live Connect com os Serviços Móveis.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

##<a name="permissions"></a>Restringir as permissões aos usuários autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>No Visual Studio 2012 Express para Windows 8, abra o projeto que você criou quando concluiu o tutorial <a href="/pt-br/documentation/articles/mobile-services-windows-store-get-started">Introdução aos Serviços Móveis</a>.</p></li> 
<li><p>Pressione a tecla F5 para executar este aplicativo baseado em inicialização rápida; verifique se uma exceção sem tratamento com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado.</p>
   
   	<p>Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela <em>TodoItem</em> agora requer autenticação.</p></li>
</ol>

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

1. Baixe e instale o [Live SDK para Windows].

2.  No menu **Projeto** do Visual Studio, clique em **Adicionar Referência**, em seguida, expanda **Windows**, clique em **Extensões**, selecione **Live SDK**e, em seguida, clique em **OK**. 

  	![][16]

  	Isso adiciona uma referência ao Live SDK ao projeto.

3. Abra o arquivo de projeto default.html e adicione o seguinte elemento &lt;script&gt; no elemento &lt;head&gt;. 

        <script src="///LiveSDKHTML/js/wl.js"></script>

   	Isso habilita o Microsoft IntelliSense no arquivo default.html.


4. Abra o arquivo de projeto default.js e adicione o seguinte comentário na parte superior do arquivo. 

        /// <reference path="///LiveSDKHTML/js/wl.js" />

   	Isso habilita o Microsoft IntelliSense no arquivo default.js.

5. Na sobrecarga do método **app.OnActivated**, substitua a chamada para o método **refreshTodoItems** pelo seguinte código: 
	
        var session = null;   

        var logout = function () {
            return new WinJS.Promise(function (complete) {
                WL.getLoginStatus().then(function () {
                    if (WL.canLogout()) {
                        WL.logout(complete);                            
                    }
                    else {
                        complete();
                    }
                });
            });
        };                  

        var login = function () {
            return new WinJS.Promise(function (complete) {                    
                WL.login({ scope: "wl.basic"}).then(function (result) {
                    session = result.session;

                    WinJS.Promise.join([
                        WL.api({ path: "me", method: "GET" }),
                        client.login(result.session.authentication_token)
                    ]).done(function (results) {
                        var profile = results[0];
                        var mobileServicesUser = results[1];
                        refreshTodoItems();
                        var title = "Welcome " + profile.first_name + "!";
                        var message = "You are now logged in as: " + mobileServicesUser.userId;
                        var dialog = new Windows.UI.Popups.MessageDialog(message, title);
                        dialog.showAsync().done(complete);                                
                    });                       
                }, function (error) {                        
                    session = null;
                    var dialog = new Windows.UI.Popups.MessageDialog("You must log in.", "Login Required");
                    dialog.showAsync().done(complete);                        
                });
            });
        }

        var authenticate = function () {
            // Force a logout to make it easier to test with multiple Microsoft Accounts
            logout().then(login).then(function () {
                if (session === null) {
                    // Authentication failed, try again.
                    authenticate();
                }
            });
        }

        WL.init({
            redirect_uri: "<< INSERT REDIRECT DOMAIN HERE >>"
        });           
            
        authenticate();

    Isso inicializa o cliente do Live Connect, força um logoff, envia uma nova solicitação de logon ao Live Connect, envia o token de autenticação retornado aos Serviços Móveis e, em seguida, exibe informações sobre o usuário conectado. Esse código força um logoff, quando possível, para verificar se o usuário é solicitado por credenciais cada vez que o aplicativo for executado. Isso facilita o teste do aplicativo com diferentes Contas da Microsoft para garantir que a autenticação esteja funcionando corretamente. Esse mecanismo só funcionará se o usuário conectado não tiver uma conta da Microsoft conectada.

	>[WACOM.NOTE]Você não deve solicitar os tokens de autenticação do Live Connection ou tokens de autenticação dos Serviços Móveis todas as vezes que o seu aplicativo executar. Além disto ser ineficiente, você pode se deparar com problemas relacionados ao uso caso muitos consumidores tentem iniciar o aplicativo ao mesmo tempo. Uma abordagem melhor é armazenar em cache os tokens e primeiro tentar usar o token dos Serviços Móveis em cache antes de chamar **LoginWithMicrosoftAccountAsync**. Para obter um exemplo de como armazenar em cache este token, consulte [Introdução à autenticação](/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-users/#tokens)
	
7. Atualize a cadeia de caracteres _<< INSERT REDIRECT DOMAIN HERE >>_ da etapa anterior com o domínio de redirecionamento especificado ao configurar o aplicativo Live Connect no formato **https://_service-name_.azure-mobile.net/**.
		
8. Pressione a tecla F5 para executar o aplicativo e entrar no Live Connect com sua Conta da Microsoft. 

   	Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorizar usuários com scripts], você obterá o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usar para filtrar os dados retornados pelos Serviços Móveis. Para obter informações sobre como usar outros provedores de identidade para autenticação, consulte [Introdução à autenticação].

<!-- Anchors. -->
[Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-select-app-name.png
[4]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-service-uri.png
[6]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-app-api-settings.png





[13]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-add-reference-live-js.png

<!-- URLs. -->
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Adicionar Serviços Móveis a um aplicativo existente]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-users
[Autorizar usuários com scripts]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts/

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

<!--HONumber=35.1-->
