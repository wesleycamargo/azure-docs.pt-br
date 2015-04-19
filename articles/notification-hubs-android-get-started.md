<properties 
	pageTitle="Introdução aos Hubs de notificação do Azure" 
	description="Saiba como usar os Hubs de notificação do Azure para notificações por push." 
	services="notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="ricksal"/>
# Introdução aos Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/pt-br/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/pt-br/documentation/articles/notification-hubs-android-get-started/" title="Android" class="current">Android</a><a href="/pt-br/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/pt-br/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Este tópico mostra como usar os Hubs de Notificação do Microsoft Azure para enviar notificações por push para um aplicativo Android. 
Neste tutorial, você cria um aplicativo em branco para Android que recebe notificações por push usando o Google Cloud Messaging (GCM). Ao concluir, você poderá transmitir notificações por push a todos os dispositivos que executam seu aplicativo usando o hub de notificação.

O tutorial explica as seguintes etapas básicas para habilitar as notificações por push:

* [Habilitar o Google Cloud Messaging](#register)
* [Configurar seu Hub de notificação](#configure-hub)
* [Conexão do seu aplicativo ao Hub de notificação](#connecting-app)
* [Como enviar notificações para seu aplicativo](#send)
* [Testando seu aplicativo](#run-app)

Este tutorial demonstra o simples cenário de transmissão usando Hubs de Notificação. Certifique-se de seguir o próximo tutorial para aprender a usar hubs de notificação, para atender usuários e grupos de dispositivos específicos. 

Este tutorial exige o seguinte:

+ o SDK do Android (pressupõe-se que você esteja usando o Eclipse), que pode ser baixado <a href="http://go.microsoft.com/fwlink/?LinkId=389797">aqui</a>
+ o [SDK de Serviços Móveis do Android]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre hubs de notificação para aplicativos do Android. 

> [AZURE.NOTE] Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F%20target="_blank").

##<a id="register"></a>Habilitar o Google Cloud Messaging

[AZURE.INCLUDE [Habilitar o GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Em seguida, você usará este valor de chave da API para habilitar seu hub de notificação, fazer a autenticação com o GCM e enviar notificações por push em nome de seu aplicativo.

##<a id="configure-hub"></a>Configurar seu Hub de notificação

1. Faça logon no [Portal de Gerenciamento do Azure] e clique em **+NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativo**, em **Barramento de serviço**, depois em **Hub de Notificação** e, em seguida, **Criação Rápida**.

   	![][7]

3. Digite um nome para o hub de notificação, selecione a região desejada e clique em **Criar um novo Hub de Notificação**.

   	![][8]

4. Clique no namespace que você acabou de criar (geralmente ***nome do hub de notificação*-ns**) e clique em **Configurar** na parte superior.

   	![][9]

5. Clique na guia **Hubs de Notificação** na parte superior e clique no hub de notificação que você acabou de criar.

   	![][10]

6. Clique na guia **Configurar** na parte superior, digite o valor de **Chave da API** que você obteve na etapa anterior e clique em **Salvar**.

   	![][11]

7. Selecione a guia **Painel** na parte superior e, em seguida, clique em **Exibir Cadeia de Conexão**. Anote as duas cadeias de conexão.


Seu hub de notificação agora está configurado para funcionar com o GCM e você tem as cadeias de conexão para registrar seu aplicativo e enviar notificações por push.

##<a id="connecting-app"></a>Conexão do seu aplicativo ao Hub de notificação

###Criar novo projeto Android

1. No Eclipse ADT, crie um novo projeto Android (Arquivo, Novo, Aplicativo Android).

   	![][13]

2. Certifique-se de que o **SDK Mínimo Necessário** esteja definido como  *API 8: Android 2.2 (Froyo)* e que as duas próximas entradas SDK estejam definidas para a versão mais recente. Escolha Avançar e siga o assistente, certificando-se de que a opção **Criar atividade** esteja selecionada para criar uma atividade em branco. Aceite o ícone padrão do Iniciador na caixa seguinte e clique em **Concluir** na última caixa.

   	![][14]

###Inclua o Google Play Services no projeto

[AZURE.INCLUDE [Adicionar Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Incluir código

1. Baixe os Hubs de Notificação do SDK do Android <a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">aqui</a>. Extraia o arquivo .zip e copie o arquivo notificationhubs\notification-hubs-0.1.jar no diretório \libs do seu projeto no Gerenciador de Pacotes.

2. Baixe e descompacte o [SDK de Serviços Móveis do Android], abra a pasta **notifications** copie o arquivo **notifications-1.0.1.jar** na pasta *libs* do seu projeto do Eclipse e atualize a pasta *libs*.

    > [AZURE.NOTE] Os números no fim do nome do arquivo podem ser alterados em versões subsequentes do SDK.

	Agora, configure o aplicativo para obter uma  *registrationId* a partir do GCM e use-o para registrar a instância do aplicativo no hub de notificação.

3. No arquivo AndroidManifest.xml, adicione a seguinte linha logo abaixo do elemento <uses-sdk/>. Certifique-se de substituir `<your package>` pelo pacote selecionado para seu aplicativo na etapa 1 (neste exemplo,`com.yourCompany.wams_notificationhubs` ).

        <uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4. Na classe **MainActivity**, adicione as seguintes instruções.

		import android.os.AsyncTask;	
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;


5. Adicione os seguintes membros privados na parte superior da classe.

	> [AZURE.NOTE] Certifique-se de definir o SENDER_ID ao Número do Projeto obtido anteriormente.

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;

6. No método **OnCreate**, adicione o seguinte código e certifique-se de substituir os espaços reservados pela sua cadeia de conexão com acesso de escuta obtido na etapa anterior e o nome do seu hub de notificação que aparece na parte superior da página do Azure pelo seu hub (**não** a url completa).

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

		gcm = GoogleCloudMessaging.getInstance(this);
        
		String connectionString = "<your listen access connection string>";
		hub = new NotificationHub("<your notification hub name>", connectionString, this);
		
		registerWithNotificationHubs();

7. Em MainActivity.java, crie o seguinte método:

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

8. Você deve incluir seu próprio destinatário, pois o Android não exibe notificações. No arquivo **AndroidManifest.xml**, adicione o seguinte elemento dentro do elemento `<application/>`.

	> [AZURE.NOTE] substitua o espaço reservado pelo nome do pacote.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


9. No Gerenciador de Pacotes, clique com o botão direito no pacote (no nó `src` ), clique em **Novo**, e clique em **Classe**.

10. Em **Nome** digite `MyHandler`, em **Superclasse** digite `com.microsoft.windowsazure.notifications.NotificationsHandler`, depois clique em **Concluir**

	![][6]

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
	

##<a name="send"></a>Como enviar uma notificação para seu aplicativo

Você pode enviar notificações usando Hubs de Notificação de qualquer back-end usando a <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>. Neste tutorial você envia as notificações com um aplicativo de console do .NET. Para um exemplo de como enviar notificações de um back-end do Serviços Móveis do Azure integrado com Hubs de notificação, consulte **Introdução às notificações push no Mobile_Services** ([back-end do .NET](mobile-services-javascript-backend-android-get-started-push.md) | [Back-end do JaveScript](mobile-services-javascript-backend-android-get-started-push.md)).  Para um exemplo de como enviar notificações usando o REST APIs, consulte **Como usar Hubs de Notificação a partir de Java/PHP** ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).

1. No Visual Studio, no menu **Arquivo** selecione **Novo** e depois **Projeto...**, em seguida, em **Visual C#** clique em**Windows** e**Aplicativo do console** e clique em**OK**.  

   	![][20]

	Isso cria um novo projeto de aplicativo de console.

2. No menu **Ferramentas**, clique em **Gerenciador de Pacotes de Biblioteca** e, em seguida, selecione **Console do Gerenciador de Pacotes**. 

	Isso exibe a Console do Gerenciador de Pacotes.

3. Na janela da console, execute o seguinte comando:

        Install-Package WindowsAzure.ServiceBus
    
	Isso adiciona uma referência ao SDK do Barramento de Serviço do Azure com o <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacote NuGet do WindowsAzure.ServiceBus</a>. 

4. Abra o arquivo Program.cs e adicione a seguinte instrução `using`:

        using Microsoft.ServiceBus.Notifications;

5. Na classe **Programa**, adicione o seguinte método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

   	Substitua o espaço reservado de "nome do hub" pelo nome do hub de notificação que aparece no portal, na guia **Hubs de Notificação**. Além disso, substitua o espaço reservado de cadeias de conexão pela cadeia de conexão chamada **DefaultFullSharedAccessSignature** que você obteve na seção "Configurar seu hub de notificação". 

	>[AZURE.NOTE]Certifique-se de usar a cadeia de conexão com acesso **Completo**, não com acesso para **Escutar**. A cadeia de acesso Escutar não tem permissões para enviar notificações.

5. Em seguida, adicione as seguintes linhas no método **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="run-app"></a>Testando seu aplicativo

Antes de testar o aplicativo em um emulador, você precisa completar estas etapas de configuração do emulador (pular se o teste for em um dispositivo físico):

1. Certifique-se de que você esteja usando um Dispositivo Virtual para Android (AVD) que ofereça suporte às APIs do Google.

2. Na **Janela**, clique em **Gerenciador de Dispositivo Virtual para Android**, selecione seu dispositivo e, em seguida, clique em **Editar**.

   	![][18]

3. Selecione **Google APIs** em **Destino** e clique em **OK**.

   	![][19]

4. 	Para receber as notificações por push, você deve configurar uma conta do Google em seu Dispositivo Virtual para Android (no emulador, navegue até <strong>Configurações</strong> e clique em <strong>Adicionar Conta</strong>). Além disso, certifique-se de que o emulador esteja conectado à Internet.


Use as seguintes etapas para executar o aplicativo em um dispositivo ou em um emulador:

1. Na barra de ferramentas superior do Eclipse, clique em **Executar**e, em seguida, selecione seu aplicativo. 
 
	Isso inicia o aplicativo (se estiver usando um emulador) e carrega e executa o aplicativo. O aplicativo recupera o  *registrationId* do GCM e registra com o Hub de Notificação.

3. Pressione a tecla F5 no Visual Studio para executar o aplicativo de console. 

	Uma notificação é enviada a seu aplicativo.  
 
5. Quando um ícone aparecer na área de notificação (esquina superior esquerda), puxe para baixo a gaveta de notificações para visualizar a notificação.  

   	![][21]

## <a name="next-steps"> </a>Próximas etapas

Neste simples exemplo, você envia notificações para todos os seus dispositivos Android. Para selecionar usuários de destinos específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá consultar [Usar Hubs de Notificação para enviar notícias de última hora]. Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação].


<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
[7]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-android-get-started/notification-hub-configure-android.png
[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app.png
[14]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app2.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

<!-- URLs. -->
[SDK de Serviços Móveis do Android]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-android
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-android
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-android
[Notificações por push para usuários do aplicativo]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-android
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-android
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-push-js
[Fazendo referência a um projeto de biblioteca]: http://go.microsoft.com/fwlink/?LinkId=389800
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[objeto wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx

[Usar hubs de notificação para enviar notificações por push aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-aspnet
[Usar Hubs de Notificação para enviar notícias de última hora]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet


<!--HONumber=45--> 
