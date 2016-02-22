<properties
	pageTitle="Introdução à autenticação para os Aplicativos Móveis no aplicativo do Xamarin.Forms"
	description="Saiba como usar os Aplicativos Móveis para autenticar usuários de seu aplicativo Xamarin Forms por meio de uma variedade de provedores de identidade, incluindo o AAD, o Google, o Facebook, o Twitter e o Microsoft."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="wesmc"/>

# Adicionar autenticação ao aplicativo Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

##Visão geral

Este tópico mostra como autenticar usuários de um aplicativo móvel do Serviço de Aplicativo em seu aplicativo cliente. Neste tutorial, você adiciona a autenticação ao projeto de início rápido do Xamarin.Forms usando um provedor de identidade com suporte do Serviço de Aplicativo. Depois de ser autenticado e autorizado com êxito pelo Aplicativo Móvel, o valor da ID de usuário é exibido e você poderá acessar dados da tabela restrita.

Primeiro, é necessário concluir o [tutorial de início rápido do Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Se você não usar o projeto baixado de início rápido do servidor, deve adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).


##Registrar seu aplicativo para a autenticação e configurar os Serviços de Aplicativos

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##Restringir permissões a usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##Adicionar autenticação à biblioteca de classes portátil

Os Aplicativos Móveis usam um método `MobileServiceClient.LoginAsync` específico da plataforma para exibir a interface de logon e os dados de cache. Para autenticar um projeto do Xamarin Forms, você definirá uma interface `IAuthenticate` na biblioteca de classes portátil. Cada plataforma para a qual você deseja dar suporte implementará essa interface no projeto específico da plataforma.

Você também atualizará a interface do usuário definida na biblioteca de classes portátil ao adicionar um botão de logon. O usuário precisará clicar neste botão para se autenticar depois que o aplicativo for iniciado.

1. No Visual Studio ou no Xamarin Studio, abra App.cs no projeto **portátil**. Adicione a seguinte instrução `using` ao arquivo.

		using System.Threading.Tasks;

2. Em App.cs, adicione a definição de interface `IAuthenticate` a seguir imediatamente antes da definição de classe `App`.

	    public interface IAuthenticate
	    {
	        Task<bool> Authenticate();
	    };

3. Em App.cs, adicione os membros estáticos a seguir para inicializar a interface com uma implementação específica da plataforma.

		public class App : Application
		{

	        public static IAuthenticate Authenticator { get; private set; }

	        public static void Init(IAuthenticate authenticator)
	        {
	            Authenticator = authenticator;
	        }

			...


4. Abra TodoList.xaml.cs no projeto **portátil**. Adicione o seguinte sinalizador à classe `TodoList` para indicar se o usuário se autenticou ou não.

        bool authenticated = false;


5. Em TodoList.xaml.cs, atualize o método `OnAppearing` de forma que os itens só sejam atualizados se o usuário tiver se autenticado.


        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Set syncItems to true in order to synchronize the data on startup when running in offline mode
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }

6. Em TodoList.xaml.cs, na parte superior do construtor da classe `TodoList` defina o botão de logon a seguir e clique no manipulador...

        public TodoList()
        {
            InitializeComponent();

            manager = TodoItemManager.DefaultManager;

            var loginButton = new Button
            {
                Text = "Login",
                TextColor = Xamarin.Forms.Color.Black,
                BackgroundColor = Xamarin.Forms.Color.Lime,
            };
            loginButton.Clicked += loginButton_Clicked;

            Xamarin.Forms.StackLayout bp = buttonsPanel as StackLayout;
            Xamarin.Forms.StackLayout bpParentStack = bp.Parent.Parent as StackLayout;

            bpParentStack.Padding = new Xamarin.Forms.Thickness(10, 30, 10, 20);
            bp.Orientation = StackOrientation.Vertical;
            bp.Children.Add(loginButton);

			...

7. Em TodoList.xaml.cs, adicione o seguinte manipulador ao evento de clique no botão de logon

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true in order to synchronize the data on startup when running in offline mode
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }


8. Salve suas alterações e compile o projeto de biblioteca de classes portátil verificando se não há erros.


##Adicionar autenticação ao aplicativo Android

Nesta seção, você adicionará a autenticação ao projeto droid. Se você não estiver trabalhando com dispositivos Android, ignore esta seção.

1. No Visual Studio ou Xamarin Studio, clique com botão direito no projeto **droid** e clique em **Configurar como projeto de inicialização**.

2. Vá em frente e execute o projeto no depurador para verificar se uma exceção sem tratamento com um código de status de 401 (Não autorizado) for gerada depois que o aplicativo for iniciado. Isso ocorrerá porque você restringiu o acesso no back-end apenas a usuários autorizados.

3. Em seguida, abra MainActivity.cs no projeto droid e adicione a instrução `using` a seguir.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Atualize a classe `MainActivity` para implementar a interface `IAuthenticate`.

		public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. Atualize a classe `MainActivity` adicionando um campo `MobileServiceUser` e o método `Authenticate` mostrado abaixo para dar suporte à interface `IAuthenticate`.

	Se você quiser usar outro `MobileServiceAuthenticationProvider` em vez do Facebook, faça essa alteração também.

		// Define a authenticated user.
		private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                CreateAndShowDialog(string.Format("you are now logged in - {0}",
                    user.UserId), "Logged in!");

                success = true;
            }
            catch (Exception ex)
            {
                CreateAndShowDialog(ex.Message, "Authentication failed");
            }
            return success;
        }

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage(message);
            builder.SetTitle(title);
            builder.Create().Show();
        }


6. Atualize o método `OnCreate` da classe `MainActivity` para inicializar o autenticador antes de carregar o aplicativo.

        App.Init((IAuthenticate)this);

        LoadApplication(new App());



7. Recompile o aplicativo e o execute. Faça logon com o provedor de autenticação escolhido e verifique se você consegue acessar a tabela como um usuário autenticado.



##Adicionar autenticação ao aplicativo do iOS

Nesta seção, você adicionará a autenticação ao projeto do iOS. Se não estiver trabalhando com dispositivos iOS, você poderá ignorar esta seção.

1. No Visual Studio ou no Xamarin Studio, clique com botão direito do mouse no projeto **iOS** e clique em **Configurar como Projeto de Inicialização**.

2. Vá em frente e execute o projeto no depurador para verificar se uma exceção sem tratamento com um código de status de 401 (Não autorizado) for gerada depois que o aplicativo for iniciado. Isso ocorrerá porque você restringiu o acesso no back-end apenas a usuários autorizados.

3. Em seguida, abra AppDelegate.cs no projeto do iOS e adicione a instrução `using` a seguir.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Atualize a classe `AppDelegate` para implementar a interface `IAuthenticate`.

		public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate


5. Atualize a classe `AppDelegate` adicionando um campo `MobileServiceUser` e o método `Authenticate` mostrado abaixo para dar suporte à interface `IAuthenticate`.

	Se você quiser usar outro `MobileServiceAuthenticationProvider` em vez do Facebook, faça essa alteração também.

		// Define a authenticated user.
		private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        UIAlertView avAlert = new UIAlertView("Authentication", "You are now logged in " + user.UserId, null, "OK", null);
                        avAlert.Show();
                    }
                }

                success = true;
            }
            catch (Exception ex)
            {
                UIAlertView avAlert = new UIAlertView("Authentication failed", ex.Message, null, "OK", null);
                avAlert.Show();
            }
            return success;
        }

6. Atualize o método `FinishedLaunching` da classe `AppDelegate` para inicializar o autenticador antes de carregar o aplicativo.

        App.Init(this);

		LoadApplication (new App ());



7. Recompile o aplicativo e o execute. Faça logon com o provedor de autenticação escolhido e verifique se você consegue acessar a tabela como um usuário autenticado.




##Adicionar autenticação ao aplicativo do Windows

Nesta seção, você adicionará a autenticação para o projeto do WinApp. Se não estiver trabalhando com dispositivos Windows, você poderá ignorar esta seção.

1. No Visual Studio, clique com botão direito no projeto **WinApp** e clique em **Definir como projeto de inicialização**.

2. Vá em frente e execute o projeto no depurador para verificar se uma exceção sem tratamento com um código de status de 401 (Não autorizado) for gerada depois que o aplicativo for iniciado. Isso ocorrerá porque você restringiu o acesso no back-end apenas a usuários autorizados.

3. Em seguida, abra MainPage.xaml.cs no projeto do WinApp e adicione a instrução `using` a seguir. Substitua <*Seu namespace de biblioteca de classes portátil*> pelo namespace da biblioteca de classes portátil.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using <Your portable class library namespace>;

4. Atualize a classe `MainPage` para implementar a interface `IAuthenticate`.

	    public sealed partial class MainPage : IAuthenticate


5. Atualize a classe `MainPage` adicionando um campo `MobileServiceUser` e o método `Authenticate` mostrado abaixo para dar suporte à interface `IAuthenticate`.

	Se você quiser usar outro `MobileServiceAuthenticationProvider` em vez do Facebook, faça essa alteração também.

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
					if (user != null)
					{
	                    var messageDialog = new Windows.UI.Popups.MessageDialog(
								string.Format("you are now logged in - {0}", user.UserId), "Authentication");
	                    messageDialog.ShowAsync();
					}
                }

                success = true;
            }
            catch (Exception ex)
            {
                var messageDialog = new Windows.UI.Popups.MessageDialog(ex.Message, "Authentication Failed");
                messageDialog.ShowAsync();
            }
            return success;
        }

6. Atualize o construtor da classe `MainPage` para inicializar o autenticador antes de carregar o aplicativo. Substitua <*Seu namespace de biblioteca de classes portátil*> pelo namespace da biblioteca de classes portátil.

        public MainPage()
        {
            this.InitializeComponent();

            <Your portable class library namespace>.App.Init(this);

            LoadApplication(new <Your portable class library namespace>.App());
        }



7. Recompile o aplicativo e o execute. Faça logon com o provedor de autenticação escolhido e verifique se você consegue acessar a tabela como um usuário autenticado.


##Adicionar autenticação ao aplicativo do Windows Phone 8.1

Nesta seção, você adicionará a autenticação ao projeto WinPhone81. Se não estiver trabalhando com dispositivos com Windows Phone8.1, você poderá ignorar esta seção.

1. No Visual Studio, clique com botão direito no projeto **WinPhone81** e clique em **Definir como Projeto de Inicialização**.

2. Vá em frente e execute o projeto no depurador para verificar se uma exceção sem tratamento com um código de status de 401 (Não autorizado) for gerada depois que o aplicativo for iniciado. Isso ocorrerá porque você restringiu o acesso no back-end apenas a usuários autorizados.


3. Em seguida, abra MainPage.xaml.cs no projeto do WinPhone81 e adicione a instrução `using` a seguir. Substitua <*Seu namespace de biblioteca de classes portátil*> pelo namespace da biblioteca de classes portátil.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using <Your portable class library namespace>;

4. Atualize a classe `MainPage` para implementar a interface `IAuthenticate`.

	    public sealed partial class MainPage : IAuthenticate


5. Atualize a classe `MainPage` adicionando um campo `MobileServiceUser` e o método `Authenticate` mostrado abaixo para dar suporte à interface `IAuthenticate`.

	Se você quiser usar outro `MobileServiceAuthenticationProvider` em vez do Facebook, faça essa alteração também.

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
					if (user != null)
					{
	                    var messageDialog = new Windows.UI.Popups.MessageDialog(
								string.Format("you are now logged in - {0}", user.UserId), "Authentication");
	                    messageDialog.ShowAsync();
					}
                }

                success = true;
            }
            catch (Exception ex)
            {
                var messageDialog = new Windows.UI.Popups.MessageDialog(ex.Message, "Authentication Failed");
                messageDialog.ShowAsync();
            }
            return success;
        }

6. Atualize o construtor da classe `MainPage` para inicializar o autenticador antes de carregar o aplicativo. Substitua <*Seu namespace de biblioteca de classes portátil*> pelo namespace da biblioteca de classes portátil.

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;

            <Your portable class library namespace>.App.Init(this);

            LoadApplication(new <Your portable class library namespace>.App());
        }

7. Além disso, no Windows Phone, você precisa concluir o logon. Abra App.xaml.cs e adicione a seguinte instrução `using` e o código para o manipulador `OnActivated` na classe `App`.

	```
		using Microsoft.WindowsAzure.MobileServices;
	```

		protected override void OnActivated(IActivatedEventArgs args)
		{
		    base.OnActivated(args);

		    if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
		    {
		        var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
		        client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
		    }
		}



8. Recompile o aplicativo e o execute. Faça logon com o provedor de autenticação escolhido e verifique se você consegue acessar a tabela como um usuário autenticado.

<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!---HONumber=AcomDC_0211_2016-->