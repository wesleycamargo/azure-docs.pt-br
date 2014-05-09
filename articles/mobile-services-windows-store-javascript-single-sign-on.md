<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-js" urlDisplayName="Autenticar com logon único" pageTitle="Autenticar seu aplicativo com o Live Connect (JavaScript)" metaKeywords="Azure Live Connect, SSO do Azure, SSO Live Connect, sso dos serviços móveis, sso de aplicativo da Windows Store, sso de JavaScript do Azure" description="Saiba como usar o logon único do Live Connect nos Serviços Móveis do Azure em um aplicativo da Windows Store." metaCanonical="http://www.windowsazure.com/pt-br/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/" services="" documentationCenter="Mobile" title="Autenticar seu aplicativo da Windows Store com o logon único do Live Connect" authors="" solutions="" manager="" editor="" />




# Autenticar seu aplicativo da Windows Store com o logon único do Live Connect
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/pt-br/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="C# da Windows Store">C# da Windows Store#</a><a href="/pt-br/develop/mobile/tutorials/single-sign-on-windows-8-js" title="JavaScript da Windows Store" class="current">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/single-sign-on-wp8" title="Windows Phone">Windows Phone</a>
</div>	


Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo da Windows Store.  Neste tutorial, você adiciona autenticação ao projeto quickstart usando o Live Connect. Quando autenticado com êxito pelo Live Connect, um usuário conectado é recebido pelo nome e o valor da ID do usuário é exibido.  

<div class="dev-callout"><b>Observação</b>
	<p>Este tutorial demonstra os benefícios do uso da experiência de logon único fornecida pelo Live Connect para aplicativos da Windows Store. Isso permite que você autentique mais facilmente um usuário já conectado com seu serviço móvel. Para obter uma experiência de autenticação mais generalizada que ofereça suporte a vários provedores de autenticação, consulte o tópico <a href="/pt-br/develop/mobile/tutorials/get-started-with-users-js/">Introdução à autenticação</a>. </p>
</div>

Este tutorial explica estas etapas básicas para autenticação do Live Connect:

1. [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]
2. [Restringir permissões de tabela para usuários autenticados]
3. [Adicionar autenticação ao aplicativo]

Este tutorial exige o seguinte:

+ [Live SDK para Windows]
+ Microsoft Visual Studio 2012 Express para Windows 8 RC ou uma versão posterior

Este tutorial baseia-se no guia de início rápido dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis].

<h2><a name="register"></a><span class="short-header">Registrar seu aplicativo</span>Registrar seu aplicativo para a Windows Store</h2>

Para poder autenticar usuários, você deve enviar seu aplicativo para a Windows Store. Você deve registrar o segredo do cliente para integrar o Live Connect com os Serviços Móveis.

1. Se você não tiver registrado seu aplicativo, navegue até a página <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">Enviar uma página do aplicativo</a> no Centro de Desenvolvimento de aplicativos da Windows Store, faça logon com sua conta da Microsoft e, em seguida, clique em **Nome do aplicativo**.

   	![][0]

2. Digite um nome para seu aplicativo em **Nome do aplicativo**, clique em **Reservar nome do aplicativo** e, em seguida, clique em **Salvar**.

   	![][1]

   	Isso cria um novo registro da Windows Store para seu aplicativo.

3. No Visual Studio 2012 Express para Windows 8, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis].

4. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto, clique em **Loja** e clique em **Associar o Aplicativo à Loja...**. 

  	![][2]

   	Isso exibe o assistente **Associar seu aplicativo à Windows Store**.

5. No assistente, clique em **Entrar** e, em seguida, faça logon com sua conta da Microsoft.

6. Selecione o aplicativo que você registrou na etapa 2, clique em **Avançar** e, em seguida, clique em **Associar**.

   	![][3]

   	Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.    

7. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

   	![][4]

8. Clique na guia **Painel** e anote o valor da **URL do Site**.

   	![][5]

    Você usará esse valor para definir o domínio de redirecionamento.

9. Navegue até a página <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Meus Aplicativos</a> na Central de Desenvolvedores do Live Connect e clique em seu aplicativo na lista **Meus aplicativos**.

   	![][6] 

10. Clique na página **Editar configurações**, em seguida, em **Configurações da API** e anote os valores de **ID do cliente** e **Segredo do cliente**. 

   	![][7]

    <div class="dev-callout"><b>Observação de Segurança</b>
	<p>O segredo do cliente é uma credencial de segurança importante. Não compartilhe o segredo do cliente com ninguém nem o distribua com seu aplicativo.</p>
    </div>

11. Em **Domínio de redirecionamento**, digite a URL do seu serviço móvel da Etapa 8 e, em seguida, clique em **Salvar**.

12. De volta ao Portal de Gerenciamento, clique na guia **Identidade**, insira o **Segredo do cliente** obtido da Windows Store e clique em **Salvar**.

   	![][13]

O serviço móvel e seu aplicativo agora estão configurados para trabalhar com o Live Connect.

<h2><a name="permissions"></a><span class="short-header">Restringir permissões</span>Restringir permissões para usuários autenticados</h2>

1. No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**. 

   	![][14]

2. Clique na guia **Permissões**, defina todas as permissões como **Somente usuários autenticados** e clique em **Salvar**. Isso garantirá que todas as operações em relação a tabela **TodoItem** exigem um usuário autenticado. Isso também simplifica os scripts no próximo tutorial porque eles não terão que permitir a possibilidade de usuários anônimos.

   	![][15]

3. No Visual Studio 2012 Express para Windows 8, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis]. 

4. Pressione a tecla F5 para executar esse aplicativo baseado no quickstart; verifique se uma exceção com código de status 401 (Não autorizado) é gerada. 
   
   	Isso acontece porque o aplicativo está acessando os Serviços Móveis como um usuário não autenticado, mas a tabela _TodoItem_ agora exige autenticação.

Em seguida, você irá atualizar o aplicativo para autenticar usuários com o Live Connect antes de solicitar recursos do serviço móvel.

<h2><a name="add-authentication"></a><span class="short-header">Adicionar autenticação</span>Adicionar autenticação ao aplicativo</h2>

1. Baixe e instale o [Live SDK para Windows].

2. No menu **Projeto** do Visual Studio, clique em **Adicionar Referência**, em seguida, expanda **Windows**, clique em **Extensões**, selecione **Live SDK**e, em seguida, clique em **OK**. 

  	![][16]

  	Isso adiciona uma referência ao Live SDK ao projeto.

3. Abra o arquivo do projeto default.html e adicione o seguinte elemento &lt;script&gt; no elemento &lt;head&gt;. 

        <script src="///LiveSDKHTML/js/wl.js"></script>

   	Isso habilita o Microsoft IntelliSense no arquivo default.html.


4. Abra o arquivo do projeto default.js e adicione o seguinte comentário na parte superior do arquivo. 

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

    Isso inicializa o cliente do Live Connect, força um logoff, envia uma nova solicitação de logon ao Live Connect, envia o token de autenticação retornado aos Serviços Móveis e, em seguida, exibe informações sobre o usuário conectado. 

    <div class="dev-callout"><b>Observação</b>
	<p>Esse código força um logoff, quando possível, para verificar se o usuário é solicitado por credenciais cada vez que o aplicativo for executado. Isso facilita o teste do aplicativo com diferentes Contas da Microsoft para garantir que a autenticação esteja funcionando corretamente. Esse mecanismo só funcionará se o usuário conectado não tiver uma conta da Microsoft conectada.</p>
    </div>
	
7. Atualize a cadeia de caracteres _<< INSERT REDIRECT DOMAIN HERE >>_ da etapa anterior com o domínio de redirecionamento especificado ao configurar o aplicativo no Live Connect, no formato **https://_service-name_.azure-mobile.net/**.
		
8. Pressione a tecla F5 para executar o aplicativo e entrar no Live Connect com sua Conta da Microsoft. 

   	Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorizar usuários com scripts], você irá obter o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usar para filtrar os dados retornados pelos Serviços Móveis. Para obter informações sobre como usar outros provedores de identidade para autenticação, consulte [Introdução à autenticação].

<!-- Anchors. -->
[Registrar seu aplicativo para autenticação e configurar os Serviços Móveis]: #register
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
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-js
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-js
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-js
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-users-js

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

