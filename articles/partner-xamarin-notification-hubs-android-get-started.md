<properties linkid="" urlDisplayName="" pageTitle="Introdução aos Hubs de Notificação para aplicativos Xamarin.Android" metaKeywords="" description="Saiba como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo Xamarin Android." metaCanonical="" authors="elioda" solutions="" manager="" editor="" services="mobile-services,notification-hubs" documentationCenter="" title="Introdução aos Hubs de Notificação" />

# Introdução aos Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/manage/services/notification-hubs/getting-started-windows-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/pt-br/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/pt-br/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

Este tópico mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo Xamarin Android. 
Neste tutorial, você cria um aplicativo em branco para Xamarin Android que recebe notificações por push usando o Google Cloud Messaging (GCM). Ao concluir, você poderá transmitir notificações por push a todos os dispositivos que executarem seu aplicativo usando o hub de notificação. O código concluído está disponível na amostra do [aplicativo NotificationHubs][GitHub].

O tutorial explica as seguintes etapas básicas para habilitar as notificações por push:

1. [Habilitar a mensagem em nuvem do Google]
2. [Configurar seu Hub de Notificação]
3. [Conectando seu aplicativo ao Hub de Notificação]
4. [Executar o aplicativo com o emulador]
5. [Enviar notificações a partir de seu back-end]

Este tutorial demonstra o simples cenário de transmissão usando Hubs de Notificação. <!-- Be sure to follow along with the next tutorial to see how to use notification hubs to address specific users and groups of devices. --> Este tutorial exige o seguinte:

+ Xamarin.Android
+ Conta ativa do Google

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre hubs de notificação para aplicativos do Android. 

<div class="dev-callout"><strong>Observação</strong> <p>Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F" target="_blank">Avaliação gratuita do Azure</a>.</p></div>

<h2><a name="register"></a><span class="short-header">Ativar Google Cloud Messaging</span>Ativar Google Cloud Messaging</h2>

<p></p>

<div class="dev-callout"><b>Observação</b>
<p>Para concluir o procedimento neste tópico, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.</p>
</div> 

1. Vá até o site <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Apis do Google</a>, faça logon com suas credenciais de conta do Google e clique em **Criar projeto...**.

   	![][1]   
	
	<div class="dev-callout"><b>Observação</b>
	<p>Quando você já tiver um projeto existente, você será direcionado para a página do <strong>Painel</strong> após o login. Para criar um novo projeto do painel, expanda <strong>API do projeto</strong>, clique em <strong>Create...</strong> em <strong>Outros Projetos</strong> e digite um nome de projeto e clique em <strong>Criar projeto</strong>.</p>
    </div>

2. Clique em **Visão Geral** na coluna esquerda e anote o número do projeto na seção **Painel**. 

	Posteriormente no tutorial você define esse valor como a variável PROJECT_ID no cliente.

3. Na página <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Apis do Google</a>, clique em **Serviços** e depois alterne para ativar o **Google Cloud Messaging para Android** e aceite os termos de serviço. 

4. Clique em **Acesso às APIs**e em **Criar nova chave de Servidor...** 

   	![][2]

5. Em **Configurar Chave do Servidor para a API do projeto**, Clique em **Criar**.

   	![][3]

6. Anote o valor da **chave da API**.

   	![][4] 

Em seguida, você usará este valor de chave da API para ativar seu hub de notificação, fazer a autenticação com o GCM e enviar notificações por push em nome de seu aplicativo.

<h2><a name="configure-hub"></a><span class="short-header">Configurar seu Hub de Notificação</span>Configurar seu Hub de Notificação</h2>

1. Faça logon no [Portal de Gerenciamento do Azure] e clique em **+NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativo**, em **Service Bus**, em **Hub de Notificação** e em **Criação Rápida**.

   	![][7]

3. Digite um nome para seu hub de notificação, selecione a região desejada e clique em **Criar novo Hub de Notificação**.

   	![][8]

4. Clique no namespace que você acabou de criar (geralmente o ***nome do hub de notificação* -ns**) e clique na guia **Configurar** na parte superior.

   	![][9]

5. Clique na guia **Hubs de Notificação** na parte superior e clique no hub de notificação que você acabou de criar.

   	![][10]

6. Clique na guia **Configurar** na parte superior, digite o valor de **Chave da API** que você obteve na etapa anterior e clique em **Salvar**.

   	![][11]

7. Selecione a guia **Painel** na parte superior e clique em **Informações de Conexão**. Anote as duas cadeias de conexão.

   	![][12]

Seu hub de notificação agora está configurado para funcionar com o GCM e você tem as cadeias de conexão para registrar seu aplicativo e enviar notificações por push.

<h2><a name="connecting-app"></a><span class="short-header">Conectando seu aplicativo</span>Conectando seu aplicativo ao Hub de Notificação</h2>

### Criar um novo projeto

1. No Xamarin Studio (ou Visual Studio), crie um novo projeto Android (Arquivo, Novo, Solução, Aplicativo Android).

   	![][13]   
   	![][14]

2. Abra as propriedades do projeto clicando com o botão direito em seu novo projeto no modo de exibição Solução e escolha **Opções**. Selecione o item **Aplicativo Android** na seção **Compilação**.

   	![][15]

3. Definir a **versão mínima do Android** no nível API 8.

4. Definir o **versão destino Android** para a versão de API você gostaria de ter como destino (deve ser API nível 8 ou superior).

5. Certifique-se de que a primeira letra de seu **nome do pacote** esteja em minúscula.

	<div class="dev-callout"><b>Observação</b>
    <p>A primeira letra do nome do pacote deve estar em minúscula. Caso contrário, você receberá erros de manifesto do aplicativo ao registrar **BroadcastReceiver** e **IntentFilter** para notificações por push abaixo.</p>
    </div> 

### Adicionar PushSharp ao seu projeto

1. Agora será preciso adicionar **PushSharp** como uma referência em nosso projeto. Para fazer isso, devemos compilar a versão mais recente do PushSharp e adicionar o DLL compilado como uma referência ao nosso projeto do Xamarin.Android.

2. Acesse a [página do PushSharp Github]e baixe a versão mais recente. Depois de extrair o conjunto de arquivos, vá até a seguinte pasta de amostra de projeto:

	**/Client.Samples/PushSharp.ClientSample.MonoForAndroid/PushSharp.ClientSample.MonoForAndroid.Gcm/**

	.. e abra o seguinte arquivo de projeto no Xamarin Studio (ou Visual Studio):
	
	**  PushSharp.ClientSample.MonoForAndroid.Gcm.csproj **

3. Crie a amostra de cliente do MonoForAndroid PushSharp no modo **Versão**.

4. Crie uma pasta **_externa** na sua pasta de projetos do Xamarin.Android

5. Copie o seguinte arquivo da amostra de cliente do MonoForAndroid PushSharp para a pasta **_externa** recém-criado em sua pasta de projetos do Xamarin.Android:

	 **\bin\Release\PushSharp.Client.MonoForAndroid.dll**

6. Abra seu projeto do Xamarin.Android no Xamarin Studio (ou Visual Studio). 

7. Clique com o botão direito na pasta **Referências** do projeto e selecione **Editar referências...**

8. Acesse a guia **.Net Assembly**, procure a pasta **_externa** do projeto, selecione o **PushSharp.Client.MonoForAndroid.dll** que criamos anteriormente e clique em **Adicionar**. Clique em OK para fechar a caixa de diálogo. 

### Adicionar Xamarin.NotificationHub ao seu projeto

1. Agora precisamos adicionar bibliotecas para oferecer suporte à classe NotificationHub. Visite a [página Xamarin.NotificationHub Github], faça o download e crie a pasta de origem.

2. Copiar o **ByteSmith.WindowsAzure.Messaging.Android.dll** compilado em sua pasta **_externa** na sua pasta de projeto Xamarin.Android.

3. Abra seu projeto do Xamarin.Android no Xamarin Studio (ou Visual Studio).

4. Clique com o botão direito na pasta **Referências** do projeto e escolha **Editar referências...**

5. Acesse a guia **.Net Assembly**, procure a pasta **_externa** do projeto, selecione o **ByteSmith.WindowsAzure.Messaging.Android.dll** que criamos anteriormente e clique em **Adicionar**. Clique em OK para fechar a caixa de diálogo. 

### Instalar Hubs de notificação em seu projeto

1. Criar uma classe **Constants.cs** e definir os seguintes valores de constantes (substituindo os espaços reservados por valores):

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>"";
        public const string NotificationHubPath = "<hub path>";

2. Adicionar as seguintes instruções à **MainActivity.cs**:

		using ByteSmith.WindowsAzure.Messaging;
		using PushSharp.Client;

3. Criar o seguinte método na classe **MainActivity**:

		private void RegisterWithGCM()
        {
            // Check to ensure everything's setup right
            PushClient.CheckDevice(this);
            PushClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            PushClient.Register(this, Constants.SenderID);
        }

4. Criar uma nova classe **MyBroadcastReceiver**.

	<div class="dev-callout"><b>Observação</b>
    <p>Vamos abordar a criação de um **BroadcastReceiver** a partir do zero. No entanto, uma alternativa rápida para criar manualmente o **MyBroadcastReceiver.cs** abaixo é referir-se ao arquivo **PushService.cs** encontrado na amostra de PushSharp no projeto de Xamarin.Android no GitHub. Duplicar o **PushService.cs** e alterar os nomes de classe podem ser uma ótima maneira de começar.</p>
    </div> 

5. Adicionar as seguintes instruções à **MyBroadcastReceiver.cs**:

		using ByteSmith.WindowsAzure.Messaging;
		using PushSharp.Client;

5. Adicionar as solicitações de permissão a seguir entre as instruções **using** e a declaração **namespace**:

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. Em **MyBroadcastReceiver.cs**, alterar a classe **MyBroadcastReceiver** para coincidir com o seguinte:

    	[BroadcastReceiver(Permission=GCMConstants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { GCMConstants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { GCMConstants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { GCMConstants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : PushHandlerBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }
        
7. Adicionar outra classe em **MyBroadcastReceiver.cs** chamada **PushHandlerService** que deriva de **PushHandlerServiceBase**. Certifique-se de usar a diretiva **Serviço** na classe:

    	[Service] //Must use the service tag
    	public class PushHandlerService : PushHandlerServiceBase
    	{
        	public static string RegistrationID { get; private set; }
        	private NotificationHub Hub { get; set; }

        	public PushHandlerService() : base(Constants.SenderID) 
       		{
            	Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor"); 
        	}
    	}


8. **PushHandlerServiceBase** implementa métodos **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** e **OnError()**. Nossa classe de implementação **PushHandlerService** deve substituir esses métodos e esses métodos serão acionados em resposta à interação com o hub de notificação.

9. Substituir o método **OnRegistered()** em **PushHandlerService** pelo seguinte código:

        protected override async void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("PushHandlerService-GCM Registered...", "The device has been Registered, Tap to View!");

            Hub = new NotificationHub(Constants.NotificationHubPath, Constants.ConnectionString);
            try
            {
                await Hub.UnregisterAllAsync(registrationId);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }

            var tags = new List<string>() { "falcons" }; // create tags if you want

            try
            {
                var hubRegistration = await Hub.RegisterNativeAsync(registrationId, tags);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message); 
                Debugger.Break();
            }
        }

	<div class="dev-callout"><b>Observação</b>
    <p>No código **OnRegistered()** acima que você deve observar a capacidade de especificar marcas para registrar para canais de mensagens específicos.</p>
    </div> 
    
10. Substituir o método **OnMessage** em **PushHandlerService** pelo seguinte código:

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


<h2><a name="run-app"></a><span class="short-header">Executar o aplicativo</span>Executar o aplicativo no emulador</h2>

Quando você executa o aplicativo no emulador, certifique-se de usar um Android Virtual Device (AVD) que oferece suporte a APIs do Google.

1. Em **Ferramentas**, clique em **Abrir Gerenciador de Emulador para Android**, selecione seu dispositivo e clique em **Editar**.

   	![][18]

2. Selecione **APIs do Google** em **Destino** e clique em **OK**.

   	![][19]

3. Na barra de ferramentas superior, clique em **Executar** e selecione seu aplicativo. Isso iniciará o emulador e executará o aplicativo.

4. O aplicativo recuperará o *registrationId* do GCM e registrará no Hub de Notificação.

	<div class="dev-callout"><b>Observação</b>
    <p>Para receber as notificações por push, você deve configurar uma conta do Google em seu Dispositivo Virtual para Android (no emulador, navegue até **Configurações** e clique em **Adicionar Conta**). Além disso, certifique-se de que o emulador esteja conectado à Internet.</p>
    </div> 

<h2><a name="send"></a><span class="short-header">Enviar notificação</span>Enviar notificação a partir de seu back-end</h2>

Você pode enviar notificações usando Hubs de Notificação de qualquer back-end usando nossa <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dn223264.aspx">interface REST</a> Neste tutorial, você enviará notificações com um aplicativo de console .NET e com um Serviço Móvel usando um script de nó.

Para enviar notificações usando um aplicativo .NET:

1. Crie um novo aplicativo de console com Visual C#: 

   	![][20]

2. Adicione uma referência ao SDK do Service Bus do Azure com o <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacote NuGet do WindowsAzure.ServiceBus</a>. No menu principal do Visual Studio, clique em **Ferramentas**, em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**. Depois, na janela do console, digite:

        Install-Package WindowsAzure.ServiceBus

    e pressione Enter.

2. Abra o arquivo Program.cs e adicione a seguinte instrução:

        using Microsoft.ServiceBus.Notifications;

3. Em sua classe `Programa`, adicione o método seguinte:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
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

5. Insira o script a seguir em sua função do agendador. Certifique-se de substituir os espaços reservados pelo nome de seu hub de notificação e pela cadeia de conexão para a *DefaultFullSharedAccessSignature* que você obteve anteriormente. Clique em **Salvar**.

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

6. Clique em **Executar uma Vez** na barra inferior. Você deve receber uma notificação do sistema.

## <a name="next-steps"> </a>Próximas etapas

Neste simples exemplo, você envia notificações para todos os seus dispositivos Android. Para selecionar usuários de destinos específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push a usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá consultar [Usar Hubs de Notificação para enviar notícias de última hora]. Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação] e em [Instruções sobre Hubs de Notificação para Android].

<!-- Anchors. -->
[Habilitar a mensagem em nuvem do Google]: #register
[Configurar seu Hub de Notificação]: #configure-hub
[Conectando seu aplicativo ao Hub de Notificação]: #connecting-app
[Executar o aplicativo com o emulador]: #run-app
[Enviar notificações a partir de seu back-end]: #send
[Próximas etapas]:#next-steps

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
[Enviar uma página de aplicativo:]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-push-js

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[objeto wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/pt-br/library/jj927170.aspx
[Instruções sobre Hubs de Notificação para Android]: http://msdn.microsoft.com/pt-br/library/dn282661.aspx

[Usar Hubs de Notificação para enviar notificações por push a usuários]: /pt-br/manage/services/notification-hubs/notify-users-aspnet
[Usar Hubs de Notificação para enviar notícias de última hora]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet
[Página do PushSharp Github]: https://github.com/Redth/PushSharp
[página Xamarin.NotificationHub Github]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329

