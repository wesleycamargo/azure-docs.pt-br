1. No seu projeto de aplicativo, abra o arquivo `AndroidManifest.xml`. No código nas duas próximas etapas, substitua _`**my_app_package**`_ pelo nome do pacote do aplicativo para o seu projeto, que é o valor do atributo `package` da marca `manifest`. 

2. Adicione as seguintes novas permissões depois do elemento `uses-permission`:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Adicione o código a seguir após o marca de abertura `application`:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            						 	android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


4. Baixe e descompacte o [Mobile Services Android SDK], abra a pasta **notificações**, copie o arquivo **notifications-1.0.1.jar** para a pasta *libs* do seu projeto Eclipse e atualize a pasta *libs*.

    > [AZURE.NOTE]Os números no fim do nome do arquivo podem ser alterados em versões subsequentes do SDK.

5.  Abra o arquivo *ToDoItemActivity.java* e adicione a seguinte declaração de importação:

		import com.microsoft.windowsazure.notifications.NotificationsManager;


6. Adicione a seguinte variável particular à classe: substitua _`<PROJECT_NUMBER>`_ pelo número do projeto atribuído pelo Google para seu aplicativo no procedimento anterior:

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

7. Altere a definição do *MobileServiceClient* de **particular** para **público estático**, assim, a aparência agora é a seguinte:

		public static MobileServiceClient mClient;



9. Em seguida, precisamos adicionar uma nova classe para tratar das notificações. No Gerenciador de Pacotes, clique com o botão direito no pacote (no nó `src`), clique em **Novo** e em **Classe**.

10. Em **Nome**, digite `MyHandler`, em **Superclasse**, digite `com.microsoft.windowsazure.notifications.NotificationsHandler` e clique em **Concluir**.

	![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)

	Isto criará a nova classe MyHandler.

11. Adicione as seguintes instruções de importação para a classe `MyHandler`:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.AsyncTask;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;

	
12. Em seguida, adicione os membros a seguir para a classe `MyHandler`:

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
		Context ctx;


13. Na classe `MyHandler`, adicione o seguinte código para substituir o método **onRegistered**, que registra o dispositivo com o Hub de Notificação do serviço móvel.

		@Override
		public void onRegistered(Context context,  final String gcmRegistrationId) {
		    super.onRegistered(context, gcmRegistrationId);
	
		    new AsyncTask<Void, Void, Void>() {
		    		    	
		    	protected Void doInBackground(Void... params) {
		    		try {
		    		    ToDoActivity.mClient.getPush().register(gcmRegistrationId, null);
		    		    return null;
	    		    }
	    		    catch(Exception e) { 
			    		// handle error    		    
	    		    }
					return null;  		    
	    		}
		    }.execute();
		}



14. Na classe `MyHandler`, adicione o código a seguir para substituir o método **onReceive**, que faz com que a notificação seja exibida quando é recebida.

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


15. No arquivo TodoActivity.java, atualize o método **onCreate** da classe *ToDoActivity* para registrar a classe de manipulador de notificação. Certifique-se de adicionar este código depois de o *MobileServiceClient* ser instanciado.


		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Seu aplicativo foi atualizado para oferecer suporte a notificações de push.

<!-- URLs. -->
[Mobile Services Android SDK]: http://aka.ms/Iajk6q

<!---HONumber=62-->