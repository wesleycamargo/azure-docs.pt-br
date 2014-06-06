<properties linkid="develop-mobile-tutorials-get-started-with-users-android" urlDisplayName="Introdução à autenticação" pageTitle="Introdução à autenticação (Android) | Mobile Dev Center" metaKeywords="" description="Saiba como usar os Serviços Móveis para autenticar usuários de seu aplicativo Android por meio de vários provedores de identidade, como Google, Facebook, Twitter e Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Introdução à autenticação nos Serviços Móveis" authors=""  solutions="" writer="" manager="" editor=""  />





# Introdução à autenticação dos Serviços Móveis
<div class="dev-center-tutorial-selector sublanding">   
	<a href="/pt-br/develop/mobile/tutorials/get-started-with-users-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-android" title="Android" class="current">Android</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-html" title="HTML">HTML</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Este tópico mostra como autenticar usuários no Azure Mobile Services do seu aplicativo. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-authentication-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o vídeo</span></a><span class="time">10:42</span></div>
</div> 

Este tutorial orienta você pelas seguintes etapas básicas para autenticação no seu aplicativo:

1. [Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]
2. [Restringir permissões de tabela para usuários autenticados]
3. [Adicionar autenticação ao aplicativo]

Este tutorial baseia-se no início rápido dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis]. 

Para concluir este tutorial, é necessário ter o Eclipse e o Android 4.2 ou uma versão posterior. 

<h2><a name="register"></a><span class="short-header">Registrar seu aplicativo</span>Registrar seu aplicativo para autenticação e configurar os Serviços Móveis</h2>

Para ser capaz de autenticar usuários, você deve registrar seu aplicativo com um provedor de identidade. Você deve registrar o segredo do cliente gerado pelo provedor com os Serviços Móveis.

1. Faça o logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu serviço móvel.

   	![][4]

2. Clique na guia **Painel** e anote o valor da **URL do Site**.

   	![][5]

    Talvez seja necessário fornecer este valor para o provedor de identidade ao registrar seu aplicativo.

3. Escolha um provedor de identidade suportado na lista abaixo e siga as etapas para registrar seu aplicativo com o provedor:

 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-microsoft-authentication/" target="_blank">Conta da Microsoft</a>
 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-facebook-authentication/" target="_blank">Logon no Facebook</a>
 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-twitter-authentication/" target="_blank">Logon no Twitter</a>
 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-google-authentication/" target="_blank">Logon no Google</a>
 - <a href="/pt-br/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>


    Lembre-se de anotar os valores da identidade do cliente e segredo gerados pelo provedor.

    <div class="dev-callout"><b>Observação de Segurança</b>
	<p>O segredo gerado pelo provedor é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.</p>
    </div>

4. No Portal de Gerenciamento, clique na guia **Identidade**, digite os valores do identificador do aplicativo e do segredo compartilhados obtidos do provedor de identidade e clique em **Salvar**.

   	![][13]

O serviço móvel e seu aplicativo agora estão configurados para trabalhar com o provedor de autenticação escolhido.

<h2><a name="permissions"></a><span class="short-header">Restringir permissões</span>Restringir permissões para usuários autenticados</h2>

1. No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**. 

   	![][14]

2. Clique na guia **Permissões**, defina todas as permissões para **Somente usuários autenticados** e clique em **Salvar**. Isso garantirá que todas as operações em relação a tabela **TodoItem** exigem um usuário autenticado. Isso também simplifica os scripts no próximo tutorial porque eles não terão que permitir a possibilidade de usuários anônimos.

   	![][15]

3. No Eclipse, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis]. 

4. No menu **Executar**, clique em **Executar** para iniciar o aplicativo. Verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é acionada depois que o aplicativo é iniciado. 

	 Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela _TodoItem_ agora exige autenticação.

Em seguida, você irá atualizar o aplicativo para autenticar usuários antes de solicitar recursos do serviço móvel.

<h2><a name="add-authentication"></a><span class="short-header">Adicionar autenticação</span>Adicionar autenticação ao aplicativo</h2>

1. No Gerenciador de Pacotes no Eclipse, abra o arquivo ToDoActivity.java e adicione as seguintes instruções de importação.

		import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
		import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2. Adicione o seguinte método à classe **ToDoActivity**: 
	
		private void authenticate() {
		
			// Login using the Google provider.
			mClient.login(MobileServiceAuthenticationProvider.Google,
					new UserAuthenticationCallback() {
	
						@Override
						public void onCompleted(MobileServiceUser user,
								Exception exception, ServiceFilterResponse response) {
	
							if (exception == null) {
								createAndShowDialog(String.format(
												"You are now logged in - %1$2s",
												user.getUserId()), "Success");
								createTable();
							} else {
								createAndShowDialog("You must log in. Login Required", "Error");
							}
						}
					});
		}

    Isso cria um novo método para manipular o processo de autenticação. O usuário é autenticado usando um logon do Google. Será exibida uma caixa de diálogo que exibe a ID do usuário autenticado. Você não pode continuar sem uma autenticação positiva.

    <div class="dev-callout"><b>Observação</b>
	<p>Se você estiver usando um provedor de identidade além do Google, altere o valor passado para o método <strong>login</strong> acima para um dos seguintes: <em>MicrosoftAccount</em>, <em>Facebook</em> ou <em>Twitter</em>.</p>
    </div>

3. No método **onCreate**, adicione a seguinte linha de código após o código que instancia o objeto `MobileServiceClient`.

		authenticate();

	Essa chamada inicia o processo de autenticação.

4. Mova o código restante após `authenticate();` no método **onCreate** para um novo método **createTable**, parecido com o seguinte:

		private void createTable() {
	
			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getTable(ToDoItem.class);
	
			mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
	
			// Create an adapter to bind the items with the view
			mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
			ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
			listViewToDo.setAdapter(mAdapter);
	
			// Load the items from the Mobile Service
			refreshItemsFromTable();
		}

9. No menu **Executar**, clique em **Executar** para iniciar o aplicativo e entrar com seu provedor de identidade escolhido. 

   	Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.

## <a name="next-steps"></a>Próximas etapas

No próximo tutorial, [Autorizar usuários com scripts], você irá obter o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usar para filtrar os dados retornados pelos Serviços Móveis. 

<!-- Anchors. -->
[Registrar seu aplicativo para a autenticação e configure os Serviços Móveis]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Próximas etapas]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-android-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->

[Enviar uma página de aplicativo:]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Logon único para aplicativos da Windows Store usando o Live Connect]: /pt-br/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Começar com os Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-android
[Começar com os dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-android
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-android
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-android
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-android

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

