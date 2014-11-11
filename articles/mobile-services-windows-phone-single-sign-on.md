<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-wp8" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Phone application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Authenticate your Windows Phone 8 app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Autenticar seu aplicativo do Windows Phone 8 com o logon único do Live Connect

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/pt-br/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="C# da Windows Store#">C# da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/single-sign-on-windows-8-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/single-sign-on-wp8/" title="Windows Phone" class="current">Windows Phone</a>
</div>

Este tópico mostra como usar o logon único do Live Connect para autenticar usuários nos Serviços Móveis do Windows Azure de um aplicativo do Windows Phone 8. Neste tutorial, você adiciona autenticação ao projeto quickstart usando o Live Connect. Quando autenticado com êxito pelo Live Connect, um usuário conectado é recebido pelo nome e o valor da ID do usuário é exibido.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
    <p>Este tutorial demonstra os benef&iacute;cios do uso da experi&ecirc;ncia de logon &uacute;nico fornecida pelo Live Connect para aplicativos do Windows Phone. Isso permite que voc&ecirc; autentique mais facilmente um usu&aacute;rio j&aacute; conectado com seu servi&ccedil;o m&oacute;vel. Para obter uma experi&ecirc;ncia de autentica&ccedil;&atilde;o mais generalizada que ofere&ccedil;a suporte a v&aacute;rios provedores de autentica&ccedil;&atilde;o, consulte o t&oacute;pico <a href="/pt-br/develop/mobile/tutorials/get-started-with-users-wp8/">Introdu&ccedil;&atilde;o &agrave; autentica&ccedil;&atilde;o</a>. </p>
</div>

Este tutorial explica estas etapas básicas para autenticação do Live Connect:

1.  [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis][Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]
2.  [Restringir permissões de tabela para usuários autenticados][Restringir permissões de tabela para usuários autenticados]
3.  [Adicionar autenticação ao aplicativo][Adicionar autenticação ao aplicativo]

Este tutorial exige o seguinte:

-   [Live SDK para Windows e Windows Phone][Live SDK para Windows e Windows Phone]
-   Microsoft Visual Studio 2012 Express para Windows Phone

Este tutorial baseia-se no quickstart dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

## <a name="register"></a><span class="short-header">Registrar seu aplicativo</span>Registrar seu aplicativo com o Live Connect

Para poder autenticar usuários, você deve registrar seu aplicativo no Centro de Desenvolvedores do Live Connect. Você deve registrar o segredo do cliente para integrar o Live Connect com os Serviços Móveis.

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

    ![][0]

2.  Clique na guia **Painel** e anote o valor da **URL do Site**.

    ![][1]

    Você usará esse valor para definir o domínio de redirecionamento.

3.  Navegue até a página [Meus Aplicativos][Meus Aplicativos] na Central de Desenvolvedores do Live Connect e faça logon com sua conta da Microsoft, se necessário.

4.  Clique em **Criar aplicativo**, digite um **Nome de aplicativo** e clique em **Aceito**.

    ![][2]

    Isso registra o aplicativo no Live Connect.

5.  Clique na **página Configurações do aplicativo**, em seguida, em **Configurações da API** e anote os valores da **ID do cliente** e **Segredo do cliente**.

    ![][3]

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o de Seguran&ccedil;a</b>
<p>O segredo do cliente &eacute; uma credencial de seguran&ccedil;a importante. N&atilde;o compartilhe o segredo do cliente com ningu&eacute;m nem o distribua com seu aplicativo.</p>
</div>

6.  Em **Domínio de redirecionamento**, digite a URL do seu serviço móvel da Etapa 2, clique em **Sim** no **aplicativo cliente Mobile** e, em seguida, clique em **Salvar**.

7.  De volta ao Portal de Gerenciamento, clique na guia **Identidade**, insira o **Segredo do cliente** obtido do Live Connect e clique em **Salvar**.

    ![][4]

O serviço móvel e seu aplicativo agora estão configurados para trabalhar com o Live Connect.

## <a name="permissions"></a><span class="short-header">Restringir permissões</span>Restringir permissões para usuários autenticados

1.  No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**.

    ![][5]

2.  Clique na guia **Permissões**, defina todas as permissões para **Somente usuários autenticados** e clique em **Salvar**. Isso garantirá que todas as operações em relação a tabela **TodoItem** exigem um usuário autenticado. Isso também simplifica os scripts do próximo tutorial porque eles não precisarão permitir a possibilidade de usuários anônimos.

    ![][6]

3.  No Visual Studio 2012 Express para Windows Phone, abra o projeto criado quando você concluiu o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

4.  Pressione a tecla F5 para executar este aplicativo baseado em início rápido; verifique se uma exceção com um código de status de 401 (não autorizado) é gerada.

    Isso acontece porque o aplicativo está acessando os Serviços Móveis como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

Em seguida, você irá atualizar o aplicativo para autenticar usuários com o Live Connect antes de solicitar recursos do serviço móvel.

## <a name="add-authentication"></a><span class="short-header">Adicionar autenticação</span>Adicionar autenticação ao aplicativo

1.  Baixe e instale o [Live SDK para Windows e Windows Phone][Live SDK para Windows e Windows Phone].

2.  No menu **Projeto** do Visual Studio, clique em **Adicionar Referência**, em seguida, expanda **Assemblies**, clique em **Extensões**, marque **Microsoft.Live** e, em seguida, clique em **OK**.

    ![][7]

    Isso adiciona uma referência ao Live SDK ao projeto.

3.  Abra o arquivo de projeto mainpage.xaml.cs e adicione o seguinte usando as instruções:

        using Microsoft.Live;      

4.  Adicione o seguinte snippet de código para a classe MainPage:

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

    Isso cria uma variável de membro para armazenar a sessão do Live Connect atual e um método para manipular o processo de autenticação.

5.  Atualize a cadeia de caracteres *\<\< INSERT CLIENT ID HERE \>\>* da etapa anterior com o valor de ID de cliente gerado quando você registrou seu aplicativo com o Live Connect.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Em um aplicativo do Windows Phone 8, uma inst&acirc;ncia da classe <strong>LiveAuthClient</strong> &eacute; criada, passando o valor da ID de cliente para o construtor de classe. Em um <a href="/pt-br/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/">aplicativo da Windows Store</a>, a mesma classe &eacute; instanciada, passando o URI do dom&iacute;nio de redirecionamento.</p>
</div>

6.  Exclua o método **OnNavigatedTo** existente, ou remova seu comentário, e substitua-o pelo método a seguir que trata o evento **Loaded** para a página.

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

    Esse método chamará o novo método **Authenticate**.

7.  Substitua o construtor MainPage por este código:

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

    Esse construtor também registrará o manipulador para o evento Loaded.

8.  Pressione a tecla F5 para executar o aplicativo e entrar no Live Connect com sua Conta da Microsoft.

    Depois que o seu logon for bem-sucedido, o aplicativo será executado sem erros, e você será capaz de consultar os Serviços Móveis e fazer atualizações nos dados.

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorizar usuários com scripts][Autorizar usuários com scripts], você irá obter o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usar para filtrar os dados retornados pelos Serviços Móveis. Para obter informações sobre como usar outros provedores de identidade para autenticação, consulte [Introdução à autenticação][8].

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-wp8/
  [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]: #register
  [Restringir permissões de tabela para usuários autenticados]: #permissions
  [Adicionar autenticação ao aplicativo]: #add-authentication
  [Live SDK para Windows e Windows Phone]: http://go.microsoft.com/fwlink/p/?LinkId=262253
  [Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-wp8
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-phone-single-sign-on/mobile-service-uri.png
  [Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  [2]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-live-connect-add-app.png
  [3]: ./media/mobile-services-windows-phone-single-sign-on/mobile-live-connect-app-api-settings-mobile.png
  [4]: ./media/mobile-services-windows-phone-single-sign-on/mobile-identity-tab-ma-only.png
  [5]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-data-tables.png
  [6]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-change-table-perms.png
  [7]: ./media/mobile-services-windows-phone-single-sign-on/mobile-add-reference-live-wp8.png
  [Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-wp8
  [8]: /pt-br/develop/mobile/tutorials/get-started-with-users-wp8
