<properties 
	pageTitle="Adicionar notificações por push a seu aplicativo Xamarin.Forms com o Serviço de Aplicativo do Azure" 
	description="Aprenda a usar o Serviço de Aplicativo do Azure para enviar notificações por push para o aplicativo Xamarin.Forms" 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="wesmc7777"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="11/23/2015" 
	ms.author="wesmc"/>

# Adicionar notificações por push ao aplicativo Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Visão geral

Este tutorial tem base no [tutorial de início rápido do Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md) que você deve concluir primeiro. Você adicionará suporte para notificações por push para cada projeto ao qual queira oferecer suporte no projeto de início rápido do Xamarin.Forms. Sempre que um registro for inserido, uma notificação por push será enviada.

Se você não usar o projeto baixado do início rápido do servidor, você deve adicionar o pacote de extensão de notificação por push ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

O [simulador de iOS não dá suporte a notificações de push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html); portanto, você deve usar um dispositivo físico com iOS. Você também precisará de uma [assinatura do programa de desenvolvedores da Apple](https://developer.apple.com/programs/ios/).

##Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você ainda não tiver uma conta, inscreva-se para uma avaliação do Azure e obtenha até 10 aplicativos móveis grátis. Você pode continuar usando-os até mesmo após o término de sua avaliação. Consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

* Um Mac com [Xamarin Studio] e [Xcode] v 4.4 ou posterior instalado. Se quiser, você poderá executar o aplicativo Xamarin.Forms usando o Visual Studio em um computador com Windows, mas isso é um pouco mais complicado, pois você precisa se conectar a um MAC em rede executando o Xamarin.iOS Build Host. Se você estiver interessado em fazer isso, consulte [Instalando o Xamarin.iOS no Windows].

* Um dispositivo físico iOS. Não há suporte para notificações por push pelo simulador do iOS.

* Complete o [Tutorial de início rápido do Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md)

##Criar um Hub de notificação para seu aplicativo móvel

Para configurar seu aplicativo a fim de enviar notificações, crie um novo hub de notificação e configure-o para os serviços de notificação de plataforma que você usará.

Essas etapas orientarão você pela criação de um novo hub de notificação. Se você já tiver criado um, basta selecioná-lo.

1. Faça logon no [Portal do Azure](https://portal.azure.com/). Clique em **Procurar** > **Aplicativos Móveis** > seu back-end > **Configurações** > **Móvel** > **Push** > **Hub de Notificação** > **+ Hub de Notificação** e forneça um nome e namespace para um novo hub de notificação. Em seguida, clique no botão **OK**.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. Na folha Criar Hub de Notificação, clique em **Criar**.


##Atualizar o projeto de servidor para enviar notificações por push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##Implantar o projeto de servidor atualizado no Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]


## Atualizar o projeto de biblioteca de classes portáteis. 

A classe `TodoItemManager` definida no projeto compartilhado encapsula a conexão do cliente ao back-end do Aplicativo Móvel, juntamente com as operações que desejamos executar na tabela hospedada no back-end do Aplicativo Móvel. Exporemos a conexão do cliente para podermos nos registrar para notificações por push.

1. No Visual Studio ou Xamarin Studio, abra TodoItemManager.cs no projeto compartilhado. Adicione o seguinte membro estático e acessadores à classe `TodoItemManager`. Usaremos isso para acessar o `MobileServiceClient` quando precisarmos obter o objeto `Microsoft.WindowsAzure.MobileServices.Push` específico à plataforma. 

        static TodoItemManager defaultInstance = null;

        public static TodoItemManager DefaultInstance
        {
            get
            {
                return defaultInstance;
            }
            private set
            {
                defaultInstance = value;
            }
        }

		public MobileServiceClient CurrentClient
		{
			get { return client; }
		}


2. Adicione o código para inicializar `DefaultInstance` no início do construtor para a classe `TodoItemManager`.

        DefaultClient = this;




##(Opcional) Configurar e executar o projeto do Android

Esta seção trata da execução do projeto droid Xamarin para Android. Você poderá ignorá-la se não estiver trabalhando com dispositivos Android.


####Habilitar o Google Cloud Messaging (GCM)


[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


####Configurar o hub de notificação para o GCM

1. Faça logon no [Portal do Azure](https://portal.azure.com/). Clique em **Procurar** > **Aplicativos Móveis** > seu aplicativo móvel > **Configurações** > **Push** > **Google (GCM)**. Cole a chave de API do servidor criada anteriormente e clique em **Salvar**. Agora, seu serviço móvel está configurado para funcionar com notificações por push no Android.

	![](./media/app-service-mobile-xamarin-forms-get-started-push/mobile-app-save-gcm-api-key.png)


####Adicionar notificações por push projeto droid

1. Clique com o botão direito do mouse na pasta Componentes, clique em Obter Mais Componentes..., procure o componente **Cliente do Google Cloud Messaging** e adicione-o ao projeto. Esse componente ajuda a simplificar o trabalho com notificações por push em um projeto do Xamarrin Android.

2. Abra o arquivo de projeto MainActivity.cs e adicione a seguinte instrução ao topo do arquivo:

		using Gcm.Client;

3. Adicione o seguinte código ao método `OnCreate` após a chamada para `LoadApplication`

	    try
	    {
	        // Check to ensure everything's setup right
	        GcmClient.CheckDevice(this);
	        GcmClient.CheckManifest(this);
	
	        // Register for push notifications
	        System.Diagnostics.Debug.WriteLine("Registering...");
	        GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
	    }
	    catch (Java.Net.MalformedURLException)
	    {
	        CreateAndShowDialog("There was an error creating the Mobile Service. Verify the URL", "Error");
	    }
	    catch (Exception e)
	    {
	        CreateAndShowDialog(e.Message, "Error");
	    }


4. Adicione o seguinte código ao método auxiliar `CreateAndShowDialog`.

		private void CreateAndShowDialog(String message, String title) 
		{
			AlertDialog.Builder builder = new AlertDialog.Builder(this);

			builder.SetMessage (message);
			builder.SetTitle (title);
			builder.Create().Show ();
		}


5. Na classe `MainActivity`, adicione o seguinte código a fim de expor o `MainActivity` atual, para que possamos executar algumas interfaces do usuário no thread da interface do usuário principal:
		
		// Create a new instance field for this activity.
		static MainActivity instance = null;
		
		// Return the current activity instance.
		public static MainActivity CurrentActivity
		{
		    get
		    {
		        return instance;
		    }
		}

6. Inicialize a variável `instance` no início do método `MainActivity.OnCreate`.

		// Set the current instance of MainActivity.
		instance = this;

7. Adicione um novo arquivo de classe ao projeto **Droid**. Nomeie o novo arquivo de classe **GcmService**.

8. Inclua as instruções `using` na parte superior do arquivo:

		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;
		using Android.App;
		using Android.Content;
		using System.Collections.Generic;
		using System.Diagnostics;
		using Android.Util;
		using Newtonsoft.Json.Linq;
		using System.Text;
		using System.Linq;

9. Adicione as seguintes solicitações de permissão na parte superior do arquivo, após as instruções `using` e antes da declaração `namespace`.

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
		
		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

10. Adicione a seguinte definição de classe ao namespace. Substitua **<PROJECT_NUMBER>** pelo número do projeto que você anotou anteriormente.

		[BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]		
		public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
		{		
		    public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };		
		}

11. Atualize sua classe `GcmService` para usar o novo receptor de difusão.

		 [Service]
		 public class GcmService : GcmServiceBase
		 {
		     public static string RegistrationID { get; private set; }
		
		     public GcmService()
		         : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
		 }


12. Adicione o código a seguir à classe GcmService que substitui o manipulador de eventos OnRegistered e implementa um método `Register`.

	Esse código registrará um corpo de modelo para receber notificações de modelo usando o parâmetro `messageParam`. Notificações de modelo permitem que você envie notificações entre plataformas. Para saber mais, consulte [Modelos](https://msdn.microsoft.com/library/azure/dn530748.aspx).
		
		protected override void OnRegistered(Context context, string registrationId)
		{
		    Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
		    RegistrationID = registrationId;
		
		    createNotification("GcmService Registered...", "The device has been Registered, Tap to View!");
		
            var push = TodoItemManager.DefaultInstance.CurrentClient.GetPush();
		
		    MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
		
		}

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{"data":{"message":"$(messageParam)"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyGCM}
                };
                
                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

13. Você deve implementar `OnMessage` para lidar com uma notificação por push recebida. Nesse código trataremos a notificação e a enviaremos ao gerenciador de notificação.

		protected override void OnMessage(Context context, Intent intent)
		{
		    Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");
		
		    var msg = new StringBuilder();
		
		    if (intent != null && intent.Extras != null)
		    {
		        foreach (var key in intent.Extras.KeySet())
		            msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
		    }
		
		    //Store the message
		    var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
		    var edit = prefs.Edit();
		    edit.PutString("last_msg", msg.ToString());
		    edit.Commit();
		
		    string message = intent.Extras.GetString("message");
		    if (!string.IsNullOrEmpty(message))
		    {
		        createNotification("New todo item!", "Todo item: " + message);
		        return;
		    }
		
		    string msg2 = intent.Extras.GetString("msg");
		    if (!string.IsNullOrEmpty(msg2))
		    {
		        createNotification("New hub message!", msg2);
		        return;
		    }
		
		    createNotification("Unknown message details", msg.ToString());
		}
		
		void createNotification(string title, string desc)
		{
		    //Create notification
		    var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;
		
		    //Create an intent to show ui
		    var uiIntent = new Intent(this, typeof(MainActivity));
		
		    //Create the notification
		    var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);
		
		    //Auto cancel will remove the notification once the user touches it
		    notification.Flags = NotificationFlags.AutoCancel;
		
		    //Set the notification info
		    //we use the pending intent, passing our ui intent over which will get called
		    //when the notification is tapped.
		    notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));
		
		    //Show the notification
		    notificationManager.Notify(1, notification);
		}
	
14. Você também deve implementar os manipuladores `OnUnRegistered` e `OnError` para o receptor.

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
		}

		protected override void OnError(Context context, string errorId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
		}



####Testar notificações por push em seu aplicativo Android

1. No Visual Studio ou Xamarin Studio, clique com botão direito no projeto **droid** e clique em **Configurar como projeto de inicialização**.
 
2. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo em um dispositivo compatível com iOS; em seguida, clique em **OK** para aceitar as notificações por push.
	
	> [AZURE.NOTE]Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

2. No aplicativo, digite uma tarefa e clique no ícone do sinal de adição (**+**).

3. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.





##(Opcional) Configurar e executar o projeto do iOS

Esta seção trata da execução do projeto do iOS Xamarin para dispositivos iOS. Você poderá ignorá-la se não estiver trabalhando com dispositivos iOS.

[AZURE.INCLUDE [Hubs de notificação Xamarin habilitam as notificações por push da Apple](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]


####Configurar o hub de notificação para APNS

1. Faça logon no [Portal do Azure](https://portal.azure.com/). Clique em **Procurar** > **Aplicativos Móveis** > seu aplicativo móvel > **Configurações** > **Push** > **Apple (APNS)** > **Carregar Certificado**. Carregue o arquivo de certificado de push .p12 exportado anteriormente. Selecione **Sandbox** se você tiver criado um certificado de push de desenvolvimento para desenvolvimento e teste. Caso contrário, escolha **Produção**. Agora, seu serviço móvel está configurado para funcionar com notificações por push para iOS.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)


	Em seguida, você definirá a configuração de projeto do iOS no Xamarin Studio ou no Visual Studio.

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####Adicionar as notificações por push ao seu aplicativo iOS

1. Adicione a seguinte declaração `using` ao topo do arquivo **AppDelegate.cs**.

        using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;


2. No projeto do iOS, abra AppDelegate.cs e atualize`FinishedLaunching` para oferecer suporte a notificações remotas, da seguinte maneira.

		public override bool FinishedLaunching (UIApplication app, NSDictionary options)
		{
			global::Xamarin.Forms.Forms.Init ();

			Microsoft.WindowsAzure.MobileServices.CurrentPlatform.Init();

            // IMPORTANT: uncomment this code to enable sync on Xamarin.iOS
            // For more information, see: http://go.microsoft.com/fwlink/?LinkId=620342
            //SQLitePCL.CurrentPlatform.Init();
            
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

			LoadApplication (new App ());

			return base.FinishedLaunching (app, options);
		}


4. Em AppDelegate.cs, adicione também uma substituição para o evento **RegisteredForRemoteNotifications** a fim de se registrar para notificações:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            const string templateBodyAPNS = "{"aps":{"alert":"$(messageParam)"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultInstance.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. Em AppDelegate.cs adicione também uma substituição para o evento **DidReceivedRemoteNotification** a fim de tratar notificações recebidas enquanto o aplicativo está em execução:

        public override void DidReceiveRemoteNotification(UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

Seu aplicativo foi atualizado para oferecer suporte a notificações de push.

####Testar notificações por push em seu aplicativo iOS

1. Clique com o botão direito no projeto do iOS e clique em **Definir como Projeto de Inicialização**.

2. Pressione o botão **Executar** ou **F5** no Visual Studio para compilar o projeto e iniciar o aplicativo em um dispositivo compatível com iOS; em seguida, clique em **OK** para aceitar as notificações por push.
	
	> [AZURE.NOTE]Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

3. No aplicativo, digite uma tarefa e clique no ícone do sinal de adição (**+**).

4. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.




##(Opcional) Configurar e executar o projeto do Windows

Esta seção trata da execução do projeto WinApp Xamarin para dispositivos Windows. Você poderá ignorá-la se não estiver trabalhando com dispositivos Windows.


####Registrar seu aplicativo Windows para notificações por push com WNS

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####Configurar o hub de notificação para WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####Adicionar notificações por push ao seu aplicativo do Windows

1. No Visual Studio, abra **App.xaml.cs** no projeto **WinApp**. Adicione as seguintes instruções `using`.

		using System.Threading.Tasks;
		using Windows.Networking.PushNotifications;
		using WesmcMobileAppGaTest;
		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;

2. No App.xaml.cs, adicione o seguinte método `InitNotificationsAsync`. Esse método obtém o canal de notificação por push e registra um modelo para receber notificações de modelo do hub de notificação. Uma notificação de modelo que oferece suporte a `messageParam` será entregue a esse cliente.

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = "<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyWNS},
                  {"headers", headers} // Only needed for WNS & MPNS
                };

            await TodoItemManager.DefaultInstance.CurrentClient.GetPush().RegisterAsync(channel.Uri, templates);
        }

3. Em App.xaml.cs, atualize o manipulador de eventos `OnLaunched` com o atributo `async` e chame `InitNotificationsAsync`

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            Frame rootFrame = Window.Current.Content as Frame;

            // Do not repeat app initialization when the Window already has content,
            // just ensure that the window is active
            if (rootFrame == null)
            {
                // Create a Frame to act as the navigation context and navigate to the first page
                rootFrame = new Frame();
                // Set the default language
                rootFrame.Language = Windows.Globalization.ApplicationLanguages.Languages[0];
                rootFrame.NavigationFailed += OnNavigationFailed;
                Xamarin.Forms.Forms.Init(e); 
                if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
                {
                    //TODO: Load state from previously suspended application
                }
                // Place the frame in the current Window
                Window.Current.Content = rootFrame;
            }

            if (rootFrame.Content == null)
            {
                // When the navigation stack isn't restored navigate to the first page,
                // configuring the new page by passing required information as a navigation
                // parameter
                rootFrame.Navigate(typeof(MainPage), e.Arguments);
            }
            // Ensure the current window is active
            Window.Current.Activate();

            await InitNotificationsAsync();
        }

4. No Gerenciador de Soluções do Visual Studio, abra o arquivo **Package.appxmanifest** e defina **Compatível com Notificação do Sistema** como **Sim** em **Notificações**.

5. Compile o aplicativo e verifique se não há erros. Agora, o aplicativo cliente deve se registrar para notificações de modelo no back-end do Aplicativo Móvel.


####Testar as notificações por push em seu aplicativo para Windows

1. No Visual Studio, clique com botão direito no projeto **WinApp** e clique em **Definir como projeto de inicialização**.


2. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo em um dispositivo compatível com iOS; em seguida, clique em **OK** para aceitar as notificações por push.
	
	> [AZURE.NOTE]Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

3. No aplicativo, digite uma tarefa e clique no ícone do sinal de adição (**+**).

4. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.



<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Instalando o Xamarin.iOS no Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333


 

<!---HONumber=AcomDC_1125_2015-->