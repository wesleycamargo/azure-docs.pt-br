<properties
	pageTitle="Adicionar notificações por push ao aplicativo Xamarin.Forms | Microsoft Azure"
	description="Aprenda a usar os serviços do Azure para enviar notificações por push aos aplicativos Xamarin.Forms."
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
	ms.date="07/17/2016"
	ms.author="wesmc"/>

# Adicionar notificações por push ao aplicativo Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##Visão geral

Este tutorial mostra como usar os serviços do Azure para enviar notificações por push para um aplicativo Xamarin.Forms em execução nas várias plataformas de dispositivo nativas, Android, iOS e Windows. As notificações por push são enviadas de um back-end de Aplicativos Móveis do Azure usando os Hubs de Notificação do Azure. Os registros de modelo são usados para que a mesma mensagem possa ser enviada a dispositivos que estejam em execução em todas as plataformas usando vários serviços de notificação por push (PNS). Para saber mais sobre como enviar notificações por push entre plataformas, veja a documentação [Hubs de Notificação do Azure](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

Você adiciona notificações por push a todos os projetos aos quais seu aplicativo Xamarin.Forms dá suporte. Sempre que um registro for inserido no back-end, será enviada uma notificação por push.

##Pré-requisitos

Para obter o melhor resultado com este tutorial, é recomendável que você conclua primeiro o tutorial [Criar um aplicativo Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Depois de concluir este tutorial, você terá um projeto Xamarin.Forms que é um aplicativo de lista de tarefas de várias plataformas.

Se você não usar o projeto baixado do início rápido do servidor, você deve adicionar o pacote de extensão de notificação por push ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Enviar notificações por push para dispositivos iOS requer uma [associação ao Apple Developer Program](https://developer.apple.com/programs/ios/). Além disso, você deve usar um dispositivo iOS físico, já que o [simulador de iOS não dá suporte a notificações de push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

##<a name="create-hub"></a>Criar um Hub de notificação

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##Atualizar o projeto de servidor para enviar notificações por push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##(Opcional) Configurar e executar o projeto do Android

Conclua esta seção para habilitar notificações por push para o projeto Droid Xamarin.Forms para Android.


###Habilitar o Google Cloud Messaging (GCM)

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

###Configurar seu back-end de Aplicativo Móvel para enviar solicitações por push usando GCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

###Adicionar notificações por push ao projeto Droid

Com o back-end configurado para usar o Google Cloud Messaging (GCM), podemos adicionar os componentes e o código ao cliente, permitindo que o aplicativo se registre no GCM, se registre para receber notificações por push de Hubs de Notificação do Azure por meio do back-end de aplicativo móvel e receba notificações.

1. No projeto **Droid**, clique com o botão direito do mouse na pasta **Componentes**, clique em **Obter Mais Componentes...**, procure o componente **Cliente do Google Cloud Messaging** e adicione-o ao projeto. Esse componente oferece suporte a notificações por push para um projeto Android Xamarin.


2. Abra o arquivo de projeto MainActivity.cs e adicione a seguinte instrução ao topo do arquivo:

		using Gcm.Client;

3. Adicione o código a seguir ao método **OnCreate**, após a chamada a **LoadApplication**:

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
	        CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
	    }
	    catch (Exception e)
	    {
	        CreateAndShowDialog(e.Message, "Error");
	    }


4. Adicione um novo método auxiliar **CreateAndShowDialog**, desta maneira:

		private void CreateAndShowDialog(String message, String title)
		{
			AlertDialog.Builder builder = new AlertDialog.Builder(this);

			builder.SetMessage (message);
			builder.SetTitle (title);
			builder.Create().Show ();
		}


5. Adicione o código a seguir à classe **MainActivity**:

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

	Isso expõe a instância atual de **MainActivity** e, portanto, podemos executar no thread principal da interface do usuário.

6. Inicialize a variável `instance` no início do método **OnCreate** como mostrado a seguir.

		// Set the current instance of MainActivity.
		instance = this;

2. Adicione um novo arquivo de classe ao projeto **Droid** chamado `GcmService.cs` e verifique se as instruções **using** a seguir estão presentes na parte superior do arquivo:

		using Android.App;
		using Android.Content;
		using Android.Media;
		using Android.Support.V4.App;
		using Android.Util;
		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;
		using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Text;


9. Adicione as seguintes solicitações de permissão na parte superior do arquivo, após as instruções **using** e antes da declaração **namespace**.

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]

10. Adicione a seguinte definição de classe ao namespace.

		[BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
		public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
		{
		    public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
		}

	>[AZURE.NOTE]Substitua **<PROJECT\_NUMBER>** pelo número do projeto que você anotou anteriormente.

11. Substitua a classe **GcmService** vazia pelo código a seguir, que usa o novo receptor de difusão:

		 [Service]
		 public class GcmService : GcmServiceBase
		 {
		     public static string RegistrationID { get; private set; }

		     public GcmService()
		         : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
		 }


12. Adicione o código a seguir à classe **GcmService** que substitui o manipulador de eventos **OnRegistered** e implementa um método **Register**.

		protected override void OnRegistered(Context context, string registrationId)
		{
		    Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
		    RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

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

		Note that this code uses the `messageParam` parameter in the template registration. 

13. Adicione o seguinte código que implementa **OnMessage**:

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

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

	Isso manipula as notificações recebidas e as envia para o gerenciador de notificações a ser exibido.

14. **GcmServiceBase** também exige que você implemente os métodos de manipulador **OnUnRegistered** e **OnError**, o que pode ser feito da seguinte maneira:

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
		}

		protected override void OnError(Context context, string errorId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
		}

Agora, você está pronto para testar as notificações por push no aplicativo em execução em um dispositivo ou no emulador Android.

###Testar notificações por push em seu aplicativo Android

As duas primeiras etapas só serão necessárias durante o teste em um emulador.

1. Verifique se você está implantando ou depurando em um dispositivo virtual com APIs do Google definidas como destino, conforme mostrado abaixo no Gerenciador de AVD (dispositivo virtual Android).

2. Adicione uma conta do Google ao dispositivo Android clicando em **Aplicativos** > **Configurações** > **Adicionar conta**, siga os prompts para usar Adicionar uma conta existente do Google ao dispositivo para criar uma nova.

1. No Visual Studio ou Xamarin Studio, clique com botão direito no projeto **Droid** e clique em **Configurar como projeto de inicialização**.

2. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo no dispositivo ou no emulador Android.

3. No aplicativo, digite uma tarefa e clique no ícone do sinal de adição (**+**).

4. Verifique se uma notificação é recebida quando um item é adicionado.


##(Opcional) Configurar e executar o projeto do iOS

Esta seção trata da execução do projeto do iOS Xamarin para dispositivos iOS. Você poderá ignorá-la se não estiver trabalhando com dispositivos iOS.

[AZURE.INCLUDE [notification-hubs-xamarin-enable-apple-push-notifications](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]


####Configurar o hub de notificação para APNS

1. Faça logon no [Portal do Azure](https://portal.azure.com/). Clique em **Procurar** > **Aplicativos Móveis** > seu aplicativo móvel > **Configurações** > **Push** > **Apple (APNS)** > **Carregar Certificado**. Carregue o arquivo de certificado de push .p12 exportado anteriormente. Selecione **Sandbox** se tiver criado um certificado de push de desenvolvimento para desenvolvimento e teste. Caso contrário, escolha **Produção**. Agora, seu serviço móvel está configurado para funcionar com notificações por push para iOS.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)


	Em seguida, você definirá a configuração de projeto do iOS no Xamarin Studio ou no Visual Studio.

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####Adicionar as notificações por push ao seu aplicativo iOS

1. No projeto **iOS**, abra AppDelegate.cs adicione a seguinte instrução **using** à parte superior do arquivo de código.

        using Newtonsoft.Json.Linq;

4. Na classe **AppDelegate**, adicione também uma substituição ao evento **RegisteredForRemoteNotifications** a fim de registrar para o recebimento notificações:

        public override void RegisteredForRemoteNotifications(UIApplication application, 
			NSData deviceToken)
        {
            const string templateBodyAPNS = "{"aps":{"alert":"$(messageParam)"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. Em **AppDelegate**, adicione também a seguinte substituição para o manipulador de eventos **DidReceivedRemoteNotification**:

        public override void DidReceiveRemoteNotification(UIApplication application, 
			NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
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

	Este método trata as notificações recebidas enquanto o aplicativo está em execução.

2. Na classe **AppDelegate**, adicione o seguinte código ao método **FinishedLaunching**:

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

	Isso habilita o suporte para notificações remotas e solicitações de registro por push.

Seu aplicativo foi atualizado para oferecer suporte a notificações de push.

####Testar notificações por push em seu aplicativo iOS

1. Clique com o botão direito no projeto do iOS e clique em **Definir como Projeto de Inicialização**.

2. Pressione o botão **Executar** ou **F5** no Visual Studio para compilar o projeto e iniciar o aplicativo em um dispositivo iOS; em seguida, clique em **OK** para aceitar as notificações por push.

	> [AZURE.NOTE] Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

3. No aplicativo, digite uma tarefa e clique no ícone do sinal de adição (**+**).

4. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.


##(Opcional) Configurar e executar os projetos do Windows

Esta seção trata da execução dos projetos WinApp e WinPhone81 de Xamarin.Forms para dispositivos Windows. Estas etapas também oferecem suporte a projetos da Plataforma Universal do Windows (UWP). Você poderá ignorá-la se não estiver trabalhando com dispositivos Windows.


####Registrar seu aplicativo Windows para notificações por push com WNS

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####Configurar o hub de notificação para WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####Adicionar notificações por push ao seu aplicativo do Windows

1. No Visual Studio, abra **App.xaml.cs** em um projeto do Windows e adicione as instruções **using** a seguir.

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using Windows.Networking.PushNotifications;
		using <your_TodoItemManager_portable_class_namespace>;

	Substitua `<your_TodoItemManager_portable_class_namespace>` pelo namespace do seu projeto portátil que contenha a classe `TodoItemManager`.
 

2. Em App.xaml.cs, adicione o seguinte método **InitNotificationsAsync**:

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = 
				"<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
			{
				{"body", templateBodyWNS},
				{"headers", headers} // Needed for WNS.
			};

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
				.RegisterAsync(channel.Uri, templates);
        }

	Esse método obtém o canal de notificação por push e registra um modelo para receber notificações de modelo do seu hub de notificação. Uma notificação de modelo que oferece suporte a *messageParam* será entregue a esse cliente.

3. Em App.xaml.cs, atualize a definição do método de manipulador de eventos **OnLaunched** ao adicionar o modificador `async` e adicione a linha de código a seguir ao final do método:

        await InitNotificationsAsync();

	Isso garante que o registro da notificação por push será criado ou atualizado sempre que o aplicativo for iniciado. É importante fazer isso para garantir que o canal de notificação por push WNS esteja sempre ativo.

4. No Gerenciador de Soluções do Visual Studio, abra o arquivo **Package.appxmanifest** e defina **Compatível com Notificação do Sistema** como **Sim** em **Notificações**.

5. Compile o aplicativo e verifique se não há erros. Agora, o aplicativo cliente deve se registrar para notificações de modelo no back-end do Aplicativo Móvel. Repita esta seção para cada projeto do Windows em sua solução.


####Testar as notificações por push em seu aplicativo para Windows

1. No Visual Studio, clique com botão direito no projeto do Windows e clique em **Definir como projeto de inicialização**.

2. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo.

3. No aplicativo, digite um nome para um novo todoitem e, em seguida, clique no ícone de adição (**+**) para adicioná-lo.

4. Verifique se uma notificação é recebida quando o item é adicionado.

##Próximas etapas

Saiba mais sobre as notificações por push:

* [Trabalhar com o SDK do servidor de back-end do .NET para aplicativos móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags) marcas permitem que você vise clientes segmentados com envios. Saiba como adicionar marcas a uma instalação de dispositivo.

* [Diagnosticar problemas com notificações por push](../notification-hubs/notification-hubs-push-notification-fixer.md) há vários motivos por que as notificações podem ser abandonadas ou não irem terminar nos dispositivos. Este tópico mostra como analisar e descobrir a causa de falhas de notificação por push.

Considere a possibilidade de prosseguir com um dos seguintes tutoriais:

* [Adicionar autenticação ao aplicativo](app-service-mobile-xamarin-forms-get-started-users.md) Saiba como autenticar os usuários do aplicativo com um provedor de identidade.

* [Habilitar sincronização offline para seu aplicativo](app-service-mobile-xamarin-forms-get-started-offline-data.md) Saiba como adicionar suporte offline ao seu aplicativo usando um back-end de Aplicativo Móvel. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!---HONumber=AcomDC_0720_2016-->