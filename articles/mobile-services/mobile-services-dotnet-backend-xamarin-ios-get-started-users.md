<properties 
	pageTitle="Introdução à autenticação em Serviços Móveis para aplicativos Xamarin iOS - Serviços Móveis do Azure" 
	description="Aprenda a usar os serviços móveis para autenticar usuários de seu aplicativo iOS Xamarin por meio de uma variedade de provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft." 
	services="mobile-services" 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="donnam"/>

# Adicionar autenticação ao aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1. [Registrar seu aplicativo para autenticação e configurar os Serviços Móveis]
2. [Restringir permissões de tabela para usuários autenticados]
3. [Adicionar autenticação ao aplicativo]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis].

##<a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

<ol start="6">
<li><p>No Visual Studio ou Xamarin Studio, execute o projeto cliente em um dispositivo ou simulador. Verifique se uma exceção não tratada com um código de status 401 (Não autorizado) é gerada após o aplicativo ser iniciado.</p>
   
   	<p>Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela <em>TodoItem</em> agora exige autenticação.</p></li>
</ol>

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

Nesta seção, você modificará o aplicativo para exibir uma tela de logon antes de exibir os dados. Quando o aplicativo iniciar, ele não se conectará a seu serviço móvel e não exibirá dados. Depois que o usuário executar pela primeira vez um gesto de atualização, a tela de logon aparecerá e, após o êxito no logon, a lista de itens de tarefas pendentes será exibida.

1. No projeto cliente, abra o arquivo **QSTodoService.cs** e adicione as seguintes declarações ao QSTodoService:

		// Mobile Service logged in user
		private MobileServiceUser user; 
		public MobileServiceUser User { get { return user; } }

2. Adicione um novo método **Authenticate** ao **QSTodoService** com a seguinte definição:

        private async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

> [AZURE.NOTE]Se você estiver usando um provedor de identidade que não seja o Facebook, altere o valor passado para **LoginAsync** acima para um dos seguintes: _MicrosoftAccount_, _Twitter_, _Google_ ou _WindowsAzureActiveDirectory_.

3. Abra o **QSTodoListViewController.cs**. Modifique a definição do método de **ViewDidLoad** para remover a chamada para **RefreshAsync()** perto do final:

		public override async void ViewDidLoad ()
		{
			base.ViewDidLoad ();

			todoService = QSTodoService.DefaultService;

			todoService.BusyUpdate += (bool busy) => {
				if (busy)
					activityIndicator.StartAnimating ();
				else 
					activityIndicator.StopAnimating ();
			};

			// Comment out the call to RefreshAsync
			// await RefreshAsync ();

			AddRefreshControl ();
		}


4. Modifique o método **RefreshAsync** para autenticar e exibir uma tela de logon se a propriedade **User** for null. No código a seguir na parte superior da definição do método:

		// start of RefreshAsync method
		if (todoService.User == null) {
			await QSTodoService.DefaultService.Authenticate (this);
			if (todoService.User == null) {
				Console.WriteLine ("couldn't login!!");
				return;
			}
		}
		// rest of RefreshAsync method
	
5. Pressione o botão **Executar** para criar o projeto e iniciar o aplicativo no simulador do iPhone. Verifique se o aplicativo não exibe dados.

	Faça um gesto de atualização pressionando a lista de itens, o que fará com que a tela de logon apareça. Depois de inserir credenciais válidas com êxito, o aplicativo exibirá a lista de itens de tarefas pendentes e você poderá fazer a atualização dos dados.

<!-- ## <a name="next-steps"> </a>Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. 
 -->
<!-- Anchors. -->
[Registrar seu aplicativo para autenticação e configurar os Serviços Móveis]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: mobile-services-dotnet-backend-xamarin-ios-get-started.md
[Get started with authentication]: mobile-services-dotnet-backend-xamarin-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-xamarin-ios-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=August15_HO6-->