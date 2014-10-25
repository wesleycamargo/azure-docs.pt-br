<properties linkid="develop-notificationhubs-tutorials-get-started-nokia-x" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="kirillg" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-nokia-x" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="piyushjo"></tags>

# Introdução aos Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/pt-br/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/pt-br/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/pt-br/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/pt-br/documentation/articles/notification-hubs-nokia-x-get-started/" title="Nokia X" class="current">Nokia X</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Este tópico mostra como usar os **Hubs de Notificação do Azure** para enviar notificações por push para um aplicativo Android no **Nokia X**. Neste tutorial, você criará um aplicativo em branco para Android que recebe notificações por push usando o Serviço de Notificação da Nokia. Ao concluir, você poderá transmitir notificações por push a todos os dispositivos que executam seu aplicativo usando o hub de notificação.

O tutorial explica as seguintes etapas básicas para habilitar as notificações por push:

-   [Configurar o Serviço de Notificação da Nokia][Configurar o Serviço de Notificação da Nokia]
-   [Configurar seu Hub de Notificação][Configurar seu Hub de Notificação]
-   [Conectando seu aplicativo ao Hub de Notificação][Conectando seu aplicativo ao Hub de Notificação]
-   [Como enviar uma notificação para seu aplicativo][Como enviar uma notificação para seu aplicativo]
-   [Testando seu aplicativo][Testando seu aplicativo]

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa ter uma conta ativa do Azure. Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

Este tutorial exige o seguinte:

1.  Ambiente de desenvolvimento do Nokia X, o qual você pode configurar seguindo as instruções contidas [aqui][aqui]. Lembre-se de instalar os pacotes específicos do Nokia X e configurar o emulador do Nokia X ao seguir as instruções.
2.  Instalação do dispositivo Nokia X (opcional), a qual você pode configurar seguindo as instruções contidas [aqui][1].
3.  SDK do Android (presume-se que você usará o Eclipse), o qual você pode baixar [aqui][2].
4.  SDK para Android de Serviços Móveis, o qual você pode baixar [aqui][3]<a>.

## <span id="register"></span></a>Configurar o Serviço de Notificação da Nokia

1.  Entre no [Console do Desenvolvedor da API de Notificações da Nokia][Console do Desenvolvedor da API de Notificações da Nokia]

2.  Vá até a guia **Criar serviços** e crie um novo serviço fornecendo uma **ID do Remetente** e **Descrição do Serviço**

    ![][]

3.  Anote a **ID do Remetente** e a **Chave de Autorização** após o serviço ser criado com êxito.

4.  Você pode ir até a guia **Meus serviços** para listar todos os serviços criados com os respectivos **ID do Remetente** e **Chave de Autorização**

    ![][4]

5.  Para obter detalhes, consulte este [link][link].

## <span id="configure-hub"></span></a>Configurar seu Hub de Notificação

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure] e clique em **+NOVO** na parte inferior da tela.

2.  Clique em **Serviços de Aplicativos** e em **Barramento de Serviço**, **Hub de Notificação** e **Criação Rápida**.

    ![][5]

3.  Digite um nome para o hub de notificação, selecione a região desejada e, como opção, o namespace, em seguida, clique em **Criar um novo Hub de Notificação**.

    ![][6]

4.  Acesse o Hub de Notificação que você acabou de criar. Clique na guia Barramento de Serviço à esquerda e no namespace em que você criou o Hub de Notificação e, em seguida, clique na guia Hubs de Notificação.

    ![][7]

5.  Depois que estiver no Hub de Notificação que você criou, clique na guia **Configurar** na parte superior.

    ![][8]

6.  Role a tela para baixo para encontrar as **configurações de notificação do Nokia x**, cole a Chave de Autorização obtida no Serviço de Notificação da Nokia e clique no botão **Salvar** na parte inferior da página

    ![][9]

7.  Selecione a guia **Painel** na parte superior e clique em **Informações de Conexão** na parte inferior da página.

    ![][10]

8.  Anote as duas cadeias de conexão do SAS **DefaultListenSharedAccessSignature** e **DefaultFullSharedAccessSignature**, as quais você usará mais adiante no tutorial.

## <span id="connect-hub"></span></a>Conectando seu aplicativo ao Hub de Notificação

### Criar novo projeto Android

1.  No Eclipse ADT, crie um novo projeto Android (Arquivo -\> Novo -\> Aplicativo Android).

2.  Verifique se o SDK mínimo exigido está configurado como **API 16: Android 4.1 (Jelly Bean)** e que as duas próximas entradas SDK estejam definidas para a versão mais recente disponível. Escolha Avançar e siga o assistente, certificando-se de que a opção **Criar atividade** esteja selecionada para criar uma **atividade em branco**. Aceite o ícone padrão do Iniciador na caixa seguinte e clique em **Concluir** na última caixa.

    ![][11]

3.  Verifique se o destino de compilação do projeto está definido corretamente (Plataforma = 4.1.2 e Nível de API = 16 para este exemplo). Clique com o botão direito do mouse em Projeto, selecione Propriedades e Android na caixa de diálogo Propriedades do Projeto.

    ![][12]

4.  Adicione o arquivo JAR do Serviço de Notificação da Nokia ao seu projeto. Esse **push.jar** da biblioteca auxiliar de Notificações da Nokia oferece uma API semelhante à biblioteca auxiliar do GCM.
    Navegue até Propriedades do Projeto -\> Caminho de Compilação do Java -\> Bibliotecas -\> Adicionar JARs Externos e adicione o **push.jar** disponível em **\\extras\\nokia\\nokia\_x\_services\\libs\\nna\\push.jar**. O Javadoc para a biblioteca está localizado em **\\extras\\nokia\\nokia\_x\_services\\javadocs\\nna** onde o IDE do Eclipse está instalado.

5.  Não se esqueça também de copiar essa biblioteca do push.jar para o diretório \\libs de seu projeto no explorador do Pacote.

6.  Baixe os Hubs de Notificação do SDK do Android [aqui][3]. Extraia o arquivo .zip e copie o arquivo notificationhubs\\notification-hubs-0,3.jar no diretório \\libs do seu projeto no Gerenciador de Pacotes.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Os n&uacute;meros no fim do nome do arquivo podem ser alterados em vers&otilde;es subsequentes do SDK.</p>
</div>

### Incluir código

1.  Abra o arquivo **AndroidManifest.xml** e substitua o elemento do aplicativo pelas linhas de código a seguir, certificando-se que esteja substituindo o `[YourPackageName]` pelo nome do pacote especificado ao criar o aplicativo Android.

        <!-- Push Notifications connects to Internet services. -->
        <uses-permission android:name="android.permission.INTERNET" />

        <!-- Keeps the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />

         <!--
         Creates a custom permission so only this app can receive its messages.

         NOTE: the permission *must* be called PACKAGE.permission.C2D_MESSAGE,
               where PACKAGE is the application's package name.
        -->
        <permission 
            android:name="[YourPackageName].permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="[YourPackageName].permission.C2D_MESSAGE" />

        <uses-permission android:name="com.nokia.pushnotifications.permission.RECEIVE" />

        <application
            android:allowBackup="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >
            <activity
                android:name="[YourPackageName].MainActivity"
                android:label="@string/app_name" >
                <intent-filter>
                    <action android:name="android.intent.action.MAIN" />
                    <category android:name="android.intent.category.LAUNCHER" />
                </intent-filter>
            </activity>

            <receiver
                android:name="com.nokia.push.PushBroadcastReceiver"
                android:permission="com.nokia.pushnotifications.permission.SEND" >
                <intent-filter>

                    <!-- Receives the actual messages. -->
                    <action android:name="com.nokia.pushnotifications.intent.RECEIVE" />
                    <!-- Receives the registration id. -->
                    <action android:name="com.nokia.pushnotifications.intent.REGISTRATION" />
                    <category android:name="[YourPackageName]" />
                </intent-filter>
            </receiver>

            <service android:name="[YourPackageName].PushIntentService" />
        </application> 

2.  No Gerenciador de Pacotes, clique com o botão direito do mouse no pacote (no nó `src`), clique em **Novo**, **Classe** e crie uma nova classe denominada **ConfigurationSettings.java**
    ![][13]

    Em seguida, adicione o código a seguir a ela, que define as constantes usadas na amostra:

        public class ConfigurationSettings {
            public static String NotificationHubName = "";
            public static String NotificationHubConnectionString = "";
            public static String SenderId = "";
        }

    Preencha as constantes acima com seu **SenderId** de configuração do console de push da Nokia e **NotificationHubName** e **NotificationHubConnectionString** (DefaultListenSharedAccessSignature) do portal de gerenciamento.

3.  Em **MainActivity.java**, adicione a instrução de importação a seguir:

        import com.nokia.push.PushRegistrar;

    e, em seguida, adicione o código a seguir no método onCreate:

        // Make sure the device has the proper dependencies.
        PushRegistrar.checkDevice(this);

        // Make sure the manifest was properly set - comment out this line
        // while developing the app, then uncomment it when it's ready
        PushRegistrar.checkManifest(this);

        // Register the device with the Nokia Notification service
        PushRegistrar.register(this, ConfigurationSettings.SenderId);

4.  Adicione outra nova classe denominada **PushIntentService.java** e inclua o código a seguir, que será registrado no Hub de Notificação e também lidará com as mensagens recebidas desse Hub de Notificação.

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import android.util.Log;

        import com.microsoft.windowsazure.messaging.NotificationHub;
        import com.nokia.push.PushBaseIntentService;

        /**
         * IntentService responsible for handling push notification messages.
         */
        public class PushIntentService extends PushBaseIntentService {

            private NotificationManager mNotificationManager;
            private static NotificationHub hub;
            public static final int NOTIFICATION_ID = 1;
            private static final String TAG = "NokiaXPush/PushIntentService";

            /**
             * Constructor.
             */
            public PushIntentService() {
            }

            @Override
            protected String[] getSenderIds(Context context) {
                return new String[] { ConfigurationSettings.SenderId };
            }   

            @Override
            protected void onRegistered(Context context, String registrationId) {
                Log.i(TAG, "Nokia Registration ID \"" + registrationId + "\"");
                RegisterWithNotificationHub(context, registrationId);
            }

            public static void RegisterWithNotificationHub(Context context, String registrationId) {
                if (hub == null) {
                    hub = new NotificationHub(
                            ConfigurationSettings.NotificationHubName, 
                            ConfigurationSettings.NotificationHubConnectionString, 
                            context);
                }
                try {
                    hub.register(registrationId);
                    Log.i(TAG, "Registered with Notification Hub - '" 
                            + ConfigurationSettings.NotificationHubName + "'" 
                            + " with RegistrationID - '"
                            + registrationId + "'");
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }

            @Override
            protected void onMessage(Context context, Intent intent) {
                String message = intentExtrasToString(intent.getExtras());
                Log.i(TAG, "Received message. Extras: " + message);
                generateNotification(context, message);
            }

            /**
             * Extracts the key-value pairs from the given Intent extras and returns
             * them in a string.
             */
            private String intentExtrasToString(Bundle extras) {
                StringBuilder sb = new StringBuilder();
                sb.append("{ ");

                for (String key : extras.keySet()) {
                    sb.append(sb.length() <= 2 ? "" : ", ");
                    sb.append(key).append("=").append(extras.get(key));
                }

                sb.append(" }");
                return sb.toString();
            }

            /**
             * Issues a notification to inform the user that server has sent a message.
             */
            private void generateNotification(Context context, String message)
            {
                mNotificationManager = (NotificationManager)
                        context.getSystemService(Context.NOTIFICATION_SERVICE);

                PendingIntent contentIntent = PendingIntent.getActivity(context, 0,
                    new Intent(context, MainActivity.class), 0);

                NotificationCompat.Builder mBuilder =
                    new NotificationCompat.Builder(context)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle("Notification Hub Demo")
                    .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(message))
                    .setContentText(message);

                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }

            @Override
            protected void onError(Context arg0, String errorId) {
                Log.i(TAG, "Received error:  " + errorId);
            }

            @Override
            protected void onUnregistered(Context arg0, String errorId) {
                Log.i(TAG, "Received recoverable error: " + errorId);
            }
        }

## <a name="send"></a>Como enviar uma notificação para seu aplicativo

Você pode enviar notificações usando Hubs de Notificação de qualquer back-end usando a [interface REST][interface REST]. Neste tutorial, mostramos isso usando um aplicativo de console do .NET.

1.  Crie um novo aplicativo de console do Visual C\#:

    ![][14]

2.  Adicione uma referência ao SDK do Barramento de Serviço do Azure com o [pacote NuGet do WindowsAzure.ServiceBus][pacote NuGet do WindowsAzure.ServiceBus]. No menu principal do Visual Studio, clique em **Ferramentas**, em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**. Em seguida, na janela do console, digite o seguinte e pressione Enter:

        Install-Package WindowsAzure.ServiceBus

3.  Abra o arquivo Program.cs e adicione a seguinte instrução using:

        using Microsoft.ServiceBus.Notifications;

4.  Em sua classe `Program`, adicione o método a seguir:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<DefaultFullSharedAccessSignatureSASConnectionString>", "<hub name>");
            await hub.SendNokiaXNativeNotificationAsync("{\"data\" : {\"payload\":\"" + "Hello from Azure" + "\"}}");
        }

5.  Em seguida, adicione as seguintes linhas em seu método Main:

         SendNotificationAsync();
         Console.ReadLine();

## <a name="test-app"></a>Testando seu aplicativo

Para testar este aplicativo com um telefone real, basta conectá-lo ao seu computador com um cabo USB.

Para testar este aplicativo com o emulador:

1.  Na barra de ferramentas superior do Eclipse, clique em Executar e selecione seu aplicativo.

2.  Isto carregará seu aplicativo em telefone anexado ou, então, iniciará o emulador, carregando e executando o aplicativo.

3.  O aplicativo recuperará o registrationId do Serviço de Notificação da Nokia e registrará no Hub de Notificação.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>
Se o aplicativo Android conseguir se registrar com &ecirc;xito no Hub de Notifica&ccedil;&atilde;o, voc&ecirc; ver&aacute; uma mensagem como a seguinte nos logs de 'Logcat do Eclipse':
'Registrado no Hub de Notifica&ccedil;&atilde;o - '<yourNotificationHubName>' com RegistrationID - '<RegistrationIdReturnedByNokiaNotificationService'' </p>
</div>

4.  Para enviar uma notificação de teste ao usar o aplicativo do console .Net, simplesmente pressione a tecla F5 no Visual Studio para executar o aplicativo e ele enviará uma notificação que aparecerá na área de notificação superior de seu dispositivo ou emulador.

<!-- Images. -->
<!-- URLs. -->

  [Windows Universal]: /pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows Universal"
  [Windows Phone]: /pt-br/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /pt-br/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /pt-br/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /pt-br/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Nokia X]: /pt-br/documentation/articles/notification-hubs-nokia-x-get-started/ "Nokia X"
  [Xamarin.iOS]: /pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [Configurar o Serviço de Notificação da Nokia]: #register
  [Configurar seu Hub de Notificação]: #configure-hub
  [Conectando seu aplicativo ao Hub de Notificação]: #connect-hub
  [Como enviar uma notificação para seu aplicativo]: #send
  [Testando seu aplicativo]: #test-app
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [aqui]: http://developer.nokia.com/resources/library/nokia-x/getting-started/environment-setup.html
  [1]: http://developer.nokia.com/resources/library/nokia-x/getting-started/device-setup.html
  [2]: http://go.microsoft.com/fwlink/?linkid=389797&clcid=0x409
  [3]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
  [Console do Desenvolvedor da API de Notificações da Nokia]: https://console.push.nokia.com/ncm/Web/index.jsp
  []: ./media/notification-hubs-nokia-x-get-started/NokiaConsole.png
  [4]: ./media/notification-hubs-nokia-x-get-started/NokiaConsoleService.png
  [link]: http://developer.nokia.com/resources/library/nokia-x/nokia-notifications/nokia-notifications-developer-guide.html
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [5]: ./media/notification-hubs-nokia-x-get-started/AzureManagementPortal.png
  [6]: ./media/notification-hubs-nokia-x-get-started/AzureManagementCreateNH.png
  [7]: ./media/notification-hubs-nokia-x-get-started/NHConfigureTopItem.png
  [8]: ./media/notification-hubs-nokia-x-get-started/NHConfigure.png
  [9]: ./media/notification-hubs-nokia-x-get-started/NHConfigureNokiaX.png
  [10]: ./media/notification-hubs-nokia-x-get-started/NHDashboard.png
  [11]: ./media/notification-hubs-nokia-x-get-started/NewAndroidApp.png
  [12]: ./media/notification-hubs-nokia-x-get-started/AndroidBuildTarget.png
  [13]: ./media/notification-hubs-nokia-x-get-started/NewJavaClass.png
  [interface REST]: http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx
  [14]: ./media/notification-hubs-nokia-x-get-started/ConsoleProject.png
  [pacote NuGet do WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
