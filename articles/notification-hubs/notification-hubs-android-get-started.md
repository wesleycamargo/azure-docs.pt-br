<properties 
	pageTitle="Introdução aos Hubs de notificação do Azure" 
	description="Saiba como usar os Hubs de notificação do Azure para notificações por push." 
	services="notification-hubs" 
	documentationCenter="android" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>
<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="wesmc"/>

# Introdução aos Hubs de Notificação

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Visão geral

Este tópico mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo Android. Neste tutorial, você cria um aplicativo em branco para Android que recebe notificações por push usando o Google Cloud Messaging (GCM). Ao concluir, você poderá transmitir notificações por push a todos os dispositivos que executam seu aplicativo usando o hub de notificação.

Este tutorial demonstra o simples cenário de transmissão usando Hubs de Notificação. Certifique-se de seguir o próximo tutorial para aprender a usar hubs de notificação, para atender usuários e grupos de dispositivos específicos.


##Pré-requisitos

Este tutorial exige o seguinte:

+ Android Studio, que você pode baixar <a href="http://go.microsoft.com/fwlink/?LinkId=389797">daqui</a>
+ Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started%2F).


A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre hubs de notificação para aplicativos do Android.


##Criar um projeto que ofereça suporte ao Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]


##Configurar um Novo Hub de Notificação

[AZURE.INCLUDE [notification-hubs-android-configure-push](../../includes/notification-hubs-android-configure-push.md)]

##<a id="connecting-app"></a>Conexão de seu aplicativo ao Hub de Notificação

###Criar novo projeto Android

1. No Android Studio, inicie um novo projeto Android Studio.

   	![][13]

2. Escolha o fator forma **Telefone e Tablet** e o **SDK Mínimo** ao qual você deseja dar suporte. Em seguida, clique em **Próximo**.

   	![][14]

3. Escolha **Atividade em Branco** para a atividade principal. Em seguida, clique em **Avançar** e então em **Concluir**.

###Inclua o Google Play Services no projeto

[AZURE.INCLUDE [Adicionar Play Services](../../includes/mobile-services-add-Google-play-services.md)]

###Incluir código

1. Baixe o SDK do Android dos Hubs de Notificação <a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">daqui</a>. Extraia o arquivo .zip e copie **notificationhubs\notification-hubs-0.4.jar** e **notifications\notifications-1.0.1.jar** para o diretório **app\libs** do seu projeto. Você pode fazer isso arrastando os arquivos diretamente para a pasta **libs** na janela Exibição do Projeto do Android Studio. Atualize a pasta libs.



	A documentação de referência para esses dois pacotes está localizada nos links abaixo: * [com.microsoft.windowsazure.messaging](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/messaging/package-summary.html) * [com.microsoft.windowsazure.notifications](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/notifications/package-summary.html)


    > [AZURE.NOTE]Os números no fim do nome do arquivo podem ser alterados em versões subsequentes do SDK.

2. Em seguida, você configurará o aplicativo para obter uma identificação de registro do GCM e o usará para registrar a instância do aplicativo para o hub de notificação.

	No arquivo AndroidManifest.xml, adicione a linha a seguir abaixo da marca `</application>`. Substitua `<your package>` pelo nome do pacote mostrado na parte superior do arquivo AndroidManifest.xml (`com.example.testnotificationhubs` neste exemplo).

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

3. Na classe **MainActivity**, adicione as declarações `import` a seguir acima da declaração da classe.

		import android.app.AlertDialog;
		import android.content.DialogInterface;
		import android.os.AsyncTask;
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;


4. Adicione os seguintes membros privados na parte superior da classe.

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;
    	private String HubName = "<Enter Your Hub Name>";
		private String HubListenConnectionString = "<Your default listen connection string>";


	Atualize os três espaços reservados: * **SENDER_ID**: defina a `SENDER_ID` como o Número do Projeto obtido anteriormente do projeto criado no [Google Cloud Console](http://cloud.google.com/console). * **HubListenConnectionString**: defina `HubListenConnectionString` como a cadeia de conexão **DefaultListenAccessSignature** para seu hub. Você pode copiar essa cadeia de conexão clicando em **Exibir Cadeia de Conexão** na guia **Painel** de seu hub no [Portal de Gerenciamento do Azure]. * **HubName**: o nome do seu hub de notificação que aparece na parte superior da página do Azure para o hub (**não** é a url completa). Por exemplo: `"myhub"`.



5. No método **OnCreate** da classe **MainActivity**, adicione o código a seguir para executar o registro na criação da atividade.

        MyHandler.mainActivity = this;
        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);
        hub = new NotificationHub(HubName, HubListenConnectionString, this);
        registerWithNotificationHubs();

6. Em **MainActivity.java**, adicione o código abaixo para o método **registerWithNotificationHubs()**. Esse método relata êxito após o registro no Google Cloud Messaging e mo Hub de Notificação:

    	@SuppressWarnings("unchecked")
    	private void registerWithNotificationHubs() {
        	new AsyncTask() {
            	@Override
            	protected Object doInBackground(Object... params) {
                	try {
                    	String regid = gcm.register(SENDER_ID);
                    DialogNotify("Registered Successfully","RegId : " + 
						hub.register(regid).getRegistrationId());
                	} catch (Exception e) {
                    	DialogNotify("Exception",e.getMessage());
                    	return e;
                	}
                	return null;
            	}
        	}.execute(null, null, null);
    	}

		
		/**
		  * A modal AlertDialog for displaying a message on the UI thread
		  * when theres an exception or message to report.
		  * 
		  * @param title   Title for the AlertDialog box. 
		  * @param message The message displayed for the AlertDialog box.
		  */
    	public void DialogNotify(final String title,final String message)
    	{
        	final AlertDialog.Builder dlg;
        	dlg = new AlertDialog.Builder(this);

        	runOnUiThread(new Runnable() {
            	@Override
            	public void run() {
                	AlertDialog dlgAlert = dlg.create();
                	dlgAlert.setTitle(title);
                	dlgAlert.setButton(DialogInterface.BUTTON_POSITIVE, 
						(CharSequence) "OK", 
						new DialogInterface.OnClickListener() {
                            public void onClick(DialogInterface dialog, int which) {
                                dialog.dismiss();
                            }
                        });
                	dlgAlert.setMessage(message);
                	dlgAlert.setCancelable(false);
                	dlgAlert.show();
            	}
        	});
    	}

7. Você deve incluir seu próprio destinatário, pois o Android não exibe notificações. Em **AndroidManifest.xml**, adicione o elemento a seguir ao elemento `<application>`.

	> [AZURE.NOTE]Substitua o espaço reservado pelo nome do pacote.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>


8. Na Exibição de Projeto, expanda **app** -> **src** -> **main** -> **java**. Clique na pasta do seu pacote em **java**, clique em **Novo** e então clique em **Classe Java**.

	![][6]

9. No campo **Nome** da nova classe, digite **MyHandler** e, em seguida, clique em **OK**
	

10. Adicione as seguintes instruções de importação à parte superior de **MyHandler.java**:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;
		

11. Atualize a declaração de classe como a seguir para tornar `MyHandler` uma subclasse de `com.microsoft.windowsazure.notifications.NotificationsHandler`, como mostrado abaixo.

		public class MyHandler extends NotificationsHandler {


12. Adicione o código a seguir à classe `MyHandler`.

	Esse código substitui o método `OnReceive` para que o manipulador exiba um `AlertDialog` para mostrar as notificações recebidas. O manipulador também envia a notificação do gerenciador de notificações do Android usando o método `sendNotification()`.

    	public static final int NOTIFICATION_ID = 1;
    	private NotificationManager mNotificationManager;
    	NotificationCompat.Builder builder;
    	Context ctx;

    	static public MainActivity mainActivity;

    	@Override
    	public void onReceive(Context context, Bundle bundle) {
        	ctx = context;
        	String nhMessage = bundle.getString("message");

        	sendNotification(nhMessage);
        	mainActivity.DialogNotify("Received Notification",nhMessage);
    	}

    	private void sendNotification(String msg) {
        	mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        	PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

			NotificationCompat.Builder mBuilder =
				new NotificationCompat.Builder(ctx)
					.setSmallIcon(R.mipmap.ic_launcher)
					.setContentTitle("Notification Hub Demo")
					.setStyle(new NotificationCompat.BigTextStyle()
					.bigText(msg))
					.setContentText(msg);

			mBuilder.setContentIntent(contentIntent);
			mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}
	
13. No Android Studio na barra de menus, clique em **Compilar** -> **Recompilar Projeto** para garantir que nenhum erro seja detectado.

##Como Enviar Notificações



Você pode testar o recebimento de notificações em seu aplicativo ao enviar notificações no portal do Azure usando a guia de depuração no hub de notificação como mostrado na tela abaixo.

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

![][31]

1. Na Exibição de Projeto do Android Studio, expanda **Aplicativo**->**src**->**main**->**res**->**layout**. Abra o arquivo de layout **activity_main.xml** e clique na guia **Texto** para atualizar o conteúdo do texto do arquivo. Atualize-o com o código abaixo que adiciona um novo controle `Button` e `EditText` para o envio de mensagens de notificação para o hub de notificação. Adicione este código na parte inferior imediatamente antes de `</RelativeLayout>`.

	    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

	    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. Na Exibição de Projeto do Android Studio, expanda **Aplicativo** -> **src** -> **main** -> **res** -> **valores**. Abra o arquivo **strings.xml** e adicione os valores de cadeia de caracteres referenciados pelos novos controles `Button` e `EditText`. Adicione-os à parte inferior do arquivo imediatamente antes de `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. Em seu arquivo **MainActivity.java**, adicione as seguintes instruções `import` acima da classe `MainActivity`.

		import java.net.URLEncoder;
		import javax.crypto.Mac;
		import javax.crypto.spec.SecretKeySpec;
		
		import android.util.Base64;
		import android.view.View;
		import android.widget.EditText;
		
		import org.apache.http.HttpResponse;
		import org.apache.http.client.HttpClient;
		import org.apache.http.client.methods.HttpPost;
		import org.apache.http.entity.StringEntity;
		import org.apache.http.impl.client.DefaultHttpClient;


3. Em seu arquivo **MainActivity.java**, adicione os seguintes membros à parte superior da classe `MainActivity`.

	Insira o nome do seu hub para `HubName`, e não o namespace. Por exemplo, “myhub”. Além disso, insira a cadeia de conexão **DefaultFullSharedAccessSignature**. Essa cadeia de conexão pode ser copiada do [Portal de Gerenciamento do Azure] clicando em **Exibir Cadeia de Conexão** na guia **Painel** do seu hub de notificação.

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;
		private String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

4. A atividade contém o nome do hub e a cadeia de conexão completa de acesso compartilhado para o hub. Você deve criar um token SaS (Software Access Signature) para autenticar uma solicitação POST para o envio de mensagens para o seu hub de notificação. Isso é feito analisando os dados chave da cadeia de conexão e então criando o token SaS como mencionado na referência da API REST [Conceitos comuns](http://msdn.microsoft.com/library/azure/dn495627.aspx) .

	Em **MainActivity.java**, adicione o método a seguir à classe `MainActivity` para analisar sua cadeia de conexão.

	    /**
    	 * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
    	 * to parse the connection string so a SaS authentication token can be 
    	 * constructed.
    	 * 
    	 * @param connectionString This must be the DefaultFullSharedAccess connection
    	 *                         string for this example.
	     */
	    private void ParseConnectionString(String connectionString)
	    {
	        String[] parts = connectionString.split(";");
	        if (parts.length != 3)
	            throw new RuntimeException("Error parsing connection string: "
	                    + connectionString);
	
	        for (int i = 0; i < parts.length; i++) {
	            if (parts[i].startsWith("Endpoint")) {
	                this.HubEndpoint = "https" + parts[i].substring(11);
	            } else if (parts[i].startsWith("SharedAccessKeyName")) {
	                this.HubSasKeyName = parts[i].substring(20);
	            } else if (parts[i].startsWith("SharedAccessKey")) {
	                this.HubSasKeyValue = parts[i].substring(16);
	            }
	        }
	    }

5. Em **MainActivity.java**, adicione o método a seguir à classe `MainActivity` para criar um token de autenticação SaS.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         * 
         * @param uri The un-encoded resource URI string for this operation. The resource
         *            URI is the full URI of the Service Bus resource to which access is
         *            claimed. For example, 
         *            "http://<namespace>.servicebus.windows.net/<hubName>" 
         */
        private String generateSasToken(String uri) {
    
            String targetUri;
            try {
                targetUri = URLEncoder
                        .encode(uri.toString().toLowerCase(), "UTF-8")
                        .toLowerCase();
    
                long expiresOnDate = System.currentTimeMillis();
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60 * 1000;
                long expires = expiresOnDate / 1000;
                String toSign = targetUri + "\n" + expires;
    
                // Get an hmac_sha1 key from the raw key bytes
                byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
                SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
    
                // Get an hmac_sha1 Mac instance and initialize with the signing key
                Mac mac = Mac.getInstance("HmacSHA256");
                mac.init(signingKey);
    
                // Compute the hmac on input data bytes
                byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
    
            	// Using android.util.Base64 for Android Studio instead of 
	            // Apache commons codec.
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
    
                // construct authorization string
                String token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
                return token;
            } catch (Exception e) {
                DialogNotify("Exception Generating SaS",e.getMessage().toString());
            }
    
            return null;
        }


6. Em **MainActivity.java**, adicione o método a seguir à classe `MainActivity` para manipular o clique no botão **Enviar Notificação** e envie a mensagem de notificação para o hub usando a API REST.

        /**
         * Send Notification button click handler. This method parses the 
         * DefaultFullSharedAccess connection string and generates a SaS token. The 
         * token is added to the Authorization header on the POST request to the 
         * notification hub. The text in the editTextNotificationMessage control
         * is added as the JSON body for the request to add a GCM message to the hub.
         * 
         * @param v 
         */
        public void sendNotificationButtonOnClick(View v) {
            EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
            final String json = "{"data":{"message":"" + notificationText.getText().toString() + ""}}";
    
            new Thread()
            {
                public void run()
                {
                    try
                    {
                        HttpClient client = new DefaultHttpClient();
    
                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(HubFullAccess);
                        String url = HubEndpoint + HubName + "/messages/?api-version=2015-01";
                        HttpPost post = new HttpPost(url);
    
                        // Authenticate the POST request with the SaS token.
                        post.setHeader("Authorization", generateSasToken(url));
    
                        // JSON content for GCM
                        post.setHeader("Content-Type", "application/json;charset=utf-8");
    
                        // Notification format should be GCM
                        post.setHeader("ServiceBusNotification-Format", "gcm");
                        post.setEntity(new StringEntity(json));
    
                        HttpResponse response = client.execute(post);
                    }
                    catch(Exception e)
                    {
                        DialogNotify("Exception",e.getMessage().toString());
                    }
                }
            }.start();
        }



##Testando seu aplicativo

####Testando no emulador
Se você quiser testar no emulador, verifique se a sua imagem de emulador dá suporte ao nível de API do Google escolhido para o seu aplicativo. Se a imagem não oferecer suporte às APIs do Google, você acabará com a exceção **SERVICE_NOT_AVAILABLE**.

Verifique também se você adicionou sua conta do Google ao seu emulador em execução sob **Configurações**->**Contas**. Caso contrário, suas tentativas de se registrar no GCM podem resultar na exceção **AUTHENTICATION_FAILED**.

####Testando o aplicativo     

1. Execute o aplicativo e observe que a id de registro é relatada para um registro bem-sucedido.

   	![][18]

2. Insira uma mensagem de notificação a ser enviada para todos os dispositivos Android registrados no hub.

   	![][19]

3. Pressione **Enviar Notificação**. Todos os dispositivos que tiverem o aplicativo em execução mostrarão uma `AlertDialog` com a mensagem de notificação. Os dispositivos que não tiverem o aplicativo em execução mas que foram anteriormente registrados para as notificações receberão uma notificação adicionada ao gerenciador de notificações. As notificações podem ser exibidas passando o dedo do canto superior esquerdo.

   	![][21]

##Próximas etapas

Neste simples exemplo, você envia notificações para todos os seus dispositivos Android. Para selecionar usuários de destinos específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá consultar [Usar Hubs de Notificação para enviar notícias de última hora]. Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação].


<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png

[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png


<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Usar Hubs de Notificação para enviar notificações por push aos usuários]: notification-hubs-aspnet-backend-android-notify-users.md
[Usar Hubs de Notificação para enviar notícias de última hora]: notification-hubs-aspnet-backend-android-breaking-news.md
 

<!---HONumber=62-->