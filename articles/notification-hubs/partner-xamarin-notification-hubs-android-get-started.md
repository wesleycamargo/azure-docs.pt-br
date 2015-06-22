<properties
	pageTitle="Introdução aos Hubs de Notificação para aplicativos Xamarin.Android"
	description="Saiba como usar os Hubs de notificação do Azure para enviar notificações por push para um aplicativo Android Xamarin."
	authors="ysxu"
	manager="dwrede"
	editor=""
	services="notification-hubs"
	documentationCenter="xamarin"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="04/14/2015"
	ms.author="yuaxu"/>

# Introdução aos Hubs de Notificação

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## Visão geral

Este tópico mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo Xamarin Android. Neste tutorial, você cria um aplicativo em branco para Xamarin Android que recebe notificações por push usando o Google Cloud Messaging (GCM). Ao concluir, você poderá transmitir notificações por push a todos os dispositivos que executam seu aplicativo usando o hub de notificação. O código concluído está disponível na amostra do [aplicativo NotificationHubs][GitHub].

Este tutorial demonstra o simples cenário de transmissão usando Hubs de Notificação.

## Pré-requisitos

Este tutorial exige o seguinte:

+ [Xamarin.Android]
+ Conta ativa do Google
+ [Componente dos Serviços Móveis do Azure]
+ [Componente de mensagens do Azure]
+ [Componente de mensagens de nuvem do Google]

Completar este tutorial é um pré-requisito para todos os outros tutoriais de hub de notificação para aplicativos Xamarin.Android.

> [AZURE.IMPORTANT]Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).

## <a name="register"></a>Habilitar as mensagens em nuvem do Google

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

## <a name="configure-hub"></a>Configurar seu Hub de Notificação

[AZURE.INCLUDE [notification-hubs-android-configure-push](../../includes/notification-hubs-android-configure-push.md)]

## <a name="connecting-app"></a>Conectar seu aplicativo ao Hub de Notificação

### Criar um novo projeto

1. No Xamarin Studio (ou Visual Studio), clique em **Arquivo** e em **Novo** e, em seguida, clique em **Aplicativo Android** na caixa de diálogo **Nova solução** e por fim clique em **OK**.

   ![][14]

	Isso cria um novo projeto do Android.

2. Abra as propriedades do projeto clicando com o botão direito em seu novo projeto no modo de exibição Solução e escolha **Opções**. Selecione o item **Aplicativo Android** na seção **Compilação**.

   ![][15]

3. Definir a **versão mínima do Android** no nível API 8.

4. Definir o **versão destino Android** para a versão de API você gostaria de ter como destino (deve ser API nível 8 ou superior).

5. Certifique-se de que a primeira letra de seu **nome do pacote** esteja em minúscula.

	> [AZURE.IMPORTANT]A primeira letra do nome do pacote deve estar em minúscula. Caso contrário, você receberá erros de manifesto do aplicativo ao registrar **BroadcastReceiver** e **IntentFilter**para notificações por push abaixo.

### Adicione os componentes necessários ao seu projeto

O cliente de mensagens de nuvem do Google disponível na loja de componentes Xamarin simplifica o processo de oferecer suporte de notificações por push no Xamarin.Android.

1. Clique com o botão direito do mouse na pasta Componentes no aplicativo Xamarin.Android e escolha **Obter mais componentes...**

2. Pesquise o componente **Serviços Móveis do Azure** e adicione-o ao projeto.

3. Pesquise o componente **Mensagens do Azure** e adicione-o ao projeto.

4. Pesquise o componente **Cliente de Mensagens de Nuvem do Azure** e adicione-o ao projeto.


### Instalar Hubs de notificação em seu projeto

1. Criar uma classe **Constants.cs** e definir os seguintes valores de constantes (substituindo os espaços reservados por valores):

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<hub path>";

2. Adicionar as seguintes instruções à **MainActivity.cs**:

		using Microsoft.WindowsAzure.MobileServices;
		using Gcm.Client;

3. Criar o seguinte método na classe **MainActivity**:

		private void RegisterWithGCM()
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. Criar uma nova classe **MyBroadcastReceiver**.

	> [AZURE.NOTE]Vamos abordar a criação de um **BroadcastReceiver** do zero. No entanto, uma alternativa rápida para criar manualmente o **MyBroadcastReceiver.cs** abaixo é referir-se ao arquivo **GcmService.cs** encontrado no projeto de exemplo Xamarin.Android no GitHub. Duplicar o **GcmService.cs** e alterar os nomes de classe podem ser uma ótima maneira de começar.

5. Adicionar as seguintes instruções using para **MyBroadcastReceiver.cs** (para se referir ao componente e assembly adicionados anteriormente):

		using WindowsAzure.Messaging;
		using Gcm.Client;

5. Adicionar as solicitações de permissão a seguir entre as instruções **using** e a declaração **namespace**:

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. Em **MyBroadcastReceiver.cs** alterar a classe **MyBroadcastReceiver** para corresponder com o seguinte:

    	[BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, 
			Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, 
			Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, 
			Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

7. Adicionar outra classe em **MyBroadcastReceiver.cs** chamada **PushHandlerService** que deriva de **GcmServiceBase**. Certifique-se de aplicar o atributo **Service** à classe:

    	[Service] // Must use the service tag
    	public class PushHandlerService : GcmServiceBase
    	{
        	public static string RegistrationID { get; private set; }
        	private NotificationHub Hub { get; set; }

        	public PushHandlerService() : base(Constants.SenderID)
    { Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor"); } }


8. **GcmServiceBase** implementa métodos **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** e **OnError()**. Nossa classe de implementação **PushHandlerService** deve substituir esses métodos e esses métodos serão acionados em resposta à interação com o hub de notificação.

9. Substituir o método **OnRegistered()** em **PushHandlerService** pelo seguinte código:

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("PushHandlerService-GCM Registered...", "The device has been Registered, Tap to View!");

            Hub = new NotificationHub (Constants.NotificationHubPath, Constants.ConnectionString);
            try
            {
                Hub.UnregisterAll(registrationId);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }

            var tags = new List<string>() { "falcons" }; // create tags if you want

            try
            {
                var hubRegistration = Hub.RegisterNative (registrationId, tags);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

	> [AZURE.NOTE]No código **OnRegistered()** acima que você deve observar a capacidade de especificar marcas para registrar para canais de mensagens específicos.

10. Substituir o método **OnRegistered** em **PushHandlerService** pelo seguinte código:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(messageText))
            {
                createNotification("New hub message!", messageText);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

11. Adicionar o seguinte método **createNotification** a **PushHandlerService** para notificar os usuários conforme usado acima:

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

12. Substituir membros abstratos **OnUnRegistered()**, **OnRecoverableError()** e **OnError()** para que seu código seja compilado.


## <a name="run-app"></a>Executar o aplicativo no emulador

Quando você executa o aplicativo no emulador, certifique-se de usar um Android Virtual Device (AVD) que oferece suporte a APIs do Google.

	> [AZURE.IMPORTANT] Para receber as notificações por push, você deve configurar uma conta do Google em seu Dispositivo Virtual para Android (no emulador, navegue até **Configurações** e clique em **Adicionar Conta**). Além disso, certifique-se de que o emulador esteja conectado à Internet.

1. A partir de **Ferramentas**, clique em **Abrir gerenciador de emulador do Android**, selecione seu dispositivo e, em seguida, clique em **Editar**.

   ![][18]

2. Selecione **Google APIs** em **Destino** e clique em **OK**.

   ![][19]

3. Na barra de ferramentas superior, clique em **Executar**e, em seguida, selecione seu aplicativo. Isso iniciará o emulador e executará o aplicativo.

  O aplicativo recupera o *registrationId* do GCM e registrará no Hub de Notificação.

<a name="send"></a>Enviar notificação de seu back-end

Você pode enviar notificações usando Hubs de Notificação de qualquer back-end usando a <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>. Neste tutorial, você enviará notificações com um aplicativo de console .NET e com um Serviço Móvel usando um script de nó.

Para enviar notificações usando um aplicativo .NET:

1. Crie um novo aplicativo de console do Visual C#:

   ![][20]

2. Adicione uma referência ao SDK do Barramento de Serviço do Azure com o <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacote NuGet do WindowsAzure.ServiceBus</a>. No menu principal do Visual Studio, clique em **Ferramentas**, em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**. Na janela do console, digite:

        Install-Package WindowsAzure.ServiceBus

    e pressione Enter.

2. Abra o arquivo Program.cs e adicione a seguinte instrução:

        using Microsoft.ServiceBus.Notifications;

3. Em sua classe `Program`, adicione o método a seguir:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ "data" : {"msg":"Hello from Azure!"}}");
        }

4. Em seguida, adicione as seguintes linhas em seu método Main:

         SendNotificationAsync();
		 Console.ReadLine();

5. Pressione a tecla F5 para executar o aplicativo. Você deve receber uma notificação do sistema.

   ![][21]

Para enviar uma notificação usando um Serviço Móvel, consulte a [Introdução aos Serviços Móveis] e faça o seguinte:

1. Faça logon no [Portal de Gerenciamento do Azure] e selecione seu Serviço Móvel.

2. Selecione a guia **Agendador** na parte superior.

   ![][22]

3. Crie um novo trabalho agendado, insira um nome e selecione **Sob demanda**.

   ![][23]

4. Quando o trabalho for criado, clique no nome do trabalho. Em seguida, clique na guia **Script** na barra superior.

5. Insira o script a seguir em sua função de Agendador. Certifique-se de substituir os espaços reservados pelo nome de seu hub de notificação e pela cadeia de conexão para a *DefaultFullSharedAccessSignature* que você obteve anteriormente. Clique em **Salvar**.

        var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
		notificationHubService.gcm.send(null,'{"data":{"msg" : "Hello from Mobile Services!"}}',
    	  function (error)
    	  {
        	if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
	    );

6. Clique em **Executar uma vez** na barra inferior. Você deve receber uma notificação do sistema.

## <a name="next-steps"> </a>Próximas etapas

Neste simples exemplo, você envia notificações para todos os seus dispositivos Android. Para selecionar usuários de destinos específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá consultar [Usar Hubs de Notificação para enviar notícias de última hora]. Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação] e em [Instruções sobre Hubs de Notificação para Android].

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-developers.png
[2]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server.png
[3]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server2.png
[4]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server3.png

[7]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[12]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[14]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app2.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Instruções sobre Hubs de Notificação para Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Usar Hubs de Notificação para enviar notificações por push aos usuários]: /manage/services/notification-hubs/notify-users-aspnet
[Usar Hubs de Notificação para enviar notícias de última hora]: /manage/services/notification-hubs/breaking-news-dotnet
[GCMClient Component page]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub GitHub page]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.Android]: http://xamarin.com/download/
[Componente dos Serviços Móveis do Azure]: http://components.xamarin.com/view/azure-mobile-services/
[Componente de mensagens de nuvem do Google]: http://components.xamarin.com/view/GCMClient/
[Componente de mensagens do Azure]: http://components.xamarin.com/view/azure-messaging
<!--HONumber=52-->
 