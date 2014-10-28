<properties linkid="develop-notificationhubs-tutorials-get-started-android" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Introdução aos Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/pt-br/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/pt-br/documentation/articles/notification-hubs-android-get-started/" title="Android" class="current">Android</a><a href="/pt-br/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Este tópico mostra como usar os Hubs de Notificação do Windows Azure para enviar notificações por push para um aplicativo Android.
Neste tutorial, você cria um aplicativo em branco para Android que recebe notificações por push usando o Google Cloud Messaging (GCM). Ao concluir, você poderá transmitir notificações por push a todos os dispositivos que executam seu aplicativo usando o hub de notificação.

O tutorial explica as seguintes etapas básicas para habilitar as notificações por push:

-   [Habilitar o sistema de mensagens em nuvem do Google][Habilitar o sistema de mensagens em nuvem do Google]
-   [Configurar seu Hub de Notificação][Configurar seu Hub de Notificação]
-   [Conectando seu aplicativo ao Hub de Notificação][Conectando seu aplicativo ao Hub de Notificação]
-   [Como enviar notificações para seu aplicativo][Como enviar notificações para seu aplicativo]
-   [Testando seu aplicativo][Testando seu aplicativo]

Este tutorial demonstra o simples cenário de transmissão usando Hubs de Notificação. Certifique-se de seguir o próximo tutorial para aprender a usar hubs de notificação, para atender usuários e grupos de dispositivos específicos.

Este tutorial exige o seguinte:

-   o SDK do Android (pressupõe-se que você esteja usando o Eclipse), que pode ser baixado [aqui][aqui]
-   o [SDK de Serviços Móveis do Android][SDK de Serviços Móveis do Android]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre hubs de notificação para aplicativos do Android.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa ter uma conta ativa do Azure. Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

## <span id="register"></span></a>Habilitar o sistema de mensagens em nuvem do Google

[WACOM.INCLUDE [Habilitar GCM][Habilitar GCM]]

Em seguida, você usará este valor de chave da API para ativar seu hub de notificação, fazer a autenticação com o GCM e enviar notificações por push em nome de seu aplicativo.

## <span id="configure-hub"></span></a>Configurar seu Hub de Notificação

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], e clique em **+NOVO** na parte inferior da tela.

2.  Clique em **Serviços de Aplicativo**, em **Service Bus**, em **Hub de Notificação** e, em seguida, **Criação Rápida**.

    ![][]

3.  Digite um nome para o hub de notificação, selecione a região desejada e clique em **Criar um novo Hub de Notificação**.

    ![][1]

4.  Clique no namespace que você acabou de criar (geralmente o ***nome do hub de notificação*-ns**), e clique na guia **Configurar** na parte superior.

    ![][2]

5.  Clique na guia **Hubs de Notificação** na parte superior e clique no hub de notificação que você acabou de criar.

    ![][3]

6.  Clique na guia **Configurar** na parte superior, digite o valor de **Chave da API** que você obteve na etapa anterior e clique em **Salvar**.

    ![][4]

7.  Selecione a guia **Painel** na parte superior e, em seguida, clique em **Exibir Cadeia de Conexão**. Anote as duas cadeias de conexão.

Seu hub de notificação agora está configurado para funcionar com o GCM e você tem as cadeias de conexão para registrar seu aplicativo e enviar notificações por push.

## <span id="connecting-app"></span></a>Conectando seu aplicativo ao Hub de Notificação

### Criar novo projeto Android

1.  No Eclipse ADT, crie um novo projeto Android (Arquivo, Novo, Aplicativo Android).

    ![][5]

2.  Certifique-se de que o **SDK Mínimo Necessário** esteja definido como *API 8: Android 2.2 (Froyo)* e de que as duas próximas entradas SDK estejam definidas para a versão mais recente. Escolha Avançar e siga o assistente, certificando-se de que a opção **Criar atividade** esteja selecionada para criar uma atividade em branco. Aceite o ícone padrão do Iniciador na caixa seguinte e clique em **Concluir** na última caixa.

    ![][6]

### Inclua o Google Play Services no projeto

[WACOM.INCLUDE [Incluir Serviços de Reprodução][Incluir Serviços de Reprodução]]

### Incluir código

1.  Baixe os Hubs de Notificação do SDK do Android [aqui][SDK de Serviços Móveis do Android]. Extraia o arquivo .zip e copie o arquivo notificationhubs\\notification-hubs-0.1.jar no diretório \\libs do seu projeto no Gerenciador de Pacotes.

2.  Baixe e descompacte o [Mobile Services Android SDK][SDK de Serviços Móveis do Android], abra a pasta **notificações**, copie o arquivo **notifications-1.0.1.jar** para a pasta *libs* do seu projeto Eclipse e atualize a pasta *libs*.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Os n&uacute;meros no fim do nome do arquivo podem ser alterados em vers&otilde;es subsequentes do SDK.</p>
</div>

    Agora, configure o aplicativo para obter uma *registrationId* a partir do GCM e use-o para registrar a instância do aplicativo no hub de notificação.

3.  No arquivo AndroidManifest.xml, adicione a seguinte linha logo abaixo do elemento <uses-sdk></uses-sdk>. Certifique-se de substituir o `<your package>` pelo pacote selecionado para seu aplicativo na etapa 1 (neste exemplo, `com.yourCompany.wams_notificationhubs`).

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

        <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
        <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4.  Na classe **MainActivity**, adicione as seguintes instruções.

        import android.os.AsyncTask;    
        import com.google.android.gms.gcm.*;
        import com.microsoft.windowsazure.messaging.*;
        import com.microsoft.windowsazure.notifications.NotificationsManager;

5.  Adicione os seguintes membros privados na parte superior da classe.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Certifique-se de definir o SENDER_ID ao N&uacute;mero do Projeto obtido anteriormente.</p>
</div>

        private String SENDER_ID = "<your project number>";
        private GoogleCloudMessaging gcm;
        private NotificationHub hub;

6.  No método **OnCreate**, adicione o seguinte código e certifique-se de substituir os espaços reservados pela sua cadeia de conexão com acesso de escuta obtida na etapa anterior e o nome do seu hub de notificação que aparece na parte superior da página do Windows Azure pelo seu hub (**não** a url completa).

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

        gcm = GoogleCloudMessaging.getInstance(this);

        String connectionString = "<your listen access connection string>";
        hub = new NotificationHub("<your notification hub name>", connectionString, this);

        registerWithNotificationHubs();

7.  Em MainActivity.java, crie o seguinte método:

        @SuppressWarnings("unchecked")
        private void registerWithNotificationHubs() {
           new AsyncTask() {
              @Override
              protected Object doInBackground(Object... params) {
                 try {
                    String regid = gcm.register(SENDER_ID);
                    hub.register(regid);
                 } catch (Exception e) {
                    return e;
                 }
                 return null;
             }
           }.execute(null, null, null);
        }

8.  Você deve incluir seu próprio destinatário, pois o Android não exibe notificações. No arquivo **AndroidManifest.xml**, adicione o seguinte elemento dentro do elemento `<application/>`.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Substitua o espa&ccedil;o reservado pelo nome do pacote.</p>
</div>

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

9.  No Gerenciador de Pacotes, clique com o botão direito no pacote (no nó `src`), clique em **Novo** e em **Classe**.

10. Em **Nome**, digite `MyHandler`, no tipo **Superclasse**, digite \` `com.microsoft.windowsazure.notifications.NotificationsHandler` e clique em **Concluir**.

    ![][7]

    Isto criará a nova classe MyHandler.

11. Adicione as seguintes declarações de importação:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;

12. Adicione o seguinte código à classe:

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;


        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("msg");

            sendNotification(nhMessage);
        }

        private void sendNotification(String msg) {
            mNotificationManager = (NotificationManager)
                      ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                  new Intent(ctx, MainActivity.class), 0);

            NotificationCompat.Builder mBuilder =
                  new NotificationCompat.Builder(ctx)
                  .setSmallIcon(R.drawable.ic_launcher)
                  .setContentTitle("Notification Hub Demo")
                  .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(msg))
                  .setContentText(msg);

             mBuilder.setContentIntent(contentIntent);
             mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }

## <a name="send"></a>Como enviar uma notificação para seu aplicativo

Você pode enviar notificações usando Hubs de Notificação de qualquer back-end usando a [interface REST][interface REST] Neste tutorial você envia as notificações com um aplicativo de console do .NET. Para um exemplo de como enviar notificações de um back-end do Serviços Móveis do Azure integrado com Hubs de notificação, consulte **Introdução às notificações push no Mobile\_Services** ([back-end do .NET][back-end do .NET] | [Back-end do JaveScript][back-end do .NET]). Para um exemplo de como enviar notificações usando o REST APIs, consulte **Como usar Hubs de Notificação a partir de Java/PHP** ([Java][Java] | [PHP][PHP]).

1.  No Visual Studio, no menu **Arquivo** selecionar **Novo** e, em seguida, **Projeto...**, em seguida, em **Visual C#** clique no **Windows** e **Aplicativo de console** e clique em **OK**.

    ![][8]

    Isso cria um novo projeto de aplicativo de console.

2.  No menu **Ferramentas**, clique em **Gerenciador de Pacotes de Biblioteca** e, em seguida, selecione **Console do Gerenciador de Pacotes**.

    Isso exibe a Console do Gerenciador de Pacotes.

3.  Na janela da console, execute o seguinte comando:

        Install-Package WindowsAzure.ServiceBus

    Isto adiciona uma referência ao SDK do Service Bus do Azure com o [pacote NuGet do WindowsAzure.ServiceBus][pacote NuGet do WindowsAzure.ServiceBus].

4.  Abra o arquivo Program.cs e adicione a seguinte instrução `using`:

        using Microsoft.ServiceBus.Notifications;

5.  Na classe **Programa**, adicione o seguinte método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

    Substitua o espaço reservado de "nome do hub" pelo nome do hub de notificação que aparece no portal, na guia **Hubs de Notificação**. Além disso, substituir o espaço reservado de cadeias de conexão com a cadeia de conexão chamada **DefaultFullSharedAccessSignature** que você obteve na seção "Configurar seu Hub de Notificação."

    > [WACOM.NOTE]Observe que essa é a cadeia de conexão com acesso **Completo**, não com acesso **Escutar**. A cadeia de acesso Escutar não tem permissões para enviar notificações.

6.  Em seguida, adicione as seguintes linhas no método **Main**:

         SendNotificationAsync();
         Console.ReadLine();

## <a name="run-app"></a>Testando seu aplicativo

Antes de testar o aplicativo em um emulador, você precisa completar estas etapas de configuração do emulador (pular se o teste for em um dispositivo físico):

1.  Certifique-se de que você esteja usando um Dispositivo Virtual para Android (AVD) que ofereça suporte às APIs do Google.

2.  A partir de **Janela**, clique em **Gerenciador de Dispositivo Virtual para Android**, selecione seu dispositivo e, em seguida, clique em **Editar**.

    ![][9]

3.  Selecione **Google APIs** em **Destino** e clique em **OK**.

    ![][10]

4.  Para receber as notificações por push, você deve configurar uma conta do Google em seu Dispositivo Virtual para Android (no emulador, navegue até **Configurações** e clique em **Adicionar Conta**). Além disso, certifique-se de que o emulador esteja conectado à Internet.

Use as seguintes etapas para executar o aplicativo em um dispositivo ou em um emulador:

1.  Na barra de ferramentas superior do Eclipse, clique em **Executar**e, em seguida, selecione seu aplicativo.

    Isso inicia o aplicativo (se estiver usando um emulador) e carrega e executa o aplicativo. O aplicativo recupera o *registrationId* do GCM e registrará no Hub de Notificação.

2.  Pressionar a tecla F5 no Visual Studio para executar o aplicativo de console.

    Uma notificação é enviada a seu aplicativo.

3.  Quando um ícone aparecer na área de notificação (esquina superior esquerda), puxe para baixo a gaveta de notificações para visualizar a notificação.

    ![][11]

## <a name="next-steps"> </a>Próximas etapas

Neste simples exemplo, você envia notificações para todos os seus dispositivos Android. Para selecionar usuários de destinos específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários][Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá consultar [Usar Hubs de Notificação para enviar notícias de última hora][Usar Hubs de Notificação para enviar notícias de última hora]. Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação][Diretrizes dos Hubs de Notificação].

<!-- Images. --> <!-- URLs. -->

  [Windows Universal]: /pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows Universal"
  [Windows Phone]: /pt-br/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /pt-br/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /pt-br/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /pt-br/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [Habilitar o sistema de mensagens em nuvem do Google]: #register
  [Configurar seu Hub de Notificação]: #configure-hub
  [Conectando seu aplicativo ao Hub de Notificação]: #connecting-app
  [Como enviar notificações para seu aplicativo]: #send
  [Testando seu aplicativo]: #run-app
  [aqui]: http://go.microsoft.com/fwlink/?LinkId=389797
  [SDK de Serviços Móveis do Android]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [Habilitar GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  []: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal.png
  [1]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal2.png
  [2]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal.png
  [3]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal2.png
  [4]: ./media/notification-hubs-android-get-started/notification-hub-configure-android.png
  [5]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app.png
  [6]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app2.png
  [Incluir Serviços de Reprodução]: ../includes/mobile-services-add-Google-play-services.md
  [7]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
  [interface REST]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn223264.aspx
  [back-end do .NET]: /pt-br/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Java]: /pt-br/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /pt-br/documentation/articles/notification-hubs-php-backend-how-to/
  [8]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
  [pacote NuGet do WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [9]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app7.png
  [10]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app8.png
  [11]: ./media/notification-hubs-android-get-started/notification-hub-android-toast.png
  [Usar Hubs de Notificação para enviar notificações por push aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-aspnet
  [Usar Hubs de Notificação para enviar notícias de última hora]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet
  [Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/pt-br/library/jj927170.aspx
