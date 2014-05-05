<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-ios" urlDisplayName="Introdução à autenticação (Xamarin.iOS)" pageTitle="Introdução à autenticação (Xamarin.iOS) - Serviços Móveis" metaKeywords="Aplicativo de registro do Azure, autenticação do Azure, autenticar aplicativo, autenticar serviços móveis, Serviços Móveis do Xamarin.iOS" description="Saiba como usar autenticação em seu aplicativo dos Serviços Móveis do Azure para Xamarin.iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" 
title="Introdução à autenticação dos Serviços Móveis" />

# Introdução à autenticação dos Serviços Móveis
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/pt-br/develop/mobile/tutorials/get-started-with-users-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-html" title="HTML">HTML</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure do seu aplicativo.  Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.  

Este tutorial orienta você pelas seguintes etapas básicas para autenticação no seu aplicativo:

1. [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]
2. [Restringir permissões de tabela para usuários autenticados]
3. [Adicionar autenticação ao aplicativo]

Este tutorial baseia-se no Guia de início rápido dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis]. 

Para concluir este tutorial, é necessário ter [Xamarin.iOS], XCode 5.0 e iOS 5.0 ou versões posteriores.

<h2><a name="register"></a><span class="short-header">Registrar seu aplicativo</span>Registrar seu aplicativo para autenticação e configurar os Serviços Móveis</h2>

Para ser capaz de autenticar usuários, você deve registrar seu aplicativo com um provedor de identidade. Você deve registrar o segredo do cliente gerado pelo provedor com os Serviços Móveis.

1. Faça login no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique no seu serviço móvel.

   	![][4]

2. Clique na guia **Painel** e anote o valor da **URL do Site**.

   	![][5]

	Talvez seja necessário fornecer este valor para o provedor de identidade ao registrar seu aplicativo.

3. Na lista abaixo, escolha um provedor de identidade com suporte e siga as etapas para registrar seu aplicativo com esse provedor:

 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-microsoft-authentication/" target="_blank">Conta da Microsoft</a>
 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-facebook-authentication/" target="_blank">Logon no Facebook</a>
 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-twitter-authentication/" target="_blank">Logon no Twitter</a>
 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-google-authentication/" target="_blank">Logon no Google</a>
 - <a href="/pt-br/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>


    Lembre-se de anotar os valores da identidade do cliente e segredo gerados pelo provedor.

    <div class="dev-callout"><b>Observação de Segurança</b>
	<p>O segredo gerado pelo provedor é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.</p>
    </div>

4. De volta ao Portal de Gerenciamento, clique na guia **Identidade**, insira os valores do identificador do aplicativo e do segredo compartilhado obtidos do seu provedor de identidade e clique em **Salvar**.

   	![][13]

O serviço móvel e seu aplicativo agora estão configurados para trabalhar com o provedor de autenticação escolhido.

<h2><a name="permissions"></a><span class="short-header">Restringir permissões</span>Restringir permissões para usuários autenticados</h2>

1. No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**. 

   	![][14]

2. Clique na guia **Permissões**, defina todas as permissões para **Somente usuários autenticados** e clique em **Salvar**. Isso garantirá que todas as operações em relação a tabela **TodoItem** exigem um usuário autenticado. Isso também simplifica os scripts no próximo tutorial porque eles não terão que permitir a possibilidade de usuários anônimos.

   	![][15]

3. No Xcode, abra o projeto criado quando você concluiu o tutorial [Introdução aos Serviços Móveis]. 

4. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo no emulador do iPhone; verifique se uma exceção não tratada com código de status 401 (Não autorizado) é gerada depois que o aplicativo é iniciado. 
   
   	Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela _TodoItem_ agora exige autenticação.

Em seguida, você irá atualizar o aplicativo para autenticar usuários antes de solicitar recursos do serviço móvel.

<h2><a name="add-authentication"></a><span class="short-header">Adicionar autenticação</span>Adicionar autenticação ao aplicativo</h2>

1. Abra o arquivo do projeto **TodoService** e adicione as seguintes variáveis:

		// Mobile Service logged in user
		private MobileServiceUser user; 
		public MobileServiceUser User { get { return user; } }

2. Em seguida, adicione um novo método chamado **Authenticate** para **TodoService** definido como:

        private async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.MicrosoftAccount);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    <div class="dev-callout"><b>Observação</b>
	<p>Se você estiver usando um provedor de identidade diferente da Conta da Microsoft, altere o valor passado para <strong>LoginAsync</strong> acima para um dos seguintes: <i>Facebook</i>, <i>Twitter</i> ou <i>Google</i>.</p>
    </div>

3. Mova a solicitação da tabela **TodoItem** do construtor **TodoService** para um novo método chamado **CreateTable**:

        private async Task CreateTable()
        {
            // Create an MSTable instance to allow us to work with the TodoItem table
            todoTable = client.GetTable<TodoItem>();
        }
	
4. Criar um novo método público assíncrono chamado **LoginAndGetData** definido como:

        public async Task LoginAndGetData(UIViewController view)
        {
            await Authenticate(view);
            await CreateTable();
        }

5. Em **TodoListViewController** substituir o método **ViewDidAppear** e definir como encontrado abaixo. Isso registra no usuário se o **TodoService** ainda não tem um identificador de usuário:

        public override async void ViewDidAppear(bool animated)
        {
            base.ViewDidAppear(animated);

            if (TodoService.DefaultService.User == null)
            {
                await TodoService.DefaultService.LoginAndGetData(this);
            }

            if (TodoService.DefaultService.User == null)
            {
                // TODO:: show error
                return;
            } 
                
            RefreshAsync();
        }
6. Remover a chamada original para **RefreshAsync** de **TodoListViewController.ViewDidLoad**.
		
7. Pressionar o botão **Executar** para compilar o projeto, iniciar o aplicativo no emulador do iPhone e fazer logon com o provedor de identidade escolhido.

   	Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.

## Obtenha o exemplo concluído
Baixe o [o projeto de exemplo concluído]. Lembre-se de atualizar as variáveis **applicationURL** e **applicationKey** com suas próprias configurações do Azure. 

## <a name="next-steps"></a>Próximas etapas

No próximo tutorial, [Autorizar usuários com scripts], você irá obter o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usar para filtrar os dados retornados pelos Serviços Móveis. 

<!-- Anchors. -->
[Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Próximas etapas]:#next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-service-uri.png
[13]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. TODO:: update completed example project link with project download -->
[Enviar uma página de aplicativo:]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-xamarin-ios
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/-get-started-with-push-xamarin-ios
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[projeto de exemplo concluído]: http://go.microsoft.com/fwlink/p/?LinkId=331328

