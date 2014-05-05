<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-wp8" urlDisplayName="Autenticar com o logon único" pageTitle="Autenticar seu aplicativo com o Live Connect (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Saiba como usar o logon único do Live Connect nos Serviços Móveis do Azure de um aplicativo do Windows Phone." metaCanonical="" services="" documentationCenter="Mobile" title="Autenticar seu aplicativo do Windows Phone 8 com o logon único do Live Connect" authors="glenga" solutions="" manager="" editor="" />




# Autenticar seu aplicativo do Windows Phone 8 com o logon único do Live Connect

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/pt-br/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/develop/mobile/tutorials/single-sign-on-windows-8-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/pt-br/develop/mobile/tutorials/single-sign-on-wp8/" title="Windows Phone" class="current">Windows Phone</a>
</div>	

Este tópico mostra como usar o logon único do Live Connect para autenticar usuários nos Serviços Móveis do Azure de um aplicativo do Windows Phone 8.  Neste tutorial, você adiciona autenticação ao projeto quickstart usando o Live Connect. Quando autenticado com êxito pelo Live Connect, um usuário conectado é recebido pelo nome e o valor da ID do usuário é exibido.  

<div class="dev-callout"><b>Observação</b>
	<p>Este tutorial demonstra os benefícios do uso da experiência de logon único fornecida pelo Live Connect para aplicativos do Windows Phone. Isso permite que você autentique mais facilmente um usuário já conectado com seu serviço móvel. Para obter uma experiência de autenticação mais generalizada que ofereça suporte a vários provedores de autenticação, consulte o tópico <a href="/pt-br/develop/mobile/tutorials/get-started-with-users-wp8/">Introdução à autenticação</a>. </p>
</div>

Este tutorial o orientará pelas etapas básicas para habilitar a autenticação do Live Connect:

1. [Registrar seu aplicativo para autenticação e configurar os Serviços Móveis]
2. [Restringir as permissões de tabela aos usuários autenticados]
3. [Adicionar autenticação ao aplicativo]

Este tutorial exige o seguinte:

+ [Live SDK para Windows e Windows Phone]
+ Microsoft Visual Studio 2012 Express para Windows Phone

Este tutorial baseia-se no início rápido dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis].

<h2><a name="register"></a><span class="short-header">Registrar seu aplicativo</span>Registrar seu aplicativo com o Live Connect</h2>

Para poder autenticar usuários, você deve registrar seu aplicativo no Centro de Desenvolvedores do Live Connect. Você deve registrar o segredo do cliente para integrar o Live Connect com os Serviços Móveis.

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e, em seguida, clique no seu serviço móvel.

   	![][4]

2. Clique na guia **Painel** e anote o valor da **URL do Site**.

   	![][5]

    Você usará esse valor para definir o domínio de redirecionamento.

3. Navegue até a página <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Meus Aplicativos</a> no Centro de Desenvolvedores do Live Connect e faça logon com a sua conta da Microsoft, se necessário. 

4. Clique em **Criar aplicativo**, digite um **Nome de aplicativo** e clique em **Aceito**.

   	![][1] 

   	Isso registra o aplicativo no Live Connect.

5. Clique na **página Configurações do aplicativo**, em seguida, em **Configurações da API** e anote os valores da **ID do Cliente** e o **Segredo do cliente**. 

   	![][2]

    <div class="dev-callout"><b>Observação de segurança</b>
	<p>O segredo do cliente é uma credencial de segurança importante. Não o compartilhe com ninguém nem o distribua com o seu aplicativo.</p>
    </div>

6. Em **Domínio de redirecionamento**, digite a URL do seu serviço móvel da Etapa 2, clique em **Sim** no **aplicativo cliente Mobile** e, em seguida, clique em **Salvar**.

7. De volta ao Portal de Gerenciamento, clique na guia **Identidade**, insira o **Segredo do cliente** obtido do Live Connect e clique em **Salvar**.

   	![][13]

O serviço móvel e seu aplicativo agora estão configurados para trabalhar com o Live Connect.

<h2><a name="permissions"></a><span class="short-header">Restringir permissões</span>Restringir permissões aos usuários autenticados</h2>

1. No Portal de Gerenciamento, clique na guia **Dados** e, em seguida, clique na tabela **TodoItem**. 

   	![][14]

2. Clique na guia **Permissões**, defina todas as permissões para **Somente usuários autenticados** e clique em **Salvar**. Isso garantirá que todas as operações em relação a tabela **TodoItem** exigem um usuário autenticado. Isso também simplificará os scripts no próximo tutorial, pois eles não precisarão permitir possíveis usuários anônimos.

   	![][15]

3. No Visual Studio 2012 Express para Windows Phone, abra o projeto criado quando você concluiu o tutorial [Introdução aos Serviços Móveis]. 

4. Pressione a tecla F5 para executar este aplicativo baseado em início rápido; verifique se uma exceção com código de status 401 (Não autorizado) é gerada. 
   
   	Isso acontece porque o aplicativo está acessando os Serviços Móveis como um usuário não autenticado, mas a tabela _TodoItem_ agora exige autenticação.

Em seguida, você irá atualizar o aplicativo para autenticar usuários com o Live Connect antes de solicitar recursos do serviço móvel.

<h2><a name="add-authentication"></a><span class="short-header">Adicionar autenticação</span>Adicionar autenticação ao aplicativo</h2>

1. Baixe e instale o [Live SDK para Windows e Windows Phone].

2. No menu **Projeto** do Visual Studio, clique em **Adicionar Referência**, em seguida, expanda **Assemblies**, clique em **Extensões**, marque **Microsoft.Live** e, em seguida, clique em **OK**. 

   	![][16]

  	Isso adicionará uma referência ao Live SDK ao projeto.

5. Abra o arquivo de projeto mainpage.xaml.cs e adicione o seguinte usando as instruções:

        using Microsoft.Live;      

6. Adicione o trecho de código a seguir à classe MainPage:
	
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task Authenticate()
        {
            LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT CLIENT ID HERE >>");

            while (session == null)
            {
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
                    MessageBox.Show(message, title, MessageBoxButton.OK);                    
                }
                else
                {
                    session = null;
                    MessageBox.Show("You must log in.", "Login Required", MessageBoxButton.OK);                    
                }
            }
         }

    Isso criará uma variável de membro para armazenar a sessão do Live Connect atual e um método para tratar o processo de autenticação.

7. Atualize a cadeia de caracteres _<< INSERT CLIENT ID HERE >>_ da etapa anterior com o valor de ID de cliente gerado quando você registrou seu aplicativo com o Live Connect.

    <div class="dev-callout"><b>Observação</b>
	<p>Em um aplicativo do Windows Phone 8, uma instância da classe <strong>LiveAuthClient</strong> é criada, passando o valor da ID de cliente para o construtor de classe. Em um <a href="/pt-br/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/">aplicativo da Windows Store</a>, a mesma classe é instanciada, passando o URI do domínio de redirecionamento.</p>
    </div>

8. Exclua o método **OnNavigatedTo** existente, ou remova seu comentário, e substitua-o pelo método a seguir que trata o evento **Loaded** para a página. 

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

   	Esse método chamará o novo método **Authenticate**. 

9. Substitua o construtor MainPage por este código:

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

   	Esse construtor também registrará o manipulador para o evento Loaded.
		
9. Pressione a tecla F5 para executar o aplicativo e entrar no Live Connect com sua Conta da Microsoft. 

   	Depois que o seu logon for bem-sucedido, o aplicativo será executado sem erros, e você será capaz de consultar os Serviços Móveis e fazer atualizações nos dados.

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorizar usuários com scripts], você irá obter o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usar para filtrar os dados retornados pelos Serviços Móveis. Para obter informações sobre como usar outros provedores de identidade para autenticação, consulte [Introdução à autenticação]. 

<!-- Anchors. -->
[Registrar seu aplicativo para autenticação e configurar os Serviços Móveis]: #register
[Restringir as permissões de tabela aos usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Próximas etapas]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-windows-phone-single-sign-on/mobile-live-connect-app-api-settings-mobile.png
[4]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-phone-single-sign-on/mobile-service-uri.png





[13]: ./media/mobile-services-windows-phone-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-phone-single-sign-on/mobile-add-reference-live-wp8.png

<!-- URLs. -->
[Meus aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows e Windows Phone]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Começar com os Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-wp8
[Começar com os dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-wp8
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-wp8
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-wp8
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

