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
	ms.devlang="java" 
	ms.topic="article" 
	ms.tgt_pltfrm="mobile-baidu" 
	ms.workload="mobile" 
	ms.date="03/16/2015" 
	ms.author="wesmc"/>

# Introdução aos Hubs de Notificação

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Visão geral

O envio de nuvem Baidu é um serviço na nuvem chinês que você pode usar para enviar notificações por push para dispositivos móveis.  Esse serviço é particularmente útil na China onde entregar notificações por push para o Android é complexo devido à presença de lojas de aplicativos diferentes, serviços de envio e a disponibilidade de dispositivos com Android que não costumam estar conectadas ao GCM (Google Cloud Messaging). 

##Pré-requisitos

Este tutorial exige o seguinte:

+ O SDK do Android (pressupõe-se que você esteja usando o Eclipse), que pode ser baixado <a href="http://go.microsoft.com/fwlink/?LinkId=389797">aqui</a>
+ [SDK para Android de Serviços Móveis]
+ [SDK do Android Push Baidu]

>[AZURE.NOTE] Para concluir este tutorial, você precisa ter uma conta ativa do Azure.  Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos.  Para obter detalhes, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).


##Criar uma conta de Baidu

Para usar o Baidu, você deve criar uma conta.  Se você já tiver um, faça o logon para o [Portal do Baidu] com sua conta do Baidu e pule para a próxima etapa. Caso contrário, consulte as instruções abaixo sobre como criar uma nova conta do Baidu.  

1. Vá para [Portal do Baidu] e clique no link 登录 (logon).   Clique em立即注册 para iniciar um novo processo de registro de conta. 

   	![][1]

2. Insira os detalhes necessários - telefone, endereço de email, senha e o código de verificação e clique em Inscrever-se.

   	![][2]

3. Você receberá um email para o endereço de email inserido com um link para ativar sua conta Baidu. 

   	![][3]

4. Faça o logon em sua conta de email, abra o email de ativação do Baidu e clique no link de ativação para ativar sua conta Baidu. 

   	![][4]

Depois que você tiver uma conta Baidu ativada, faça o logon para o [Portal do Baidu] com a sua conta. 

##Registrar-se como desenvolvedor Baidu

1. Depois de fazer logon no [portal do Baidu], clique em **更多>> (mais)**.

  	![][5]

2. Role para baixo até a seção **站长与开发者服务 (Serviços do Desenvolvedor e Webmaster)** e clique em **百度开放云平台 (Plataforma de nuvem de abertura do Baidu)**. 

  	![][6]

3. Na página seguinte, clique em **开发者服务 (Serviços do Desenvolvedor)** no canto superior direito. 

  	![][7]

4. Na página seguinte, clique em **注册开发者(Desenvolvedores registrados)** no menu no canto superior direito. 

  	![][8]

5. Insira seu nome, a descrição e o número do celular para receber uma mensagem de texto de verificação e, em seguida, clique em **送验证码(Enviar o código de verificação)**.  Observe que para números de telefone internacionais, você precisa colocar o código do país entre parênteses, por exemplo, para um número dos Estados Unidos, ele será **(1)1234567890**.

  	![][9]

6. Em seguida, você deve receber uma mensagem de texto com um número de verificação, conforme mostrado no exemplo a seguir:

  	![][10] 

7. Insira o número de verificação da mensagem no **验证码 (Código de confirmação)**. 

8. Conclua o registro de desenvolvedor aceitando o contrato do Baidu e clicando em **提交 (Enviar)**.  Você verá a seguinte página na conclusão bem-sucedida do registro:

  	![][11] 

##Criar um projeto de envio na nuvem Baidu

Quando você cria um projeto de envio na nuvem Baidu, você recebe sua ID de aplicativo, chave API e a chave secreta.

1. Depois de fazer logon no [portal do Baidu], clique em **更多>> (mais)**.

  	![][5]

2. Role para baixo até a seção **站长与开发者服务 (Serviços do Desenvolvedor e Webmaster)** e clique em **百度开放云平台 (Plataforma de nuvem de abertura do Baidu)**. 

  	![][6]

3. Na página seguinte, clique em **开发者服务 (Serviços do Desenvolvedor)** no canto superior direito. 

  	![][7]

4. Na página seguinte, clique em **云推送 (Push de Nuvem)** na seção **云服务 (Serviços de Nuvem)**. 

  	![][12]

5. Se você for um desenvolvedor registrado, verá **管理控制台 (Console de Gerenciamento)** no menu superior.   Clique em **开发者服务管理 (Gerenciamento do Serviço dos Desenvolvedores)**. 

  	![][13]

6. Na página seguinte, clique em **创建工程 (Criar Projeto)**.

  	![][14]

7. Insira um nome de aplicativo e clique em **创建 (Criar)**.

  	![][15]

8. Após a criação bem-sucedida, você verá uma página com o **AppID**, **chave API** e **chave secreta**.  Anote a **chave API** e a **chave secreta** que usaremos mais tarde. 

  	![][16]

9. Configure o projeto para Notificações por Push clicando em **云推送 (Push de Nuvem)** no painel à esquerda. 

  	![][31]

10. Na página seguinte, clique no botão **推送设置 (Configurações de push)**.

	![][32]  

11. Na página de configuração, adicione o nome do pacote que você usará no projeto do Android no campo **应用包名 (Pacote de aplicativo)** e clique em **保存设置 (Salvar)**

	![][33]

Você verá a mensagem**保存成功！(salvo com êxito!)**.

##Configurar seu Hub de Notificação

1. Faça logon no [Portal de Gerenciamento do Azure] e clique em **+NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativo**, em **Barramento de serviço**, depois em **Hub de Notificação** e, em seguida, **Criação Rápida**.
 
3. Forneça um nome para o seu **Hub de notificação**, selecione a **Região** e o **Namespace** onde esse hub de notificação será criado e, em seguida, clique em **Criar um novo Hub de notificação**  

  	![][17]

4. Clique no namespace em que você criou seu hub de notificação e, em seguida, clique em **Hubs de notificação** na parte superior. 

  	![][18]

5. Selecione o Hub de notificação que você criou e clique em **configurar** no menu superior.

  	![][19]

6. Role para baixo até a seção **configurações de notificação baidu** e insira a **chave API** e a **chave secreta** obtidas do console Baidu anteriormente para o seu projeto de envio na nuvem Baidu.  Clique em **Salvar** depois de inserir esses valores. 

  	![][20]

7. Clique na guia **Painel** na parte superior para o Hub de notificação e clique em **Exibir cadeia de conexão**.

  	![][21]

8. Anote o **DefaultListenSharedAccessSignature** e a **DefaultFullSharedAccessSignature** a partir da janela de informações de conexão de acesso. 

    ![][22]

##Conectando o seu aplicativo ao Hub de Notificação

1. No Eclipse ADT, crie um novo projeto Android (Arquivo -> Novo -> Aplicativo Android).

    ![][23]

2. Insira um **nome do aplicativo** e verifique se a versão do **SDK mínimo necessário** está definida para **API 16:  Android 4.1**.

    ![][24]

3. Clique em **Avançar** e continue seguindo o assistente até a janela **Criar atividade**.  Verifique se **atividade em branco** está selecionada e selecione finalmente **Concluir** para criar um novo aplicativo Android. 

    ![][25]

4. Verifique se o **Destino da compilação do projeto** está definido corretamente.

    ![][26]

5. Baixe e descompacte o [SDK dos Serviços Móveis do Android], abra a pasta **notificationhubs**, copie o arquivo **notification-hubs-x.y.jar** na pasta *libs* do seu projeto do Eclipse e atualize a pasta *libs*.

6. Baixe e descompacte [SDK de Android por Push do Baidu], abra a pasta **libs** e copie o arquivo *pushservice-x.y.z* e as pastas *armeabi* e *mips* na pasta **libs** do aplicativo Android. 

7. Abra o **Androidmanifest.xml** de seu projeto Android e adicione as permissões exigidas pelo SDK do Baidu.

	    <uses-permission android:name="android.permission.INTERNET" />
	    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
	    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
	    <uses-permission android:name="android.permission.WRITE_SETTINGS" />
	    <uses-permission android:name="android.permission.VIBRATE" />
	    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
	    <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
	    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
	    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
	    <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
	    <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />

8. Adicione a propriedade *android:name* ao elemento *application* em**AndroidManifest.xml** substituindo *yourprojectname*, por exemplo, **com.example.BaiduTest**.  Certifique-se de que esse nome de projeto corresponde a uma senha configurada no console do Baidu. 

		<application android:name="yourprojectname.DemoApplication"

9. Adicione a seguinte configuração dentro do elemento de aplicativo após o elemento de atividade .MainActivity substituindo *yourprojectname*, por exemplo, **com.example.BaiduTest**:

		<receiver android:name="yourprojectname.MyPushMessageReceiver">
		    <intent-filter>
		        <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
		        <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
		        <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
		    </intent-filter>
		</receiver>
		
		<receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
		    android:process=":bdservice_v1">
		    <intent-filter>
		        <action android:name="android.intent.action.BOOT_COMPLETED" />
		        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
				<action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
		    </intent-filter>
		</receiver>
        
        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>                   
        </receiver>
        
        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>

9. Adicione uma nova classe chamada **ConfigurationSettings.java** ao projeto. 

    ![][28]

    ![][29]

10. Adicione os seguintes códigos a ela:

		public class ConfigurationSettings {
		        public static String API_KEY = "...";
				public static String NotificationHubName = "...";
				public static String NotificationHubConnectionString = "...";
			}
	
	Definir o valor de *API_KEY* com o que você recuperou do projeto de nuvem Baidu anteriormente,*NotificationHubName* com o nome de seu hub de notificação no portal do Azure e *NotificationHubConnectionString* com DefaultListenSharedAccessSignature no portal do Azure. 

11. Adicione uma nova classe chamada **DemoApplication.java** e adicione o seguinte código para ela:

		import com.baidu.frontia.FrontiaApplication;
		
		public class DemoApplication extends FrontiaApplication {
		    @Override
		    public void onCreate() {
		        super.onCreate();
		    }
		}

12. Adicione uma outra nova classe chamada **MyPushMessageReceiver.java** e adicione o código a seguir para ela.  Esta é a classe que manipula as notificações de push recebidas do servidor de envio de Baidu:

		import java.util.List;
		import android.content.Context;
		import android.os.AsyncTask;
		import android.util.Log;
		import com.baidu.frontia.api.FrontiaPushMessageReceiver;
		import com.microsoft.windowsazure.messaging.NotificationHub;
		
		public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
		    /** TAG to Log */
			public static NotificationHub hub = null;
			public static String mChannelId, mUserId;
		    public static final String TAG = MyPushMessageReceiver.class
		            .getSimpleName();
		    
			@Override
		    public void onBind(Context context, int errorCode, String appid,
		            String userId, String channelId, String requestId) {
		        String responseString = "onBind errorCode=" + errorCode + " appid="
		                + appid + " userId=" + userId + " channelId=" + channelId
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		        mChannelId = channelId;
		        mUserId = userId;
		        
		        try {
		       	 if (hub == null) {
		                hub = new NotificationHub(
		                		ConfigurationSettings.NotificationHubName, 
		                		ConfigurationSettings.NotificationHubConnectionString, 
		                		context);
		                Log.i(TAG, "Notification hub initialized");
		            }
		        } catch (Exception e) {
		           Log.e(TAG, e.getMessage());
		        }
		        
		        registerWithNotificationHubs();
			}
		    
		    private void registerWithNotificationHubs() {
		       new AsyncTask<Void, Void, Void>() {
		          @Override
		          protected Void doInBackground(Void... params) {
		             try {
		            	 hub.registerBaidu(mUserId, mChannelId);
		            	 Log.i(TAG, "Registered with Notification Hub - '" 
		     	    			+ ConfigurationSettings.NotificationHubName + "'" 
		     	    			+ " with UserId - '"
		     	    			+ mUserId + "' and Channel Id - '"
		     	    			+ mChannelId + "'");
		             } catch (Exception e) {
		            	 Log.e(TAG, e.getMessage());
		             }
		             return null;
		         }
		       }.execute(null, null, null);
		    }
		    
		    @Override
		    public void onSetTags(Context context, int errorCode,
		            List<String> sucessTags, List<String> failTags, String requestId) {
		        String responseString = "onSetTags errorCode=" + errorCode
		                + " sucessTags=" + sucessTags + " failTags=" + failTags
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onDelTags(Context context, int errorCode,
		            List<String> sucessTags, List<String> failTags, String requestId) {
		        String responseString = "onDelTags errorCode=" + errorCode
		                + " sucessTags=" + sucessTags + " failTags=" + failTags
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onListTags(Context context, int errorCode, List<String> tags,
		            String requestId) {
		        String responseString = "onListTags errorCode=" + errorCode + " tags="
		                + tags;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onUnbind(Context context, int errorCode, String requestId) {
		        String responseString = "onUnbind errorCode=" + errorCode
		                + " requestId = " + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onNotificationClicked(Context context, String title,
		            String description, String customContentString) {
		        String notifyString = "title="" + title + "" description=""
		                + description + "" customContent=" + customContentString;
		        Log.d(TAG, notifyString);
		    }
		
		    @Override
		    public void onMessage(Context context, String message,
		            String customContentString) {
		        String messageString = "message="" + message + "" customContentString=" + customContentString;
		        Log.d(TAG, messageString);
		    }
		}

13. Abra o **MainActivity.java** e adicione o seguinte para o método **onCreate**:

	        PushManager.startWork(getApplicationContext(),
	                PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

e adicione as seguintes instruções de importação no início:
			import com.baidu.android.pushservice.PushConstants;
			import com.baidu.android.pushservice.PushManager;

##Enviar notificações para seu aplicativo

Você pode enviar notificações usando Hubs de Notificação de qualquer back-end que use a nossa <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>.  Neste tutorial, mostramos isso usando um aplicativo de console do .NET. 

1. Crie um novo aplicativo de console do Visual C#:

	![][30]

2. Adicione uma referência ao SDK do Service Bus do Azure com o <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacote NuGet do WindowsAzure.ServiceBus</a>.  No menu principal do Visual Studio, clique em **Ferramentas**, em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**.  Em seguida, na janela do console, digite o seguinte e pressione Enter:

        Install-Package WindowsAzure.ServiceBus

3. Abra o arquivo Program.cs e adicione a seguinte instrução:

        using Microsoft.ServiceBus.Notifications;

4. Em sua classe de  `Program`, adicione o seguinte método e substitua o *DefaultFullSharedAccessSignatureSASConnectionString* e *NotificationHubName* pelos valores que você tem. 

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
			string message = "{"title":"((Notification title))","description":"Hello from Azure"}";
			var result = await hub.SendBaiduNativeNotificationAsync(message);
		}

5. Em seguida, adicione as seguintes linhas em seu método Main:

         SendNotificationAsync();
		 Console.ReadLine();

##Testando seu aplicativo

Para testar este aplicativo com um telefone real, basta conectá-lo ao seu computador com um cabo USB.

Para testar este aplicativo com o emulador:

1. Na barra de ferramentas superior do Eclipse, clique em Executar e selecione seu aplicativo. 

2. Isto carregará seu aplicativo no telefone anexado ou, então ele iniciará o emulador, carregando e executando o aplicativo.

3. O aplicativo recupera o 'userId' e o 'channelId' do serviço de notificação do Baidu Push e registra a si mesmo com o Hub de notificação.
	
4.	Para enviar uma notificação de teste ao usar o aplicativo do console .Net, simplesmente pressione a tecla F5 no Visual Studio para executar o aplicativo e ele enviará uma notificação que aparecerá na área de notificação superior de seu dispositivo ou emulador. 


<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[SDK para Android de Serviços Móveis]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[SDK dos Serviços Móveis do Android]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[SDK do Android Push Baidu]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[SDK de Android por Push do Baidu]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Portal do Baidu]: http://www.baidu.com/




<!--HONumber=49--> 