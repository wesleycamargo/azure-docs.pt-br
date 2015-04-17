<properties 
	pageTitle="Introdução à autenticação para aplicativos móveis no Xamarin iOS" 
	description="Aprenda a usar os aplicativos móveis para autenticar usuários de seu aplicativo Xamarin iOS por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="mahender"/>

# Adicionar autenticação ao seu aplicativo Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../includes/app-service-mobile-selector-get-started-users.md)]

Este tópico mostra como autenticar usuários de um aplicativo móvel do serviço de aplicativo usando o seu aplicativo cliente.  Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade ao qual o Serviço de Aplicativo dá suporte.  Após obter sucesso em ser autenticado e autorizado pelo seu aplicativo móvel, o valor de identificação de usuário é exibido.

Este tutorial se baseia no início rápido do aplicativo móvel.  Você também deve primeiro concluir o tutorial [Criar um aplicativo Xamarin.iOS]. 

##<a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços de Aplicativos

[AZURE.INCLUDE [app-service-mobile-register-authentication](../includes/app-service-mobile-register-authentication.md)] 

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

<ol start="7">
<li><p>No Visual Studio ou Xamarin Studio, execute o projeto cliente em um dispositivo ou simulador.  Verifique se uma exceção sem tratamento com um código de status 401 (Não autorizado) é gerada após o aplicativo ser iniciado.</p>
   
   	<p>Isso acontece porque o aplicativo tenta acessar o código do aplicativo móvel como um usuário não autenticado, mas a tabela <em>TodoItem</em> agora exige autenticação.</p></li>
</ol>

Em seguida, você irá atualizar o aplicativo para autenticar os usuários antes de solicitar recursos do seu Serviço de Aplicativo.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

Nesta seção, você modificará o aplicativo para exibir uma tela de logon antes de exibir os dados.  Quando o aplicativo iniciar, ele não se conectará a seu Serviço de Aplicativo e não exibirá dados.  Depois que o usuário executar pela primeira vez o gesto de atualização, a tela de logon aparecerá e, após o êxito no logon, a lista de itens de tarefas pendentes será exibida.

1. No projeto cliente, abra o arquivo **QSTodoService.cs** e adicione as seguintes declarações ao QSTodoService:

		// Logged in user
		private MobileServiceUser user; 
		public MobileServiceUser User { get { return user; } }

2. Adicione um novo método **Authenticate** ao **QSTodoService** com a seguinte definição:

        public async Task Authenticate(UIViewController view)
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

> [AZURE.NOTE] Se você estiver usando um provedor de identidade diferente do Facebook, altere o valor passado ao **LoginAsync** acima para um dos seguintes:  _MicrosoftAccount_, _Twitter__, Google_ ou _MicrosoftAzureActiveDirectory_.

3. Abra **QSTodoListViewController.cs**.  Modifique a definição de método de **ViewDidLoad** para remover a chamada para **RefreshAsync()** perto do final:

		public override async void ViewDidLoad ()
		{
			base.ViewDidLoad ();

			todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();

           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }

			// Comment out the call to RefreshAsync
			// await RefreshAsync ();
		}


4. Modifique o método **RefreshAsync** para autenticar e exibir uma tela de logon se a propriedade **User** for null.  No código a seguir na parte superior da definição do método:

		// start of RefreshAsync method
		if (todoService.User == null) {
			await QSTodoService.DefaultService.Authenticate (this);
			if (todoService.User == null) {
				Console.WriteLine ("couldn't login!!");
				return;
			}
		}
		// rest of RefreshAsync method
	
5. Pressione o botão **Run** para compilar o projeto e iniciar o aplicativo no simulador do iPhone.  Verifique se o aplicativo não exibe dados. 

	Faça um gesto de atualização pressionando a lista de itens, o que fará com que a tela de logon apareça.  Depois de inserir credenciais válidas com êxito, o aplicativo exibirá a lista de itens de tarefas pendentes e você poderá fazer a atualização dos dados.

 
<!-- URLs. -->
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Criar um aplicativo Xamarin.iOS]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

[Portal de Gerenciamento do Azure]: https://portal.azure.com

<!--HONumber=49-->