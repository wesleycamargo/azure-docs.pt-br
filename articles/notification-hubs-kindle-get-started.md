<properties urlDisplayName="Get Started" pageTitle="Introdução aos Hubs de Notificação do Azure" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-kindle" ms.devlang="Java" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

# Introdução aos Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/pt-br/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/pt-br/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/pt-br/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle" class="current">Kindle</a><a href="/pt-br/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Este tópico mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo Kindle.  
Neste tutorial, você cria um aplicativo Kindle em branco que recebe notificações por push usando o Google Cloud Messaging (GCM).

Este tutorial exige o seguinte:

+ O SDK do Android (pressupõe-se que você usará o Eclipse), que pode ser baixado <a href="http://go.microsoft.com/fwlink/?LinkId=389797">aqui</a>.
+ Siga as etapas <a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">aqui</a> para configurar o ambiente de desenvolvimento para Kindle.

##Adicionar um novo aplicativo ao portal do desenvolvedor

1. Primeiro, crie um aplicativo no [portal do desenvolvedor].

	![][0]

2. Copie a **Chave do Aplicativo**.

	![][1]

3. No portal, clique no nome do aplicativo e, em seguida, clique na guia **Mensagens de Dispositivos**.

	![][2]

4. Clique em **Criar um Novo Perfil de Segurança** e, em seguida, crie um novo perfil de segurança (por exemplo, **Perfil de segurança TestAdm**). Em seguida, clique em **Salvar**.

	![][3]

5. Clique em "Perfis de Segurança" para exibir o perfil de segurança que você acabou de criar. Copie os valores de **ID do Cliente** e **Segredo do Cliente** para uso posterior.

	![][4]

## Criar uma chave para a API

1. Abra uma prompt de comando com privilégios de administrador.
2. Navegue até a pasta Android SDK.
3. Digite o seguinte comando:

    	keytool -list -v -alias androiddebugkey -keystore ./debug.keystore

	![][5]

4.  Para a senha do **keystore**, digite **android**.

5.  Copie a impressão digital **MD5**.
6.  De volta no portal do desenvolvedor, na guia **Mensagens**, clique em **Android/Kindle** e insira o nome do pacote para seu aplicativo (por exemplo, **com.sample.notificationhubtest**), o valor de **MD5** e, em seguida, clique em **Gerar Chave de API**.

## Adicionar credenciais ao hub

No portal, adicione o segredo do cliente e a id do cliente à guia **Configurar** de seu hub de notificação.

## Configurar o aplicativo

<div class="dev-callout"><b>Observação</b>
    <p>Ao criar um aplicativo, use pelo menos API de nível 17.</p>
    </div>

Adicione as bibliotecas do ADM ao seu projeto Eclipse.

1. Para obter a biblioteca do ADM, [baixe o SDK]. Extraia o arquivo zip do SDK.
2. No Eclipse, clique com o botão direito do mouse em seu projeto e clique em **Propriedades**. Selecione **Caminho de Compilação de Java** à esquerda e selecione a guia **Bibliotecas  **na parte superior. Clique em **Adicionar Jar Externo** e selecione o arquivo `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` da pasta na qual você extraiu o SDK da Amazon.
3. Baixe o SDK do Android NotificationHubs (link).
4. Descompacte o pacote e, em seguida, arraste o arquivo `notification-hubs-sdk.jar` para a pasta `libs ` no Eclipse.

Edite seu manifesto de aplicativo para dar suporte ao ADM:

1. Adicione o namespace Amazon no elemento raiz do manifesto:


		xmlns:amazon="http://schemas.amazon.com/apk/res/android"

2. Adicione permissões como o primeiro elemento sob o elemento manifest. Substitua **[YOUR PACKAGE NAME]** pelo pacote usado para criar seu aplicativo. 

		<permission
	     android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
	     android:protectionLevel="signature" />

		<uses-permission android:name="android.permission.INTERNET"/>

		<uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />
 
		<!-- This permission allows your app access to receive push notifications
		from ADM. -->
		<uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />
 
		<!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
		<uses-permission android:name="android.permission.WAKE_LOCK" />

3. Insira o seguinte elemento como o primeiro filho do elemento application. Lembre-se de substituir **[YOUR SERVICE NAME]** pelo nome do seu manipulador de mensagens do ADM que você criará na próxima seção (incluindo o pacote) e substitua **[YOUR PACKAGE NAME]** pelo nome do pacote com o qual você criou o seu aplicativo.

		<amazon:enable-feature
		      android:name="com.amazon.device.messaging"
		             android:required="true"/>
		<service
		    android:name="[YOUR SERVICE NAME]"
		    android:exported="false" />
		 
		<receiver
		    android:name="[YOUR SERVICE NAME]$Receiver"
		 
		    <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
		    android:permission="com.amazon.device.messaging.permission.SEND" >
		 
		    <!-- To interact with ADM, your app must listen for the following intents. -->
		    <intent-filter>
		  <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
		  <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
		 
		  <!-- Replace the name in the category tag with your app's package name. -->
		  <category android:name="[YOUR PACKAGE NAME]" />
		    </intent-filter>
		</receiver>

## Crie o manipulador de mensagens do ADM:

1. Crie uma nova classe que herda de `com.amazon.device.messaging.ADMMessageHandlerBase` e nomeie-a `MyADMMessageHandler`, conforme mostrado na figura a seguir:

	![][6]

2. Adicione as seguintes declarações de `importação`:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.support.v4.app.NotificationCompat;
		import com.amazon.device.messaging.ADMMessageReceiver;
		import com.microsoft.windowsazure.messaging.NotificationHub

3. Adicione o seguinte código à classe criada. Lembre-se de substituir o nome e cadeia de conexão do hub (escuta):

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
      	private static NotificationHub hub;
		public static NotificationHub getNotificationHub(Context context) {
			Log.v("com.wa.hellokindlefire", "getNotificationHub");
			if (hub == null) {
				hub = new NotificationHub("[hub name]", "[listen connection string]", context);
			}
			return hub;
		}

		public MyADMMessageHandler() {
				super("MyADMMessageHandler");
			}
	
			public static class Receiver extends ADMMessageReceiver
    		{
        		public Receiver()
        		{
            		super(MyADMMessageHandler.class);
        		}
    		}
	
			private void sendNotification(String msg) {
				Context ctx = getApplicationContext();
		
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

4. Adicione o seguinte código ao método `OnMessage()`:
	
		String nhMessage = intent.getExtras().getString("msg");
		sendNotification(nhMessage);
 
5. Adicione o código a seguir ao método `OnRegistered`:

			try {
		getNotificationHub(getApplicationContext()).register(registrationId);
			} catch (Exception e) {
		Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
			}

6.	Adicione o código a seguir ao método `OnUnregistered`:

			try {
				getNotificationHub(getApplicationContext()).unregister();
			} catch (Exception e) {
				Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
			}

7. Em seguida, no método `MainActivity`, adicione a seguinte instrução de importação:

		import com.amazon.device.messaging.ADM;				

8. Agora, adicione o código a seguir no final do método `OnCreate`:

		final ADM adm = new ADM(this);
		if (adm.getRegistrationId() == null)
		{
		   adm.startRegister();
		} else {
			new AsyncTask() {
			      @Override
			      protected Object doInBackground(Object... params) {
			         try {			        	 MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
			         } catch (Exception e) {
			        	 Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
			        	 return e;
			         }
			         return null;
			     }
			   }.execute(null, null, null);
		}

## Adicionar a APIKey ao seu aplicativo

1. No Eclipse, crie um novo arquivo chamado **api_key.txt** no diretório assets de seu projeto.
2. Abra o arquivo e copie **Chave da API** gerada no portal do desenvolvedor da Amazon.

## Executar o aplicativo

1. Inicie o emulador.
2. No emulador, passe o dedo de cima para baixo e clique em **Configurações**, em seguida, clique em **Minha conta** e registre-se com uma conta válida da Amazon.
3. No Eclipse, execute o aplicativo.

<div class="dev-callout"><b>Observação</b>
    <p>Se ocorrer um problema, verifique a hora do emulador (ou dispositivo). O valor temporal deve ser preciso. Para alterar a hora do emulador do Kindle, você pode executar o seguinte comando no diretório de ferramentas da plataforma do SDK do Android:  </p>
</div>
		adb shell  date -s "yyyymmdd.hhmmss"

## Enviar uma mensagem

Para enviar uma mensagem usando o .NET:

		static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
        }

![][7]

<!-- URLs. -->
[portal do desenvolvedor]: https://developer.amazon.com/home.html
[baixar o SDK]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
