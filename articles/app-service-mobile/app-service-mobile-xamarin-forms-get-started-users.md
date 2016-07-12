<properties
	pageTitle="Introdução à autenticação para os Aplicativos Móveis no aplicativo do Xamarin.Forms | Microsoft Azure"
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
	ms.date="06/16/2016"
	ms.author="wesmc"/>

# Adicionar autenticação ao aplicativo Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

##Visão geral

Este tópico mostra como autenticar usuários de um aplicativo móvel do Serviço de Aplicativo em seu aplicativo cliente. Neste tutorial, você adiciona a autenticação ao projeto de início rápido do Xamarin.Forms usando um provedor de identidade com suporte do Serviço de Aplicativo. Depois de ser autenticado e autorizado com êxito pelo Aplicativo Móvel, o valor da ID de usuário é exibido e você poderá acessar dados da tabela restrita.

##Pré-requisitos

Para obter o melhor resultado com este tutorial, é recomendável que você conclua primeiro o tutorial [Criar um aplicativo Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Depois de concluir este tutorial, você terá um projeto Xamarin.Forms que é um aplicativo de lista de tarefas de várias plataformas.

Se você não usar o projeto baixado de início rápido do servidor, deve adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Registrar seu aplicativo para a autenticação e configurar os Serviços de Aplicativos

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##Restringir permissões a usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##Adicionar autenticação à biblioteca de classes portátil

Os Aplicativos Móveis usam o método de extensão [LoginAsync] no [MobileServiceClient] para fazer um usuário entrar com a autenticação do Serviço de Aplicativo. Este exemplo usa um fluxo de autenticação gerenciado por servidor que exibe a interface de entrada do provedor no aplicativo. Para saber mais, veja [Autenticação gerenciada por servidor](app-service-mobile-dotnet-how-to-use-client-library.md#serverflow). Para fornecer uma melhor experiência de usuário em seu aplicativo de produção, você pode considerar o uso da [Autenticação gerenciada pelo cliente](app-service-mobile-dotnet-how-to-use-client-library.md#clientflow).

Para autenticar-se em um projeto Xamarin.Forms, você definirá uma interface **IAuthenticate** na Biblioteca de Classes Portátil para o aplicativo. Você também atualizar a interface do usuário definida na Biblioteca de Classes Portátil para adicionar um botão **Entrar**, em que o usuário clicará para iniciar a autenticação. Após a autenticação bem-sucedida, os dados serão carregados do back-end do aplicativo móvel.

Você deve implementar a interface **IAuthenticate** para cada plataforma com suporte do seu aplicativo.


1. No Visual Studio ou no Xamarin Studio, abra App.cs do projeto com **Portable** no nome, que é o projeto da Biblioteca de Classes Portátil, e adicione a seguinte instrução `using`:

		using System.Threading.Tasks;

2. Em App.cs, adicione a definição de interface `IAuthenticate` a seguir imediatamente antes da definição de classe `App`.

	    public interface IAuthenticate
	    {
	        Task<bool> Authenticate();
	    }

3. Adicione os membros estáticos a seguir à classe **App** para inicializar a interface com uma implementação específica da plataforma.

	    public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }

4. Abra TodoList.xaml do projeto da Biblioteca de Classes Portátil, adicione o elemento **Button** a seguir ao elemento de layout *buttonsPanel* após o botão existente:

      	<Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30" 
			Clicked="loginButton_Clicked"/>

	Esse botão dispara a autenticação gerenciada por servidor com seu back-end de aplicativo móvel.

5. Abra TodoList.xaml.cs do projeto da Biblioteca de Classes Portátil e adicione o seguinte campo à classe `TodoList`:

		// Track whether the user has authenticated. 
        bool authenticated = false;


6. Substitua o método **OnAppearing** pelo seguinte código:

	    protected override async void OnAppearing()
	    {
	        base.OnAppearing();
	
	        // Refresh items only when authenticated.
	        if (authenticated == true)
	        {
	            // Set syncItems to true in order to synchronize the data 
	            // on startup when running in offline mode.
	            await RefreshItems(true, syncItems: false);

				// Hide the Sign-in button.
                this.loginButton.IsVisible = false;
	        }
	    }

	Isso garante que os dados só serão atualizados do serviço depois que o usuário tiver sido autenticado.

7. Adicione o seguinte manipulador para o evento **Clicked** à classe **TodoList**:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }

8. Salve suas alterações e compile o projeto de Biblioteca de Classes Portátil verificando se não há erros.


##Adicionar autenticação ao aplicativo Android

Esta seção mostra como implementar a interface **IAuthenticate** no projeto do aplicativo Android. Ignore esta seção se não estiver dando suporte a dispositivos Android.

1. No Visual Studio ou no Xamarin Studio, clique com botão direito do mouse no projeto **droid** e em **Definir como Projeto de Inicialização**.

2. Pressione F5 para iniciar o projeto no depurador e verifique se uma exceção sem tratamento com um código de status de 401 (Não autorizado) foi gerada depois que o aplicativo foi iniciado. Isso ocorrerá porque você restringiu o acesso no back-end aos usuários autorizados.

3. Abra MainActivity.cs no projeto Android e adicione estas instruções `using`:

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Atualize a classe **MainActivity** para implementar a interface **IAuthenticate** da seguinte maneira:

		public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. Atualize a classe **MainActivity** ao adicionar um campo **MobileServiceUser** e um método **Authenticate**, que é necessário para a interface **IAuthenticate**, da seguinte maneira:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }


	Se você estiver usando um provedor de identidade diferente do Facebook, escolha um valor diferente para [MobileServiceAuthenticationProvider].

6. Adicione o código a seguir ao final do método **onCreate** da classe **MainActivity** antes da chamada a `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

	Isso garante que o autenticador seja inicializado antes que o aplicativo seja carregado.

7. Recompile o aplicativo, execute-o e entre com o provedor de autenticação escolhido e verifique se você consegue acessar a tabela como um usuário autenticado.

##Adicionar autenticação ao aplicativo do iOS

Esta seção mostra como implementar a interface **IAuthenticate** no projeto do aplicativo iOS. Ignore esta seção se não estiver dando suporte a dispositivos iOS.

1. No Visual Studio ou no Xamarin Studio, clique com botão direito do mouse no projeto **iOS** e em **Definir como Projeto de Inicialização**.

2. Pressione F5 para iniciar o projeto no depurador e verifique se uma exceção sem tratamento com um código de status de 401 (Não autorizado) foi gerada depois que o aplicativo foi iniciado. Isso ocorrerá porque você restringiu o acesso no back-end aos usuários autorizados.

4. Abra AppDelegate.cs no projeto do iOS e adicione as seguintes instruções `using`:

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Atualize a classe **AppDelegate** para implementar a interface **IAuthenticate** da seguinte maneira:

		public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate

5. Atualize a classe **AppDelegate** ao adicionar um campo **MobileServiceUser** e um método **Authenticate**, que é necessário para a interface **IAuthenticate**, da seguinte maneira:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;                        
                    }
                }        
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();         

            return success;
        }

	Se você estiver usando um provedor de identidade diferente do Facebook, escolha um valor diferente para [MobileServiceAuthenticationProvider].

6. Adicione a seguinte linha de código ao método **FinishedLaunching** antes da chamada a `LoadApplication()`:

        App.Init(this);

	Isso garante que o autenticador seja inicializado antes que o aplicativo seja carregado.

7. Recompile o aplicativo, execute-o e entre com o provedor de autenticação escolhido e verifique se você consegue acessar a tabela como um usuário autenticado.


##Adicionar autenticação a projetos de aplicativo do Windows

Esta seção mostra como implementar a interface **IAuthenticate** nos projetos de aplicativo do Windows 8.1 e do Windows Phone 8.1. As mesmas etapas também se aplicam a projetos da Plataforma Universal do Windows (UWP). Ignore esta seção se não estiver dando suporte a dispositivos Windows.

1. No Visual Studio, clique com botão direito do mouse no projeto **WinApp** ou no projeto **WinPhone81** e clique em **Definir como Projeto de Inicialização**.

2. Pressione F5 para iniciar o projeto no depurador e verifique se uma exceção sem tratamento com um código de status de 401 (Não autorizado) foi gerada depois que o aplicativo foi iniciado. Isso ocorrerá porque você restringiu o acesso no back-end aos usuários autorizados.

3. Abra MainPage.xaml.cs para o projeto do aplicativo do Windows e adicione as seguintes instruções `using`:

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using Windows.UI.Popups;
		using <your_Portable_Class_Library_namespace>;

	Substitua `<your_Portable_Class_Library_namespace>` pelo namespace para sua biblioteca de classes portátil.

4. Atualize a classe **MainPage** para implementar a interface **IAuthenticate** da seguinte maneira:

	    public sealed partial class MainPage : IAuthenticate


5. Atualize a classe **MainPage** ao adicionar um campo **MobileServiceUser** e um método **Authenticate**, que é necessário para a interface **IAuthenticate**, da seguinte maneira:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
						success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }
                
            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

			// Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }


	Se você estiver usando um provedor de identidade diferente do Facebook, escolha um valor diferente para [MobileServiceAuthenticationProvider].

6. Adicione a seguinte linha de código no construtor à classe **MainPage** antes da chamada a `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);
 
    Substitua `<your_Portable_Class_Library_namespace>` pelo namespace para sua biblioteca de classes portátil. Se esse for o projeto WinApp, você poderá pular para a etapa 8. A próxima etapa se aplica apenas ao projeto WinPhone81, onde você precisa concluir o retorno de chamada de logon.

7. (Opcional) No projeto de aplicativo **WinPhone81**, abra App.xaml.c e adicione as seguintes instruções `using`:

		using Microsoft.WindowsAzure.MobileServices;
		using <your_Portable_Class_Library_namespace>;

	Substitua `<your_Portable_Class_Library_namespace>` pelo namespace para sua biblioteca de classes portátil.

8.  Adicione o método **OnActivated** a seguir à classe **App**:

		protected override void OnActivated(IActivatedEventArgs args)
		{
		    base.OnActivated(args);

			// We just need to handle activation that occurs after web authentication. 
		    if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
		    {
				// Get the client and call the LoginComplete method to complete authentication.
		        var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
		        client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
		    }
		}

	Quando a substituição do método já existir, bastará adicionar o código condicional do trecho acima.

7. Recompile o aplicativo, execute-o e entre com o provedor de autenticação escolhido e verifique se você consegue acessar a tabela como um usuário autenticado.

##Próximas etapas

Agora que você concluiu este tutorial de autenticação básica, considere continuar com um dos seguintes tutoriais:

+ [Adicionar notificações por push ao aplicativo](app-service-mobile-xamarin-forms-get-started-push.md) Saiba como adicionar suporte a notificações por push ao aplicativo e configurar o back-end do Aplicativo Móvel para usar os Hubs de Notificação do Azure para enviar notificações por push.

+ [Habilitar sincronização offline para seu aplicativo](app-service-mobile-xamarin-forms-get-started-offline-data.md) Saiba como adicionar suporte offline em seu aplicativo usando um back-end do aplicativo móvel. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede.

<!-- Images. -->

<!-- URLs. -->
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx

<!---HONumber=AcomDC_0629_2016-->