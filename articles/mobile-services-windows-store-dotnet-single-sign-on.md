<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-dotnet" urlDisplayName="Autenticar com logon único" pageTitle="Autenticar seu aplicativo da Windows Store com o Live Connect" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, serviços móveis sso, aplicativo da Windows Store sso" description="Saiba como usar o logon único do Live Connect nos Serviços Móveis do Azure em um aplicativo da Windows Store." metaCanonical="" services="" documentationCenter="" title="Autenticar seu aplicativo da Windows Store com o logon único do Live Connect" authors=""  solutions="" writer="glenga" manager="" editor=""  />




# Autenticar seu aplicativo da Windows Store com o logon único do Live Connect
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/pt-br/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="Windows Store C#" class="current">Windows Store C#</a><a href="/pt-br/develop/mobile/tutorials/single-sign-on-windows-8-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/pt-br/develop/mobile/tutorials/single-sign-on-wp8" title="Windows Phone">Windows Phone</a>
</div>	


Este tópico mostra como usar o logon único do Live Connect para autenticar usuários nos Serviços Móveis do Azure em um aplicativo da Windows Store.  Neste tutorial, você adiciona autenticação ao projeto quickstart usando o Live Connect. Quando autenticado com êxito pelo Live Connect, um usuário conectado é recebido pelo nome e o valor da ID do usuário é exibido.  

<div class="dev-callout"><b>Observação</b>
	<p>Este tutorial demonstra os benefícios do uso da experiência de logon único fornecida pelo Live Connect para aplicativos da Windows Store. Isso permite que você autentique mais facilmente um usuário já conectado com seu serviço móvel. Para obter uma experiência de autenticação mais generalizada que ofereça suporte a vários provedores de autenticação, consulte o tópico <a href="/pt-br/develop/mobile/tutorials/get-started-with-users-dotnet/">Introdução à autenticação</a>. </p>
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

1. Se você não tiver registrado seu aplicativo ainda, navegue até o <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">Enviar uma página de aplicativo</a> no Centro de Desenvolvimento de aplicativos da Windows Store, faça logon com sua conta da Microsoft e, em seguida, clique em **Nome do aplicativo**.

   	![][0]

2. Digite um nome para seu aplicativo em **Nome do aplicativo**, clique em **Reservar nome do aplicativo** e, em seguida, clique em **Salvar**.

   	![][1]

   	Isso cria um novo registro da Windows Store para seu aplicativo.

3. No Visual Studio 2012 Express para Windows 8, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis].

4. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto, clique em **Armazenar** e, em seguida, clique em **Associar o aplicativo à Loja....** 

  	![][2]

   	Isso exibe o Assistente de **Associar o Aplicativo à Loja do Windows**.

5. No assistente, clique em **Entrar** e, em seguida, faça logon com sua conta da Microsoft.

6. Selecione o aplicativo que você registrou na etapa 2, clique em **Avançar** e, em seguida, clique em **Associar**.

   	![][3]

   	Isso adiciona as informações de registro da Windows Store necessárias para o manifesto do aplicativo.    

7. Faça o logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu serviço móvel.

   	![][4]

8. Clique no **Painel** e anote o valor da **URL do Site**.

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

16. De volta ao Portal de Gerenciamento, clique na guia **identidade**, insira o **Segredo do cliente** obtido da Windows Store e clique em **Salvar**.

   	![][13]

O serviço móvel e seu aplicativo agora estão configurados para trabalhar com o Live Connect.

<h2><a name="permissions"></a><span class="short-header">Restringir permissões</span>Restringir permissões para usuários autenticados</h2>

1. No Portal de Gerenciamento, clique na guia **Dados** e na tabela **TodoItem**. 

   	![][14]

2. Clique na guia **Permissões**, defina todas as permissões para **Somente usuários autenticados** e clique em **Salvar**. Isso garantirá que todas as operações em relação a tabela **TodoItem** exigem um usuário autenticado. Isso também simplifica os scripts no próximo tutorial porque eles não terão que permitir a possibilidade de usuários anônimos.

   	![][15]

3. No Visual Studio 2012 Express para Windows 8, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis]. 

4. Pressione a tecla F5 para executar este aplicativo baseado em início rápido; verifique se uma exceção com um código de status de 401 (não autorizado) é gerada. 
   
   	Isso acontece porque o aplicativo está acessando os Serviços Móveis como um usuário não autenticado, mas a tabela _TodoItem_ agora exige autenticação.

Em seguida, você irá atualizar o aplicativo para autenticar usuários com o Live Connect antes de solicitar recursos do serviço móvel.

<h2><a name="add-authentication"></a><span class="short-header">Adicionar autenticação</span>Adicionar autenticação ao aplicativo</h2>

1. Baixe e instale o [Live SDK para Windows].

2. No menu **Projeto** do Visual Studio, clique em **Adicionar Referência**, em seguida, expanda **Windows**, clique em **Extensões**, selecione **Live SDK**e, em seguida, clique em **OK**. 

  	![][16]

  	Isso adiciona uma referência ao Live SDK ao projeto.

5. Abra o arquivo do projeto MainPage.xaml.cs e adicione a seguinte instrução using:

        using Microsoft.Live;        

6. Adicione o seguinte trecho de código à classe MainPage:
	
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task Authenticate()
        {
            LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT REDIRECT DOMAIN HERE >>");

            while (session == null)
            {
                // Force a logout to make it easier to test with multiple Microsoft Accounts
                if (liveIdClient.CanLogout)
                    liveIdClient.Logout();
	
                LiveLoginResult result = await liveIdClient.LoginAsync(new[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;
                    LiveConnectClient client = new LiveConnectClient(result.Session);
                    LiveOperationResult meResult = await client.GetAsync("me");
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);
	
                    string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                    var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                    var dialog = new MessageDialog(message, title);
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
                else
                {
                    session = null;
                    var dialog = new MessageDialog("You must log in.", "Login Required");
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
            }
         }

    Isso cria uma variável de membro para armazenar a sessão do Live Connect atual e um método para manipular o processo de autenticação.

    <div class="dev-callout"><b>Observação</b>
	<p>Esse código força um logoff, quando possível, para verificar se o usuário é solicitado por credenciais cada vez que o aplicativo for executado. Isso facilita o teste do aplicativo com diferentes Contas da Microsoft para garantir que a autenticação esteja funcionando corretamente. Esse mecanismo só funcionará se o usuário conectado não tiver uma conta da Microsoft conectada.</p>
    </div>
	

7. Atualize a cadeia de caracteres _<< INSERT REDIRECT DOMAIN HERE >>_ da etapa anterior com o domínio de redirecionamento especificado ao configurar o aplicativo no Live Connect, no formato **https://_service-name_.azure-mobile.net/**.

    <div class="dev-callout"><b>Observação</b>
	<p>Em um aplicativo da Windows Store, uma instância da classe <strong>LiveAuthClient</strong> é criada, passando o valor do domínio de redirecionamento para o construtor de classe. Em um <a href="/pt-br/develop/mobile/tutorials/single-sign-on-wp8/">aplicativo do Windows Phone 8</a>, a mesma classe é instanciada passando a ID do cliente.</p>
    </div>

8. Substitua o manipulador de eventos **OnNavigatedTo** pelo manipulador que chama o novo método **Authenticate**:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }
		
9. Pressione a tecla F5 para executar o aplicativo e entrar no Live Connect com sua Conta da Microsoft. 

   Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorizar usuários com scripts], você irá obter o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usar para filtrar os dados retornados pelos Serviços Móveis. Para obter informações sobre como usar outros provedores de identidade para autenticação, consulte [Introdução à autenticação]. Saiba mais sobre como usar os Serviços Móveis com .NET no [Referência conceitual do tutorial dos Serviços Móveis .NET]

<!-- Anchors. -->
[Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-select-app-name.png
[4]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-service-uri.png
[6]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-app-api-settings.png





[13]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-add-reference-live-dotnet.png

<!-- URLs. -->
[Enviar uma página de aplicativo:]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-dotnet
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-dotnet
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-users-js

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência conceitual do tutorial dos Serviços Móveis .NET]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library

