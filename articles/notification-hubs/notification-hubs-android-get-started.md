<properties
	pageTitle="Introdução aos Hubs de Notificação do Azure para aplicativos do Android | Microsoft Azure"
	description="Neste tutorial, você aprende a usar os Hubs de Notificação do Azure para enviar notificações por push a dispositivos Android."
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
	ms.topic="hero-article"
	ms.date="12/15/2015"
	ms.author="wesmc"/>

# Introdução aos Hubs de Notificação para aplicativos do Android

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Visão geral

Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo Android. Você criará um aplicativo para Android em branco que recebe notificações por push usando o GCM(Google Cloud Messaging). Ao finalizar, você poderá usar seu hub de notificação para transmitir notificações por push a todos os dispositivos que executam seu aplicativo.

Este tutorial demonstra o cenário de transmissão simples usando Hubs de Notificação. Certifique-se de seguir o próximo tutorial para aprender a usar Hubs de Notificação a fim de atender aos usuários e grupos de dispositivos específicos.


## Antes de começar

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

O código completo para este tutorial pode ser encontrado no GitHub [aqui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted).


##Pré-requisitos

Este tutorial exige o seguinte:

+ Android Studio, que você pode baixar no <a href="http://go.microsoft.com/fwlink/?LinkId=389797">site do Android</a>.
+ Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started%2F).


A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre Hubs de Notificação para aplicativos Android.


##Criar um projeto que ofereça suporte ao Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


##Configurar um novo hub de notificação


[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;7. Clique na guia **Configurar** na parte superior, digite o valor de **Chave da API** que você obteve na etapa anterior e clique em **Salvar**.

&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)

Agora, o seu hub de notificação está configurado para funcionar com o GCM e você tem as cadeias de conexão ao registrar seu aplicativo para receber notificações e enviar notificações por push.

##<a id="connecting-app"></a>Conectar seu aplicativo ao hub de notificação

###Criar um novo aplicativo Android

1. No Android Studio, inicie um novo projeto Android Studio.

   	![][13]

2. Escolha o fator forma **Telefone e Tablet** e o **SDK Mínimo** ao qual você deseja oferecer suporte. Em seguida, clique em **Próximo**.

   	![][14]

3. Escolha **Atividade em Branco** para a atividade principal, clique em **Avançar** e em **Concluir**.

###Adicionar serviços do Google Play ao projeto

[AZURE.INCLUDE [Adicionar Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###Incluir código

1. Baixe o arquivo notification-hubs-0.4.jar da guia **Arquivos** do [Notification-Hubs-Android-SDK no Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4). Arraste os arquivos diretamente para a pasta **libs** na janela Exibição do Projeto do Android Studio. Em seguida, clique no arquivo com o botão direito do mouse e clique em **Adicionar como biblioteca**.
  
2. No arquivo Build.Gradle para o **aplicativo**, adicione a linha a seguir à seção **dependências**.

	    compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

	Adicione o seguinte repositório após a seção **dependências**.

		repositories {
		    maven {
		        url "http://dl.bintray.com/microsoftazuremobile/SDK"
		    }
		}

3. Configure o aplicativo para obter uma ID de registro do GCM e use-o para registrar a instância do aplicativo no hub de notificação.

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
		import android.widget.Toast;



4. Adicione os seguintes membros privados na parte superior da classe.

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;
    	private String HubName = "<Enter Your Hub Name>";
		private String HubListenConnectionString = "<Your default listen connection string>";
	    private static Boolean isVisible = false;


	Não se esqueça de atualizar os três espaços reservados: * **SENDER\_ID**: defina `SENDER_ID` como o número do projeto obtido anteriormente do projeto criado no [Google Cloud Console](http://cloud.google.com/console). * **HubListenConnectionString**: defina `HubListenConnectionString` como a cadeia de conexão **DefaultListenAccessSignature** para seu hub. Você pode copiar essa cadeia de conexão clicando em **Exibir Cadeia de Conexão** na guia **Painel** de seu hub no [Portal Clássico do Azure]. * **HubName**: use o nome do hub de notificação que aparece na parte superior da página do Azure para o hub (**não** é a URL completa). Por exemplo, use `"myhub"`.



5. No método **OnCreate** da classe **MainActivity**, adicione o código a seguir para executar o registro no hub de notificação quando a atividade for criada.

        MyHandler.mainActivity = this;
        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);
        hub = new NotificationHub(HubName, HubListenConnectionString, this);
        registerWithNotificationHubs();

6. Em **MainActivity.java**, adicione o código abaixo para o método **registerWithNotificationHubs()**. Esse método relata êxito após o registro no Google Cloud Messaging e no hub de notificação.

    	@SuppressWarnings("unchecked")
    	private void registerWithNotificationHubs() {
        	new AsyncTask() {
            	@Override
            	protected Object doInBackground(Object... params) {
                	try {
                    	String regid = gcm.register(SENDER_ID);
                    ToastNotify("Registered Successfully - RegId : " +
						hub.register(regid).getRegistrationId());
                	} catch (Exception e) {
                    	ToastNotify("Registration Exception Message - " + e.getMessage());
                    	return e;
                	}
                	return null;
            	}
        	}.execute(null, null, null);
    	}


7. Adicione o método `ToastNotify` à atividade para exibir a notificação quando o aplicativo estiver em execução e visível. Substitua também `onStart`, `onPause`, `onResume` e `onStop` para determinar se a atividade está visível para exibir a notificação do sistema.

	    @Override
	    protected void onStart() {
	        super.onStart();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onPause() {
	        super.onPause();
	        isVisible = false;
	    }
	
	    @Override
	    protected void onResume() {
	        super.onResume();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onStop() {
	        super.onStop();
	        isVisible = false;
	    }

	    public void ToastNotify(final String notificationMessage)
	    {
	        if (isVisible == true)
	            runOnUiThread(new Runnable() {
	                @Override
	                public void run() {
	                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
	                }
	            });
	    }

8. Você deve incluir seu próprio destinatário, pois o Android não exibe notificações. Em **AndroidManifest.xml**, adicione o elemento a seguir ao elemento `<application>`.

	> [AZURE.NOTE] Substitua o espaço reservado pelo nome do pacote.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>


9. Na Exibição de Projeto, expanda **app** > **src** > **main** > **java**. Clique na pasta do seu pacote em **java**, clique em **Novo** e então clique em **Classe Java**.

	![][6]

10. No campo **Nome** da nova classe, digite **MyHandler** e clique em **OK**.


11. Adicione as seguintes instruções de importação à parte superior de **MyHandler.java**:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;


12. Atualize a declaração de classe como se segue para tornar `MyHandler` uma subclasse de `com.microsoft.windowsazure.notifications.NotificationsHandler`, como mostrado abaixo.

		public class MyHandler extends NotificationsHandler {


13. Adicione o código a seguir à classe `MyHandler`.

	Esse código substitui o método `OnReceive`, de forma que o manipulador exiba um pop-up de Notificação do Sistema para mostrar as notificações recebidas. O manipulador também envia a notificação ao gerenciador de notificações do Android usando o método `sendNotification()`.

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
	        mainActivity.ToastNotify(nhMessage);
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

14. No Android Studio, na barra de menus, clique em **Compilar** -> **Recompilar Projeto** para garantir que nenhum erro seja detectado.

##Enviar notificações



Você pode testar o recebimento de notificações no aplicativo enviando notificações no [Portal Clássico do Azure] por meio da guia de depuração no hub de notificação, como mostra a tela abaixo.

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## (Opcional) Enviar notificações do aplicativo


1. Na Exibição de Projeto do Android Studio, expanda **App** > **src** > **main** > **res** > **layout**. Abra o arquivo de layout **activity\_main.xml** e clique na guia **Texto** para atualizar o conteúdo do texto do arquivo. Atualize-o com o código abaixo, que adiciona novos controles `Button` e `EditText` para o envio de mensagens de notificação ao hub de notificação. Adicione este código à parte inferior imediatamente antes de `</RelativeLayout>`.

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

2. Adicione esta linha ao arquivo **build.gradle** em `android`

		useLibrary 'org.apache.http.legacy'

3. Na Exibição de Projeto do Android Studio, expanda **App** > **src** > **main** > **res** > **values**. Abra o arquivo **strings.xml** e adicione os valores de cadeia de caracteres referenciados pelos novos controles `Button` e `EditText`. Adicione-os à parte inferior do arquivo imediatamente antes de `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


4. Em seu arquivo **MainActivity.java**, adicione as seguintes instruções `import` acima da classe `MainActivity`.

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


5. Em seu arquivo **MainActivity.java**, adicione os seguintes membros à parte superior da classe `MainActivity`.

	Atualize `HubFullAccess` com a cadeia de conexão **DefaultFullSharedAccessSignature** para o hub. Essa cadeia de conexão pode ser copiada do [Portal Clássico do Azure] clicando em **Exibir Cadeia de Conexão** na guia **Painel** do hub de notificação.

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;
		private String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

6. A atividade contém o nome do hub e a cadeia de conexão completa de acesso compartilhado para o hub. Você deve criar um token SaS (Software Access Signature) para autenticar uma solicitação POST para o envio de mensagens para o seu hub de notificação. Isso é feito analisando os principais dados da cadeia de conexão e criando o token SaS, como mencionado na referência da API REST [Conceitos comuns](http://msdn.microsoft.com/library/azure/dn495627.aspx) .

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

7. Em **MainActivity.java**, adicione o método a seguir à classe `MainActivity` para criar um token de autenticação SaS.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         *
         * @param uri The unencoded resource URI string for this operation. The resource
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
	            // Apache commons codec
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");

                // Construct authorization string
                String token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
                return token;
            } catch (Exception e) {
                DialogNotify("Exception Generating SaS",e.getMessage().toString());
            }

            return null;
        }


8. Em **MainActivity.java**, adicione o método a seguir à classe `MainActivity` para manipular o botão **Enviar Notificação** clique e envie a mensagem de notificação ao hub usando a API REST.

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

                        // Authenticate the POST request with the SaS token
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



##Testar seu aplicativo

####Teste do emulador

Se você quiser testar no emulador, verifique se a sua imagem de emulador oferece suporte ao nível de API do Google escolhido para o seu aplicativo. Se a imagem não oferecer suporte às APIs do Google, você receberá a exceção **SERVICE\_NOT\_AVAILABLE**.

Verifique também se você adicionou sua conta do Google ao emulador em execução em **Configurações** > **Contas**. Caso contrário, suas tentativas de se registrar no GCM podem resultar na exceção **AUTHENTICATION\_FAILED**.

####Testando o aplicativo

1. Execute o aplicativo e observe que a ID de registro é relatada para um registro bem-sucedido.

   	![][18]

2. Insira uma mensagem de notificação a ser enviada para todos os dispositivos Android registrados no hub.

   	![][19]

3. Pressione **Enviar Notificação**. Todos os dispositivos que tiverem o aplicativo em execução mostrarão uma `AlertDialog` com a mensagem de notificação. Os dispositivos que não tiverem o aplicativo em execução mas que foram anteriormente registrados para as notificações receberão uma notificação adicionada ao gerenciador de notificações. As notificações podem ser exibidas passando o dedo do canto superior esquerdo.

   	![][21]

##Próximas etapas

Neste exemplo simples, você envia notificações para todos os seus dispositivos Windows usando o portal ou um aplicativo de console. Recomendamos o tutorial [Usar os Hubs de Notificação para enviar notificações por push aos usuários] como a próxima etapa. Ele mostra como enviar notificações de um back-end do ASP.NET usando marcas para direcionar usuários específicos.

Se desejar segmentar os usuários por grupos de interesse, você poderá ler [Usar Hubs de Notificação para enviar notícias mais recentes].

Para obter mais informações gerais sobre os Hubs de Notificação, confira [Diretrizes dos Hubs de Notificação].




<!-- Images. -->
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
[Portal Clássico do Azure]: https://manage.windowsazure.com/
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Usar os Hubs de Notificação para enviar notificações por push aos usuários]: notification-hubs-aspnet-backend-android-notify-users.md
[Usar Hubs de Notificação para enviar notícias mais recentes]: notification-hubs-aspnet-backend-android-breaking-news.md

<!---HONumber=AcomDC_0128_2016-->