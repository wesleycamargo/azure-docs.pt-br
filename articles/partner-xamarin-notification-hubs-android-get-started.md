<properties linkid="manage-services-notification-hubs-getting-started-xamarin-android" urlDisplayName="" pageTitle="Get started with Notification Hubs for Xamarin.Android apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin Android application." metaCanonical="" authors="donnam" solutions="" manager="dwrede" editor="" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Introdução aos Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/pt-br/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/pt-br/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/pt-br/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

Este tópico mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo Xamarin Android.
Neste tutorial, você cria um aplicativo em branco para Xamarin Android que recebe notificações por push usando o Google Cloud Messaging (GCM). Ao concluir, você poderá transmitir notificações por push a todos os dispositivos que executam seu aplicativo usando o hub de notificação. O código concluído está disponível na amostra do [aplicativo NotificationHubs][aplicativo NotificationHubs].

O tutorial explica as seguintes etapas básicas para habilitar as notificações por push:

1.  [Habilitar o sistema de mensagens em nuvem do Google][Habilitar o sistema de mensagens em nuvem do Google]
2.  [Configurar seu Hub de Notificação][Configurar seu Hub de Notificação]
3.  [Conectando seu aplicativo ao Hub de Notificação][Conectando seu aplicativo ao Hub de Notificação]
4.  [Executar o aplicativo com o emulador][Executar o aplicativo com o emulador]
5.  [Enviar notificações de seu back-end][Enviar notificações de seu back-end]

Este tutorial demonstra o simples cenário de transmissão usando Hubs de Notificação. Este tutorial exige o seguinte:

-   [Xamarin.Android][1]
-   Conta ativa do Google
-   [Componente dos Serviços Móveis do Azure][Componente dos Serviços Móveis do Azure]
-   [Componente de mensagens de nuvem do Google][Componente de mensagens de nuvem do Google]

Completar este tutorial é um pré-requisito para todos os outros tutoriais de hub de notificação para aplicativos Xamarin.Android.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa ter uma conta ativa do Azure. Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

## <a name="register"></a><span class="short-header">Ativar Google Cloud Messaging</span>Ativar Google Cloud Messaging

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Para concluir o procedimento neste t&oacute;pico, voc&ecirc; deve ter uma conta do Google com um endere&ccedil;o de email verificado. Para criar uma nova conta do Google, v&aacute; para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.</p>
</div>

1.  Navegue até o site [Apis do Google][Apis do Google], entre com as credenciais de sua conta do Google e clique em **Criar projeto...**.

    ![][]

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Quando voc&ecirc; j&aacute; tiver um projeto existente, voc&ecirc; ser&aacute; direcionado para a p&aacute;gina do <strong>Painel</strong> ap&oacute;s o login. Para criar um novo projeto no Painel, expanda <strong>Projeto da API</strong>, clique em <strong>Criar...</strong>, sob <strong>Outros projetos</strong>, insira um nome de projeto e clique em <strong>Criar projeto</strong>.</p>
</div>

2.  Clique em **Visão Geral** na coluna esquerda e anote o número do projeto na seção **Painel**.

    No tutorial, posteriormente, você definirá esse valor como a variável PROJECT\_ID no cliente.

3.  Na página [Apis do Google][Apis do Google], clique em **Serviços** e depois alterne para ativar o **Google Cloud Messaging para Android** e aceite os termos de serviço.

4.  Clique em **Acesso às APIs**e em **Criar nova chave de Servidor...**

    ![][2]

5.  Em **Configurar Chave do Servidor para a API do projeto**, Clique em **Criar**.

    ![][3]

6.  Anote o valor da **Chave da API**.

    ![][4]

Em seguida, você usará este valor de chave da API para ativar seu hub de notificação, fazer a autenticação com o GCM e enviar notificações por push em nome de seu aplicativo.

## <a name="configure-hub"></a><span class="short-header">Configurar seu Hub de Notificação</span>Configurar seu Hub de Notificação

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], e clique em **+NOVO** na parte inferior da tela.

2.  Clique em **Serviços de Aplicativo**, em **Service Bus**, em **Hub de Notificação** e, em seguida, **Criação Rápida**.

    ![][5]

3.  Digite um nome para o hub de notificação, selecione a região desejada e clique em **Criar um novo Hub de Notificação**.

    ![][6]

4.  Clique no namespace que você acabou de criar (geralmente o ***nome do hub de notificação*-ns**), e clique na guia **Configurar** na parte superior.

    ![][7]

5.  Clique na guia **Hubs de Notificação** na parte superior e clique no hub de notificação que você acabou de criar.

    ![][8]

6.  Clique na guia **Configurar** na parte superior, digite o valor de **Chave da API** que você obteve na etapa anterior e clique em **Salvar**.

    ![][9]

7.  Clique na guia **Painel** na parte superior e clique em **Informações de Conexão**. Anote as duas cadeias de conexão.

    ![][10]

Seu hub de notificação agora está configurado para funcionar com o GCM e você tem as cadeias de conexão para registrar seu aplicativo e enviar notificações por push.

## <a name="connecting-app"></a><span class="short-header">Conectando seu aplicativo</span>Conectando seu aplicativo ao Hub de Notificação

### Criar um novo projeto

1.  No Xamarin Studio (ou Visual Studio), crie um novo projeto Android (Arquivo, Novo, Solução, Aplicativo Android).

    ![][11]
    ![][12]

2.  Abra as propriedades do projeto clicando com o botão direito em seu novo projeto no modo de exibição Solução e escolha **Opções**. Selecione o item **Aplicativo Android** na seção **Compilação**.

    ![][13]

3.  Definir a **versão mínima do Android** no nível API 8.

4.  Definir o **versão destino Android** para a versão de API você gostaria de ter como destino (deve ser API nível 8 ou superior).

5.  Certifique-se de que a primeira letra de seu **nome do pacote** esteja em minúscula.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>A primeira letra do nome do pacote deve estar em min&uacute;scula. Caso contr&aacute;rio, voc&ecirc; receber&aacute; erros de manifesto do aplicativo ao registrar **BroadcastReveiver** e **IntentFilter** para notifica&ccedil;&otilde;es por push abaixo.</p>
</div>

### Adicione o cliente de mensagens de nuvem do Google a seu projeto

O cliente de mensagens de nuvem do Google disponível na loja de componentes Xamarin simplifica o processo de oferecer suporte de notificações por push no Xamarin.Android.

1.  Clique com o botão direito do mouse na pasta Componentes no aplicativo Xamarin.Android e escolha **Obter mais componentes...**

2.  Procurar o componente **Cliente de mensagens de nuvem do Google**.

3.  Adicionar o componente ao aplicativo Xamarin.Android. As referências do assembly necessárias são adicionadas automaticamente.

### Adicionar Xamarin.NotificationHub ao seu projeto

Este assembly fornece uma maneira fácil para registrar-se ao os hubs de Notificação do Azure. Pode se baixar usando as instruções abaixo ou pode ser encontrada no [download de exemplos][aplicativo NotificationHubs].

1.  Visite a [página Xamarin.NotificationHub Github][página Xamarin.NotificationHub Github], faça o download e crie a pasta de origem.

2.  Crie uma pasta \*\*\_external\*\* na pasta do seu projeto Xamarin.Android, em seguida, copie o **ByteSmith.WindowsAzure.Messaging.Android.dll** compilado.

3.  Abra seu projeto do Xamarin.Android no Xamarin Studio (ou Visual Studio).

4.  Clique com o botão direito na pasta **Referências** do projeto e escolha **Editar referências...**

5.  Acesse a guia **.Net Assembly**, procure a pasta \*\*\_externa\*\* do projeto, selecione o **ByteSmith.WindowsAzure.Messaging.Android.dll** que criamos anteriormente e clique em **Adicionar**. Clique em OK para fechar a caixa de diálogo.

### Instalar Hubs de notificação em seu projeto

1.  Criar uma classe **Constants.cs** e definir os seguintes valores de constantes (substituindo os espaços reservados por valores):

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<hub path>";

2.  Adicionar as seguintes instruções à **MainActivity.cs**:

        using ByteSmith.WindowsAzure.Messaging;
        using Gcm.Client;

3.  Criar o seguinte método na classe **MainActivity**:

        private void RegisterWithGCM()
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4.  Criar uma nova classe **MyBroadcastReceiver**.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Vamos abordar a cria&ccedil;&atilde;o de um **BroadcastReceiver** a partir do zero. No entanto, uma alternativa r&aacute;pida para criar manualmente o **MyBroadcastReceiver.cs** abaixo &eacute; referir-se ao arquivo **GcmService.cs** encontrado no projeto de exemplo Xamarin.Android no GitHub. Duplicar o **GcmService.cs** e alterar os nomes de classe podem ser uma &oacute;tima maneira de come&ccedil;ar.</p>
</div>

5.  Adicionar as seguintes instruções using para **MyBroadcastReceiver.cs** (para se referir ao componente e assembly adicionados anteriormente):

        using ByteSmith.WindowsAzure.Messaging;
        using Gcm.Client;

6.  Adicionar as solicitações de permissão a seguir entre as instruções **using** e a declaração **namespace**:

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7.  Em **MyBroadcastReceiver.cs** alterar a classe **MyBroadcastReceiver** para corresponder com o seguinte:

        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

8.  Adicionar outra classe em **MyBroadcastReceiver.cs** chamada **PushHandlerService** que deriva de **PushHandlerServiceBase**. Certifique-se de usar a diretiva **Serviço** na classe:

        [Service] //Must use the service tag
        public class GcmService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
            private NotificationHub Hub { get; set; }

            public GcmService() : base(Constants.SenderID) 
            {
                Log.Info(MyBroadcastReceiver.TAG, "GcmService() constructor"); 
            }
        }

9.  **GcmServiceBase** implementa métodos **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** e **OnError()**. Nossa classe de implementação **GcmService** deve substituir esses métodos e esses métodos serão acionados em resposta à interação com o hub de notificação.

10. Substituir o método **OnRegistered()** em **PushHandlerService** pelo seguinte código:

        protected override async void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("GcmService-GCM Registered...", "The device has been Registered, Tap to View!");

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

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>No c&oacute;digo **OnRegistered()** acima que voc&ecirc; deve observar a capacidade de especificar marcas para registrar para canais de mensagens espec&iacute;ficos.</p>
</div>

11. Substituir o método **OnRegistered** em **PushHandlerService** pelo seguinte código:

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

12. Adicionar o seguinte método **createNotification** a **PushHandlerService** para notificar os usuários conforme usado acima:

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

13. Substituir membros abstratos **OnUnRegistered()**, **OnRecoverableError()** e **OnError()** para que seu código seja compilado.

## <a name="run-app"></a><span class="short-header">Executar o aplicativo</span>Executar o aplicativo no emulador

Quando você executa o aplicativo no emulador, certifique-se de usar um Android Virtual Device (AVD) que oferece suporte a APIs do Google.

1.  A partir de **Ferramentas**, clique em **Abrir gerenciador de emulador do Android**, selecione seu dispositivo e, em seguida, clique em **Editar**.

    ![][14]

2.  Selecione **Google APIs** em **Destino** e clique em **OK**.

    ![][15]

3.  Na barra de ferramentas superior, clique em **Executar**e, em seguida, selecione seu aplicativo. Isso iniciará o emulador e executará o aplicativo.

4.  O aplicativo recupera o *registrationId* do GCM e registrará no Hub de Notificação.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Para receber as notifica&ccedil;&otilde;es por push, voc&ecirc; deve configurar uma conta do Google em seu Dispositivo Virtual para Android (no emulador, navegue at&eacute; **Configura&ccedil;&otilde;es** e clique em **Adicionar Conta**). Al&eacute;m disso, certifique-se de que o emulador esteja conectado &agrave; Internet.</p>
</div>

## <a name="send"></a><span class="short-header">Enviar notificação</span>Enviar notificação de seu back-end

Você pode enviar notificações usando Hubs de Notificação de qualquer back-end usando a [interface REST][interface REST] Neste tutorial, você enviará notificações com um aplicativo de console .NET e com um Serviço Móvel usando um script de nó.

Para enviar notificações usando um aplicativo .NET:

1.  Crie um novo aplicativo de console do Visual C#:

    ![][16]

2.  Adicione uma referência ao SDK do Service Bus do Azure com o [pacote NuGet do WindowsAzure.ServiceBus][pacote NuGet do WindowsAzure.ServiceBus]. No menu principal do Visual Studio, clique em **Ferramentas**, em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**. Na janela do console, digite:

        Install-Package WindowsAzure.ServiceBus

    e pressione Enter.

3.  Abra o arquivo Program.cs e adicione a seguinte instrução:

        using Microsoft.ServiceBus.Notifications;

4.  Em sua classe `Program`, adicione o método seguinte:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

5.  Em seguida, adicione as seguintes linhas em seu método Main:

         SendNotificationAsync();
         Console.ReadLine();

6.  Pressione a tecla F5 para executar o aplicativo. Você deve receber uma notificação do sistema.

    ![][17]

Para enviar uma notificação usando um Serviço Móvel, consulte a [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis] e faça o seguinte:

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure] e selecione seu Serviço Móvel.

2.  Selecione a guia **Agendador** na parte superior.

    ![][18]

3.  Crie um novo trabalho agendado, insira um nome e selecione **Sob demanda**.

    ![][19]

4.  Quando o trabalho for criado, clique no nome do trabalho. Em seguida, clique na guia **Script** na barra superior.

5.  Insira o script a seguir em sua função de Agendador. Certifique-se de substituir os espaços reservados pelo nome de seu hub de notificação e pela cadeia de conexão para a *DefaultFullSharedAccessSignature* que você obteve anteriormente. Clique em **Salvar**.

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

6.  Clique em **Executar uma vez** na barra inferior. Você deve receber uma notificação do sistema.

## <a name="next-steps"> </a>Próximas etapas

Neste simples exemplo, você envia notificações para todos os seus dispositivos Android. Para selecionar usuários de destinos específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários][Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá consultar [Usar Hubs de Notificação para enviar notícias de última hora][Usar Hubs de Notificação para enviar notícias de última hora]. Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação][Diretrizes dos Hubs de Notificação] e em [Instruções sobre Hubs de Notificação para Android][Instruções sobre Hubs de Notificação para Android].

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Universal]: /pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows Universal"
  [Windows Phone]: /pt-br/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /pt-br/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /pt-br/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /pt-br/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [aplicativo NotificationHubs]: http://go.microsoft.com/fwlink/p/?LinkId=331329
  [Habilitar o sistema de mensagens em nuvem do Google]: #register
  [Configurar seu Hub de Notificação]: #configure-hub
  [Conectando seu aplicativo ao Hub de Notificação]: #connecting-app
  [Executar o aplicativo com o emulador]: #run-app
  [Enviar notificações de seu back-end]: #send
  [1]: http://xamarin.com/download/
  [Componente dos Serviços Móveis do Azure]: http://components.xamarin.com/view/azure-mobile-services/
  [Componente de mensagens de nuvem do Google]: http://components.xamarin.com/view/GCMClient/
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A9C9624B5&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F
  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [Apis do Google]: http://go.microsoft.com/fwlink/p/?LinkId=268303
  []: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-developers.png
  [2]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server.png
  [3]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server2.png
  [4]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server3.png
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [5]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal.png
  [6]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal2.png
  [7]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal.png
  [8]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal2.png
  [9]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
  [10]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-connection-strings.png
  [11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
  [12]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app2.png
  [13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
  [página Xamarin.NotificationHub Github]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
  [14]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
  [15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
  [interface REST]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn223264.aspx
  [16]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
  [pacote NuGet do WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [17]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
  [Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
  [18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
  [19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png
  [Usar Hubs de Notificação para enviar notificações por push aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-aspnet
  [Usar Hubs de Notificação para enviar notícias de última hora]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet
  [Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/pt-br/library/jj927170.aspx
  [Instruções sobre Hubs de Notificação para Android]: http://msdn.microsoft.com/pt-br/library/dn282661.aspx
