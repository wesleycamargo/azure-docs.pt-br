<properties
	pageTitle="Como enviar notificações por push para Android com Hubs de Notificação do Azure | Microsoft Azure"
	description="Neste tutorial, você aprende a usar os Hubs de Notificação do Azure para enviar notificações por push a dispositivos Android."
	services="notification-hubs"
	documentationCenter="android"
	keywords="notificações por push, notificação por push, notificação por push do android"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>
<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.date="04/11/2016"
	ms.author="wesmc"/>

# Como enviar notificações por push para Android com Hubs de Notificação do Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Visão geral

> [AZURE.IMPORTANT] Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).

Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo Android. Você criará um aplicativo para Android em branco que recebe notificações por push usando o GCM(Google Cloud Messaging).

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

O código completo para este tutorial pode ser baixado no GitHub [clicando aqui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted).


##Pré-requisitos

Além de uma conta ativa do Azure mencionada acima, este tutorial requer apenas a última versão do [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797).

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre Hubs de Notificação para aplicativos Android.

##Como criar um projeto que dê suporte ao Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


##Configurar um novo hub de notificação


[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;6. Na folha **Configurações**, selecione **Serviços de Notificação** e **Google (GCM)**. Insira a chave de API e clique em **Salvar**.

&emsp;&emsp;![Hubs de Notificação do Azure - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Agora, o hub de notificação está configurado para funcionar com o GCM, e você tem as cadeias de conexão para registrar seu aplicativo para receber e enviar notificações por push.

##<a id="connecting-app"></a>Conectar seu aplicativo ao hub de notificação

###Criar um novo aplicativo Android

1. No Android Studio, inicie um novo projeto Android Studio.

   	![Android Studio - novo projeto][13]

2. Escolha o fator forma **Telefone e Tablet** e o **SDK Mínimo** ao qual você deseja oferecer suporte. Em seguida, clique em **Próximo**.

   	![Android Studio - fluxo de trabalho de criação de projeto][14]

3. Escolha **Atividade Vazia** para a atividade principal, clique em **Avançar** e em **Concluir**.

###Adicionar serviços do Google Play ao projeto

[AZURE.INCLUDE [Adicionar Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###Como adicionar bibliotecas dos Hubs de Notificação do Azure

1. Baixe o arquivo `notification-hubs-0.4.jar` da guia **Arquivos** do [Notification-Hubs-Android-SDK no Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4). Arraste o arquivo até a pasta **libs** do diretório de seu projeto.

2. No arquivo `Build.Gradle` para o **aplicativo**, adicione a linha a seguir à seção **dependências**.

	    compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

	Adicione o seguinte repositório após a seção **dependências**.

		repositories {
		    maven {
		        url "http://dl.bintray.com/microsoftazuremobile/SDK"
		    }
		}

### Atualização do arquivo AndroidManifest.xml.


1. Para oferecer suporte a GCM, devemos implementar um serviço de escuta de ID da Instância em nosso código que será usado para [obter tokens do registro](https://developers.google.com/cloud-messaging/android/client#sample-register) usando a [API de ID da Instância do Google](https://developers.google.com/instance-id/). Neste tutorial, daremos um nome à classe `MyInstanceIDService`: 
 
	Adicione a seguinte definição de serviço ao arquivo Androidmanifest.xml, dentro da marca `<application>`. Substitua o espaço reservado `<your package>` pelo nome real do pacote.

		<service android:name="<your package>.MyInstanceIDService" android:exported="false">
		    <intent-filter>
		        <action android:name="com.google.android.gms.iid.InstanceID"/>
		    </intent-filter>
		</service>


2. Após recebermos nosso token de registro GCM da API de ID da Instância, usaremos ele para [registrar com o Hub de Notificação do Azure](notification-hubs-registration-management.md). Ofereceremos suporte a esse registro em segundo plano usando um `IntentService` chamado `RegistrationIntentService`. Esse serviço também será responsável por [atualizar nosso token de registro GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).
 
	Adicione a seguinte definição de serviço ao arquivo Androidmanifest.xml, dentro da marca `<application>`.

        <service
            android:name="com.example.microsoft.getstarted.RegistrationIntentService"
            android:exported="false">
        </service>



3. Também definiremos um receptor para receber as notificações. Adicione a seguinte definição de receptor ao arquivo AndroidManifest.xml, dentro da marca `<application>`.

		<receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
		    android:permission="com.google.android.c2dm.permission.SEND">
		    <intent-filter>
		        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
		        <category android:name="<your package name>" />
		    </intent-filter>
		</receiver>



4. Adicione as seguintes permissões necessárias relacionadas ao GCM abaixo da marca `</application>`. Substitua `<your package>` pelo nome do pacote mostrado na parte superior do arquivo `AndroidManifest.xml`.

	Para saber mais sobre essas permissões, confira [Configuração de um aplicativo cliente GCM para Android](https://developers.google.com/cloud-messaging/android/client#manifest).

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>


### Como adicionar um código


1. Na Exibição de Projeto, expanda **app** > **src** > **main** > **java**. Clique na pasta do seu pacote em **java**, clique em **Novo** e então clique em **Classe Java**. Adicione uma nova classe chamada `NotificationSettings`. 

	![Android Studio - nova classe de Java][6]

	Atualize esses três espaços reservados no código a seguir para a classe `NotificationSettings`:
	* **SenderId**: o número do projeto obtido anteriormente no [Google Cloud Console](http://cloud.google.com/console).
	* **HubListenConnectionString**: a cadeia de conexão **DefaultListenAccessSignature** para o hub. Você pode copiar essa cadeia de conexão clicando em **Políticas de Acesso** na folha **Configurações** do hub no [Portal do Azure].
	* **HubName**: use o nome do hub de notificação que aparece na folha de hub no [Portal do Azure].

	Código `NotificationSettings`:

		public class NotificationSettings {
		    public static String SenderId = "<Your SenderId>";
		    public static String HubName = "<Your HubName>";
		    public static String HubListenConnectionString = "<Your default listen connection string>";
		}

2. Usando as etapas acima, adicione outra classe nova chamada `MyInstanceIDService`. Essa será nossa implementação do serviço de escuta de ID da Instância.

	O código para essa classe chamará nosso `IntentService` para [atualizar o token GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) em segundo plano.

		import android.content.Intent;
		import android.util.Log;
		import com.google.android.gms.iid.InstanceIDListenerService;
		
		
		public class MyInstanceIDService extends InstanceIDListenerService {
		
		    private static final String TAG = "MyInstanceIDService";
		
		    @Override
		    public void onTokenRefresh() {
		
		        Log.i(TAG, "Refreshing GCM Registration Token");
		
		        Intent intent = new Intent(this, RegistrationIntentService.class);
		        startService(intent);
		    }
		};


3. Adicione outra classe nova ao projeto chamado `RegistrationIntentService`. Essa será a implementação de nosso `IntentService` que manipulará a [atualização do token GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) e o [registro com o hub de notificação](notification-hubs-registration-management.md).

	Use o código a seguir para essa classe.

		import android.app.IntentService;
		import android.content.Intent;
		import android.content.SharedPreferences;
		import android.preference.PreferenceManager;
		import android.util.Log;
		
		import com.google.android.gms.gcm.GoogleCloudMessaging;
		import com.google.android.gms.iid.InstanceID;
		import com.microsoft.windowsazure.messaging.NotificationHub;
		
		public class RegistrationIntentService extends IntentService {
		
		    private static final String TAG = "RegIntentService";
		
		    private NotificationHub hub;
		
		    public RegistrationIntentService() {
		        super(TAG);
		    }
		
		    @Override
		    protected void onHandleIntent(Intent intent) {		
		        SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
		        String resultString = null;
		        String regID = null;
		
		        try {
		            InstanceID instanceID = InstanceID.getInstance(this);
		            String token = instanceID.getToken(NotificationSettings.SenderId,
		                    GoogleCloudMessaging.INSTANCE_ID_SCOPE);		
		            Log.i(TAG, "Got GCM Registration Token: " + token);
		
		            // Storing the registration id that indicates whether the generated token has been
		            // sent to your server. If it is not stored, send the token to your server,
		            // otherwise your server should have already received the token.
		            if ((regID=sharedPreferences.getString("registrationID", null)) == null) {		
		                NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
		                        NotificationSettings.HubListenConnectionString, this);
		                Log.i(TAG, "Attempting to register with NH using token : " + token);
		                regID = hub.register(token).getRegistrationId();
		                resultString = "Registered Successfully - RegId : " + regID;
		                Log.i(TAG, resultString);		
		                sharedPreferences.edit().putString("registrationID", regID ).apply();
		            } else {
		                resultString = "Previously Registered Successfully - RegId : " + regID;
		            }
		        } catch (Exception e) {
		            Log.e(TAG, resultString="Failed to complete token refresh", e);
		            // If an exception happens while fetching the new token or updating our registration data
		            // on a third-party server, this ensures that we'll attempt the update at a later time.
		        }
		
		        // Notify UI that registration has completed.
		        MainActivity.mainActivity.ToastNotify(resultString);
		    }
		}


		
4. Em sua classe `MainActivity`, adicione as seguintes declarações `import` acima da declaração de classe.

		import com.google.android.gms.common.ConnectionResult;
		import com.google.android.gms.common.GoogleApiAvailability;
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;
		import android.util.Log;
		import android.widget.TextView;
		import android.widget.Toast;

5. Adicione os seguintes membros privados na parte superior da classe. Usaremos eles para [verificar a disponibilidade do Google Play Services, conforme recomendado pela Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

	    public static MainActivity mainActivity;
		private GoogleCloudMessaging gcm;
	    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
	    private static Boolean isVisible = false;

6. Em sua classe `MainActivity`, adicione o seguinte método à disponibilidade do Google Play Services.

	    /**
	     * Check the device to make sure it has the Google Play Services APK. If
	     * it doesn't, display a dialog that allows users to download the APK from
	     * the Google Play Store or enable it in the device's system settings.
	     */
	    private boolean checkPlayServices() {
	        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
	        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
	        if (resultCode != ConnectionResult.SUCCESS) {
	            if (apiAvailability.isUserResolvableError(resultCode)) {
	                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
	                        .show();
	            } else {
	                Log.i(TAG, "This device is not supported by Google Play Services.");
	                ToastNotify("This device is not supported by Google Play Services.");
	                finish();
	            }
	            return false;
	        }
	        return true;
	    }


7. Em sua classe `MainActivity`, adicione o seguinte código que verificará o Google Play Services antes de chamar o `IntentService` a fim de obter seu token de registro GCM e registrar com o hub de notificação.

	    public void registerWithNotificationHubs()
	    {
	        Log.i(TAG, " Registering with Notification Hubs");
	
	        if (checkPlayServices()) {
	            // Start IntentService to register this application with GCM.
	            Intent intent = new Intent(this, RegistrationIntentService.class);
	            startService(intent);
	        }
	    }


8. No método `OnCreate` da classe `MainActivity`, adicione o código a seguir para começar o processo de registro quando a atividade for criada.

	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	
	        mainActivity = this;
	        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
	        registerWithNotificationHubs();
	    }


9. Adicione estes outros métodos ao `MainActivity` para verificar o estado do aplicativo e o status do relatório em seu aplicativo.

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
	                    TextView helloText = (TextView) findViewById(R.id.text_hello);
	                    helloText.setText(notificationMessage);
	                }
	            });
	    }
	}

10. O método `ToastNotify` usa o controle de *"Hello World"*, `TextView`, para reportar de forma consistente o status e as notificações no aplicativo. Em seu layout activity\_main.xml, adicione a seguinte identificação para esse controle.

        android:id="@+id/text_hello"

11. Em seguida, adicionaremos uma subclasse para o receptor que definimos em AndroidManifest.xml. Adicione outra classe nova ao projeto chamado `MyHandler`.

12. Adicione as seguintes instruções de importação na parte superior de `MyHandler.java`:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;


13. Atualize a declaração de classe como se segue para tornar `MyHandler` uma subclasse de `com.microsoft.windowsazure.notifications.NotificationsHandler`, como mostrado abaixo.

		public class MyHandler extends NotificationsHandler {


14. Adicione o código a seguir à classe `MyHandler`.

	Esse código substitui o método `OnReceive`, de forma que o manipulador exiba um pop-up de Notificação do Sistema para mostrar as notificações recebidas. O manipulador também envia a notificação por push ao gerenciador de notificações do Android usando o método `sendNotification()`.

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

15. No Android Studio, na barra de menus, clique em **Compilar** -> **Recompilar Projeto** para garantir que não haja erros presentes no código.

##Como enviar notificações por push

Você pode testar o recebimento de notificações por push no aplicativo enviando-as por meio do [Portal do Azure] - procure a seção **Solução de problemas** na folha do hub, conforme mostrado abaixo.

![Hubs de notificação do Azure - Testar Enviar](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## (Opcional) Enviar notificações por push diretamente do aplicativo

Na maioria dos casos de teste, convém ser capaz de enviar notificações por push diretamente do aplicativo que você está desenvolvendo. Esta seção explica como implementar esse cenário adequadamente.

1. Na Exibição de Projeto do Android Studio, expanda **App** > **src** > **main** > **res** > **layout**. Abra o arquivo de layout `activity_main.xml` e clique na guia **Texto** para atualizar o conteúdo do texto do arquivo. Atualize-o com o código abaixo, que adiciona novos controles `Button` e `EditText` para o envio de mensagens de notificação por push ao hub de notificação. Adicione este código à parte inferior imediatamente antes de `</RelativeLayout>`.

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

2. Adicione esta linha ao arquivo `build.gradle` em `android`

		useLibrary 'org.apache.http.legacy'

3. Na Exibição de Projeto do Android Studio, expanda **App** > **src** > **main** > **res** > **values**. Abra o arquivo `strings.xml` e adicione os valores de cadeia de caracteres referenciados pelos novos controles `Button` e `EditText`. Adicione-os à parte inferior do arquivo imediatamente antes de `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


4. No arquivo `MainActivity.java`, adicione as instruções `import` a seguir acima da classe `MainActivity`.

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


5. No arquivo `MainActivity.java`, adicione os membros a seguir na parte superior da classe `MainActivity`.

	Atualize `HubFullAccess` com a cadeia de conexão **DefaultFullSharedAccessSignature** para o hub. Para copiar essa cadeia de conexão do [Portal do Azure], clique em **Políticas de Acesso** na folha **Configurações** do hub de notificação.

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;
		private String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

6. A atividade contém o nome do hub e a cadeia de conexão completa de acesso compartilhado para o hub. Você deve criar um token SaS (Software Access Signature) para autenticar uma solicitação POST para o envio de mensagens para o seu hub de notificação. Isso é feito analisando os principais dados da cadeia de conexão e criando o token SaS, como mencionado na referência da API REST [Conceitos comuns](http://msdn.microsoft.com/library/azure/dn495627.aspx) .

	Em `MainActivity.java`, adicione o método a seguir à classe `MainActivity` para analisar a cadeia de conexão.

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

7. Em `MainActivity.java`, adicione o método a seguir à classe `MainActivity` para criar um token de autenticação SaS.

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


8. Em `MainActivity.java`, adicione o método a seguir à classe `MainActivity` para manipular o clique no botão **Enviar Notificação** e enviar a mensagem de notificação por push ao hub usando a API REST interna.

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



##Testando seu aplicativo

####Notificações por push no emulador

Para testar notificações por push em um emulador, verifique se a imagem de emulador dá suporte ao nível de API do Google que você escolheu para o aplicativo. Se a imagem não der suporte às APIs nativas do Google, você receberá a exceção **SERVICE\_NOT\_AVAILABLE**.

Além disso, verifique se você adicionou a conta do Google ao emulador em execução em **Configurações** > **Contas**. Caso contrário, suas tentativas de se registrar no GCM podem resultar na exceção **AUTHENTICATION\_FAILED**.

####Executando o aplicativo

1. Execute o aplicativo e observe que a ID de registro é relatada para um registro bem-sucedido.

   	![Como testar no Android - registro de canal][18]

2. Insira uma mensagem de notificação a ser enviada para todos os dispositivos Android registrados no hub.

   	![Como testar no Android - envio de uma mensagem][19]

3. Pressione **Enviar Notificação**. Todos os dispositivos que tiverem o aplicativo em execução mostrarão uma instância `AlertDialog` com a mensagem de notificação por push. Os dispositivos que não tiverem o aplicativo em execução, mas tiverem sido registrados anteriormente para notificações por push, receberão uma notificação no Gerenciador de Notificação do Android. Para exibi-las, passe o dedo do canto superior esquerdo para baixo.

   	![Como testar no Android - notificações][21]

##Próximas etapas

Recomendamos o tutorial [Usar os Hubs de Notificação para enviar notificações por push aos usuários] como a próxima etapa. Ele mostra como enviar notificações de um back-end do ASP.NET usando marcas para direcionar usuários específicos.

Para segmentar os usuários por grupos de interesse, confira o tutorial [Usar Hubs de Notificação para enviar notícias mais recentes].

Para obter informações gerais sobre os Hubs de Notificação, confira nossas [Diretrizes dos Hubs de Notificação].

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
[Azure Classic Portal]: https://manage.windowsazure.com/
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Usar os Hubs de Notificação para enviar notificações por push aos usuários]: notification-hubs-aspnet-backend-android-notify-users.md
[Usar Hubs de Notificação para enviar notícias mais recentes]: notification-hubs-aspnet-backend-android-breaking-news.md
[Portal do Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0413_2016-->