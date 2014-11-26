1.  Abra o arquivo `AndroidManifest.xml`. No código nas duas próximas etapas, substitua *`**my_app_package**`* pelo nome do pacote do aplicativo para o seu projeto, que é o valor do atributo `package` da marca `manifest`.

2.  Adicione as seguintes novas permissões depois do elemento `uses-permission`:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3.  Adicione o código a seguir após o marca de abertura `application`:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                        android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

4.  Baixe e descompacte o [Mobile Services Android SDK][Mobile Services Android SDK], abra a pasta **notificações**, copie o arquivo **notifications-1.0.1.jar** para a pasta *libs* do seu projeto Eclipse e atualize a pasta *libs*.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Os n&uacute;meros no fim do nome do arquivo podem ser alterados em vers&otilde;es subsequentes do SDK.</p>
</div>

5.  Abra o arquivo *ToDoItemActivity.java* e adicione a seguinte declaração de importação:

        import com.microsoft.windowsazure.notifications.NotificationsManager;

6.  Adicione a seguinte variável privada à classe, onde *`<PROJECT_NUMBER>`* é o Número do Projeto atribuído pelo Google para seu aplicativo no procedimento anterior:

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

7.  No método **onCreate**, antes que a instância MobileServiceClient seja criada, adicione este código, que registra o Manipulador de Notificações para o dispositivo:

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Posteriormente, definiremos o MyHandler.class referenciado neste código.

8.  No Gerenciador de Pacotes, clique com o botão direito no pacote (no nó `src`), clique em **Novo** e em **Classe**.

9.  Em **Nome**, digite `MyHandler`, no tipo **Superclasse**, digite \` `com.microsoft.windowsazure.notifications.NotificationsHandler` e clique em **Concluir**.

    ![][0]

    Isto criará a nova classe MyHandler.

10. Adicione as seguintes instruções de importação para a classe `MyHandler`:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;

        import com.microsoft.windowsazure.mobileservices.Registration;
        import com.microsoft.windowsazure.mobileservices.RegistrationCallback;

11. Em seguida, adicione os membros a seguir para a classe `MyHandler`:

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

12. Na classe `MyHandler`, adicione o código a seguir para substituir o método **onRegistered**: que registra o dispositivo com o Hub de Notificação do serviço móvel.

        @Override
        public void onRegistered(Context context, String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);

            ToDoActivity.mClient.getPush().register(gcmRegistrationId, null, new RegistrationCallback() {
                @Override
                public void onRegister(Registration registration, Exception exception) {
                      if (exception != null) {
                            // handle error
                      }
                }
            });
        }

13. Na classe `MyHandler`, adicione o código a seguir para substituir o método **onReceive**, que faz com que a notificação seja exibida quando é recebida.

        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");

            sendNotification(nhMessage);
        }

        private void sendNotification(String msg) {
            mNotificationManager = (NotificationManager)
                      ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                  new Intent(ctx, ToDoActivity.class), 0);

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

Seu aplicativo foi atualizado para oferecer suporte a notificações de push.



  [Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  [0]: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
