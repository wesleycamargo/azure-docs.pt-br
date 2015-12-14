<properties 
	pageTitle="Introdução à autenticação para os Aplicativos Móveis no aplicativo do Xamarin.Forms" 
	description="Saiba como usar os Aplicativos Móveis para autenticar usuários de seu aplicativo Xamarin Forms por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft." 
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
	ms.date="11/30/2015" 
	ms.author="wesmc"/>

# Adicionar autenticação ao aplicativo do Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Visão geral

Este tópico mostra como autenticar usuários de um aplicativo móvel do Serviço de Aplicativo em seu aplicativo cliente. Neste tutorial, você adiciona a autenticação ao projeto de início rápido do Xamarin.Forms usando um provedor de identidade com suporte do Serviço de Aplicativo. Depois de ser autenticado e autorizado com êxito pelo Aplicativo Móvel, o valor da ID de usuário é exibido e você poderá acessar dados da tabela restrita.

Primeiro, é necessário concluir o [tutorial de início rápido do Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Se você não usar o projeto baixado de início rápido do servidor, deve adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).


##Registrar seu aplicativo para a autenticação e configurar os Serviços de Aplicativos

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##Restringir permissões a usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##Adicionar autenticação à biblioteca de classes portátil 

Os Aplicativos Móveis usam um método `MobileServiceClient.LoginAsync` específico da plataforma para exibir a interface de logon e os dados de cache. Para autenticar com um projeto do Xamarin Forms, você definirá uma interface `IAuthenticate` na biblioteca de classes portátil. Cada plataforma para a qual você deseja dar suporte pode implementar essa interface no projeto específico da plataforma. Você adicionará um código para autenticar antes de fazer chamadas em relação à tabela restrita da biblioteca de classes portátil.

1. No Visual Studio ou Xamarin Studio, abra App.cs no projeto **portátil**. Adicione a seguinte instrução `using` ao arquivo.

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

4. Abra TodoList.xaml.cs no projeto **portátil** e atualize o método `OnAppearing` para autenticar antes de tentar atualizar os itens da tabela.


        protected override async void OnAppearing()
        {
            base.OnAppearing();

            if (App.Authenticator != null)
                await App.Authenticator.Authenticate();

            // Set syncItems to true in order to synchronize the data on startup when running in offline mode
            await RefreshItems(true, syncItems: false);
        }


5. Salve as alterações e compile o projeto do portal, verificando se não há erros.


##Adicionar autenticação ao aplicativo do Android

Nesta seção, você adicionará a autenticação para o projeto do droid. Se não estiver trabalhando com dispositivos com Android, você poderá ignorar esta seção.

1. No Visual Studio ou Xamarin Studio, clique com botão direito do mouse no projeto **droid** e clique em **Configurar como Projeto de Inicialização**.

2. Vá em frente e execute o projeto no depurador para verificar se uma exceção sem tratamento com um código de status de 401 (Não autorizado) é gerada depois que o aplicativo for iniciado. Isso ocorrerá porque você restringiu o acesso no back-end apenas a usuários autorizados.

3. Em seguida, abra MainActivity.cs no projeto do droid e adicione a instrução `using` a seguir.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Atualize a classe `MainActivity` para implementar a interface `IAuthenticate`.

		public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. Atualize a classe `MainActivity` adicionando um campo `MobileServiceUser` e o método `Authenticate` mostrado abaixo para dar suporte à interface `IAuthenticate`.
 
	Se você deseja usar um `MobileServiceAuthenticationProvider` diferente em vez de o Facebook, faça essa alteração também.

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

6. Atualize o método `OnCreate` da classe `MainActivity` para inicializar o autenticador antes de carregar o aplicativo.

        App.Init((IAuthenticate)this);

        LoadApplication(new App());



7. Recompile o aplicativo e execute-o. Faça logon com o provedor de autenticação escolhido e verifique se você consegue acessar a tabela como um usuário autenticado.



##Adicionar autenticação ao aplicativo do iOS

Nesta seção, você adicionará a autenticação para o projeto do iOS. Se não estiver trabalhando com dispositivos com iOS, você poderá ignorar esta seção.

1. No Visual Studio ou Xamarin Studio, clique com botão direito do mouse no projeto **iOS** e clique em **Configurar como Projeto de Inicialização**.

2. Vá em frente e execute o projeto no depurador para verificar se uma exceção sem tratamento com um código de status de 401 (Não autorizado) é gerada depois que o aplicativo for iniciado. Isso ocorrerá porque você restringiu o acesso no back-end apenas a usuários autorizados.

3. Em seguida, abra AppDelegate.cs no projeto do iOS e adicione a instrução `using` a seguir.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Atualize a classe `AppDelegate` para implementar a interface `IAuthenticate`.

		public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate


5. Atualize a classe `AppDelegate` adicionando um campo `MobileServiceUser` e o método `Authenticate` mostrado abaixo para dar suporte à interface `IAuthenticate`.
 
	Se você deseja usar um `MobileServiceAuthenticationProvider` diferente em vez de o Facebook, faça essa alteração também.

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

        App.Init((IAuthenticate)this);

		LoadApplication (new App ());



7. Recompile o aplicativo e execute-o. Faça logon com o provedor de autenticação escolhido e verifique se você consegue acessar a tabela como um usuário autenticado.




##Adicionar autenticação ao aplicativo do Windows

Nesta seção, você adicionará a autenticação para o projeto do WinApp. Se não estiver trabalhando com dispositivos com Windows, você poderá ignorar esta seção.

1. No Visual Studio, clique com botão direito no projeto **WinApp** e clique em **Definir como Projeto de Inicialização**.

2. Vá em frente e execute o projeto no depurador para verificar se uma exceção sem tratamento com um código de status de 401 (Não autorizado) é gerada depois que o aplicativo for iniciado. Isso ocorrerá porque você restringiu o acesso no back-end apenas a usuários autorizados.

3. Em seguida, abra MainPage.xaml.cs no projeto do WinApp e adicione a instrução `using` a seguir. Substitua <*Seu namespace de biblioteca de classes portátil*> pelo namespace da biblioteca de classes portátil.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using <Your portable class library namespace>;

4. Atualize a classe `MainPage` para implementar a interface `IAuthenticate`.

	    public sealed partial class MainPage : IAuthenticate


5. Atualize a classe `MainPage` adicionando um campo `MobileServiceUser` e o método `Authenticate` mostrado abaixo para dar suporte à interface `IAuthenticate`.
 
	Se você deseja usar um `MobileServiceAuthenticationProvider` diferente em vez de o Facebook, faça essa alteração também.

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
                    var messageDialog = new Windows.UI.Popups.MessageDialog(
							string.Format("you are now logged in - {0}", user.UserId), "Authentication");
                    messageDialog.ShowAsync();
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

            <Your portable class library namespace>.App.Init((IAuthenticate)this);
            
            LoadApplication(new WesmcMobileAppGaTest.App());
        }



7. Recompile o aplicativo e execute-o. Faça logon com o provedor de autenticação escolhido e verifique se você consegue acessar a tabela como um usuário autenticado.




<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333


 

<!---HONumber=AcomDC_1203_2015--->