<properties linkid="develop-mobile-tutorials-get-started-with-users-dotnet" urlDisplayName="Introdução aos usuários" pageTitle="Começar com a autenticação (Windows Store) | Centro de Desenvolvimento Móvel" metaKeywords="" description="Saiba como usar os Serviços Móveis para autenticar usuários de seu aplicativo da Windows Store por meio de uma variedade de provedores de identidade, como Google, Facebook, Twitter e Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Introdução à autenticação nos Serviços Móveis" authors=""  solutions="" writer="" manager="" editor=""  />




# Introdução à autenticação dos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-dotnet" title="Windows Store C#" class="current">Windows Store C#</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-android" title="Android">Android</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-html" title="HTML">HTML</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tópico mostra como autenticar usuários no Serviços Móveis do Azure do seu aplicativo. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.</p>
<p>Você pode assistir a uma versão do vídeo deste tutorial clicando no clipe à direita.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-users-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir Vídeo</span></a> <span class="time">10:04</span></div>
</div> 

Este tutorial orienta você pelas seguintes etapas básicas para autenticação no seu aplicativo:

1. [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]
2. [Restringir permissões de tabela para usuários autenticados]
3. [Adicionar autenticação ao aplicativo]

Este tutorial se baseia na inicialização rápida dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis]. 

<div class="dev-callout"><b>Observação</b>
	<p>Este tutorial demonstra o método básico fornecido pelos Serviços Móveis para autenticar os usuários utilizando uma variedade de provedores de identidade. Esse método é fácil de configurar e oferece suporte a vários provedores. No entanto, esse método também exige que os usuários efetuem o login sempre que seu aplicativo for iniciado. Em vez de usar Live Connect para fornecer uma experiência de logon único em seu aplicativo da Windows Store, consulte o tópico <a href="/pt-br/develop/mobile/tutorials/single-sign-on-windows-8-dotnet">Logon único para aplicativos da Windows Store usando o Live Connect</a>.</p>
</div>

<h2><a name="register"></a><span class="short-header">Registrar seu aplicativo</span>Registrar seu aplicativo para autenticação e configurar os Serviços Móveis</h2>

Para ser capaz de autenticar usuários, você deve registrar seu aplicativo com um provedor de identidade. Você deve registrar o segredo do cliente gerado pelo provedor com os Serviços Móveis.

1. Faça o logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu serviço móvel.

   	![][4]

2. Clique na guia **Painel** e anote o valor da **URL do Serviço Móvel**.

   	![][5]

    Talvez seja necessário fornecer este valor para o provedor de identidade ao registrar seu aplicativo.

3. Escolha um provedor de identidade suportado na lista abaixo e siga as etapas para registrar seu aplicativo com o provedor:

 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-microsoft-authentication/" target="_blank">Conta da Microsoft</a>
 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-facebook-authentication/" target="_blank">Logon no Facebook</a>
 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-twitter-authentication/" target="_blank">Login no Twitter</a>
 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-google-authentication/" target="_blank">Login no Google</a>
 - <a href="/pt-br/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Active Directory do Azure</a>


    Lembre-se de anotar os valores da identidade do cliente e segredo gerados pelo provedor.

    <div class="dev-callout"><b>Observação de Segurança</b>
	<p>O segredo gerado pelo provedor é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.</p>
    </div>

4. No Portal de Gerenciamento, clique na guia **Identidade**, digite os valores do identificador do aplicativo e do segredo compartilhados obtidos do provedor de identidade e clique em **Salvar**.

   	![][13]

5. (Opcional) Conclua as etapas em [Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft].

    <div class="dev-callout"><b>Observação</b>
	<p>Esta etapa é opcional, pois só é aplicada ao provedor de login da conta da Microsoft. Ao registrar as informações do pacote de aplicativos do Windows Store com Serviços Móveis, o cliente é capaz de reutilizar as credenciais de login da conta da Microsoft para obter uma experiência de logon única. Se você não fizer isso, os usuários de login da conta da Microsoft serão apresentados com uma solicitação de logon toda vez que o método de logon for chamado. Conclua esta etapa ao planejar usar o provedor de identidade da conta da Microsoft.</p>
    </div>

O serviço móvel e seu aplicativo agora estão configurados para trabalhar com o provedor de autenticação escolhido.

<h2><a name="permissions"></a><span class="short-header">Restringir permissões</span>Restringir permissões para usuários autenticados</h2>

1. No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**. 

   	![][14]

2. Clique na guia **Permissões**, defina todas as permissões para **Somente usuários autenticados** e clique em **Salvar**. Isso garantirá que todas as operações em relação a tabela **TodoItem** exigem um usuário autenticado. Isso também simplifica os scripts no próximo tutorial porque eles não terão que permitir a possibilidade de usuários anônimos.

   	![][15]

3. No Visual Studio 2012 Express para Windows 8, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis]. 

4. Pressione a tecla F5 para executar este aplicativo baseado em inicialização rápida; verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado. 
   
   	Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela _TodoItem_ agora exige autenticação.

Em seguida, você irá atualizar o aplicativo para autenticar usuários antes de solicitar recursos do serviço móvel.

<h2><a name="add-authentication"></a><span class="short-header">Adicionar autenticação</span>Adicionar autenticação ao aplicativo</h2>

5. Abra o arquivo de projeto mainpage.xaml.cs e adicione o seguinte usando a declaração:

        using Windows.UI.Popups;

6. Adicione o seguinte snippet de código para a classe MainPage:
	
        private MobileServiceUser user;
        private async System.Threading.Tasks.Task Authenticate()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                        
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    Isso cria uma variável de membro para armazenar o usuário atual e um método para manipular o processo de autenticação. O usuário é autenticado usando um logon do Facebook. Se você estiver usando um provedor de identidade que não seja o Facebook, altere o valor de **MobileServiceAuthenticationProvider** acima para o valor do seu provedor.

    <div class="dev-callout"><b>Observação</b>
	<p>Se você registrou as informações do pacote de aplicativo Windows Store com Serviços Móveis, você deve chamar o método <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> fornecendo um valor de <strong>true</strong> para o parâmetro <em>useSingleSignOn</em>. Se você não fizer isso, os usuários ainda serão apresentados com uma solicitação de logon toda vez que o método de logon for chamado.</p>
    </div>

8. Substitua o método **OnNavigatedTo** existente irá substituir com o seguinte método que chama o novo método **Authenticate**:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }
		
9. Pressione a tecla F5 para executar o aplicativo e entrar no aplicativo com seu provedor de identidade escolhido. 

   	Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorizar usuários com scripts], você irá obter o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usar para filtrar os dados retornados pelos Serviços Móveis. Saiba mais sobre como usar os Serviços Móveis com .NET no [Referência conceitual do tutorial dos Serviços Móveis .NET]

<!-- Anchors. -->
[Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Próximas etapas]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-dotnet-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-dotnet-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-dotnet-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-dotnet-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-dotnet-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Enviar uma página de aplicativo:]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Logon único para aplicativos da Windows Store usando o Live Connect]: ./mobile-services-single-sign-on-win8-dotnet.md
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started
[Começar com os dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-dotnet
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet
[Introdução às notificações de push]: /pt-br/develop/mobile/tutorials/get-started-with-push-dotnet
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-users-js

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência conceitual do tutorial dos Serviços Móveis .NET]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library
[Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft]: /pt-br/develop/mobile/how-to-guides/register-windows-store-app-package

