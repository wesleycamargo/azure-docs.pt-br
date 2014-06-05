<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Introdução ao Push (JS)" pageTitle="Introdução às notificações por push (JavaScript do Android) | Mobile Dev Center" metaKeywords="" description="Saiba como usar os Serviços Móveis do Azure para enviar notificações por push ao aplicativo JavaScript do Android" metaCanonical="http://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Introdução às notificações por push nos Serviços Móveis" authors="ricksal"  solutions="" writer="ricksal" manager="" editor=""  />


# <a name="getting-started-with-push"> </a>Introdução às notificações por push nos Serviços Móveis


<div class="dev-center-tutorial-selector sublanding">
	<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="C# da Windows Store">C# da Windows Store</a>
	<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="JavaScript da Windows Store">JavaScript da Windows Store</a>
	<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a>
	<a href="/pt-br/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
	<a href="/pt-br/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android" class="current">Android</a>
</div>

<!--
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="Back-end do .NET">Back-end do .NET</a> |  <a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-push/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>		
-->

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações por push a um aplicativo Android. Neste tutorial você adiciona notificações por push usando o serviço de Mensagens de Nuvem do Google (GCM) para o projeto quickstart. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.

[WACOM.NOTE]Este tutorial demonstra a integração dos Serviços Móveis com os Hubs de Notificação que, atualmente, estão em modo de visualização. Por padrão, o envio de notificações por push usando os Hubs de Notificação não está habilitado em um back-end do JavaScript.  Depois que o novo Hub de Notificação for criado, o processo de integração não poderá ser revertido. As notificações por push para iOS só estão disponíveis atualmente usando o suporte ao envio por push padrão descrito [nesta versão do tópico](/pt-br/documentation/articles/mobile-services-android-get-started-push/).

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Habilitar a mensagem em nuvem do Google](#register)
2. [Configurar os Serviços Móveis](#configure)
3. [Adicionar notificações de push para seu aplicativo](#add-push)
4. [Atualizar scripts para enviar notificações por push](#update-scripts)
5. [Inserir dados para receber notificações](#test)

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis] ou a [Introdução aos dados] para conectar seu projeto ao serviço móvel.  

##<a id="register"></a>Habilitar o Google Cloud Messaging


[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Em seguida, você usará esse valor de chave de API para ativar os Serviços Móveis para autenticar com o GCM e enviar notificações de push em nome do seu aplicativo.

##<a id="configure"></a>Configurar Serviços Móveis para enviar solicitações de push

1. Faça logon no [Portal de Gerenciamento do Windows Azure], clique em **Serviços Móveis** e, em seguida, clique em seu aplicativo.

   	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2. Clique na guia **Enviar por Push**, clique em **Habilitar push aprimorado** e clique em **Sim** para aceitar a alteração na configuração.


	![](./media/mobile-services-android-get-started-push/mobile-enable-enhanced-push.png)

	![](../includes/media/mobile-services-javascript-backend-register-windows-store-app/mobile-enable-enhanced-push.png)


	Isso atualiza a configuração do seu serviço móvel para usar a funcionalidade de notificação por push aprimorada fornecida pelos Hubs de Notificação. O uso de alguns Hubs de Notificação é fornecido gratuitamente com o serviço móvel pago. Para obter mais informações, consulte [Detalhes de preços dos Serviços Móveis](http://go.microsoft.com/fwlink/p/?LinkID=311786).

    <div class="dev-callout"><b>Importante</b>
	<p>Essa operação redefine suas credenciais de envio por push e altera o comportamento dos métodos de envio por push em seus scripts. Essas alterações não podem ser revertidas. Não use esse método para adicionar um hub de notificação para um serviço móvel em produção. Para obter orientação sobre como habilitar as notificações por push aprimoradas em um serviço móvel em produção, consulte <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">esta orientação</a>.</p>
    </div>

3. Digite o valor da **Chave da API** obtido do GCM no procedimento anterior e clique em **Salvar**.



   	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

    <div class="dev-callout"><b>Importante</b>
	<p>Quando você define suas credenciais do GCM para notificações por push aprimoradas na guia Enviar por Push no portal, elas são compartilhadas com os Hubs de Notificação para configurar o hub de notificação com seu aplicativo.</p>
    </div>


O serviço móvel e seu aplicativo agora estão configurados para trabalhar com os GCM e os Hubs de Notificação.

##<a id="add-push"></a>Adicionar notificações de push para seu aplicativo

###Verifique a versão do SDK do Android

[WACOM.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]

A próxima etapa é instalar os serviços do Google Play. A mensagens de nuvem do Google tem alguns requisitos mínimos de nível do API para desenvolvimento e teste, o que a propriedade **minSdkVersion** no manifesto deve estar de acordo. 

Se estiver testando com um dispositivo mais antigo, consulte [Configurar o SDK do Google Play Services] para determinar o mínimo que pode ser definido para esse valor e defina-o de maneira apropriada.

###Adicionar Serviços do Google Play ao projeto

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Adicionar código

1. Abra o arquivo `AndroidManifest.xml`. No código nas duas próximas etapas, substitua _`**my_app_package**`_ pelo nome do pacote do aplicativo de seu projeto, que é o valor do atributo `package` da marca `manifest`. 

2. Adicione as seguintes novas permissões depois do elemento `uses-permission` existente:

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


4. Baixe e descompacte o [SDK dos Serviços Móveis do Android], abra a pasta **notifications**, copie o arquivo **notifications-1.0.1.jar** na pasta *libs* do seu projeto do Eclipse e atualize a pasta *libs*.

    <div class="dev-callout"><b>Observação</b>
	<p>Os números no fim do nome do arquivo podem ser alterados em versões subsequentes do SDK.</p>
    </div>

5.  Abra o arquivo *ToDoItemActivity.java* e adicione a seguinte instrução de importação:

		import com.microsoft.windowsazure.notifications.NotificationsManager;

6. Adicione a seguinte variável privada à classe, onde _`<PROJECT_NUMBER>`_ é o Número do Projeto atribuído pelo Google para seu aplicativo no procedimento anterior:

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

7. No método **onCreate**, antes que a instância do MobileServiceClient seja criada, adicione este código que registra o Manipulador de Notificações para o dispositivo:

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

	Posteriormente, definiremos o MyHandler.class referenciado neste código.
	

8. No Gerenciador de Pacotes, clique com o botão direito do mouse no pacote (no nó `src`), em **Novo** e em **Classe**.

9. Em **Nome**, digite `MyHandler`, em **Superclasse**, digite `com.microsoft.windowsazure.notifications.NotificationsHandler`, e, em seguida, clique em **Concluir**

	![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)

	Isto criará a nova classe MyHandler.

10. Adicione a seguinte instrução de importação:

		import android.content.Context;
		
11. Em seguida, adicione este código à classe:

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
		Context ctx;


12. Adicione o seguinte código para substituir o método **onRegistered**: que registra o dispositivo com o Hub de Notificação do serviço móvel.

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


13. Adicione o seguinte código para substituir o método **onReceive**, que faz com que a notificação seja exibida quando é recebida.

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


##<a id="update-scripts"></a>Atualizar o script de inserção registrados no Portal de Gerenciamento

1. No Portal de Gerenciamento, clique na guia **Dados** e na tabela **TodoItem**. 

   	![](./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png)

2. Em **todoitem**, clique na guia **Script** e selecione **Inserir**.
   
  	![](./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png)

   	Isso exibirá a função invocada quando ocorrer uma inserção na tabela **TodoItem**.

3. Substitua a função de inserção com o seguinte código e clique em **Salvar**:

		function insert(item, user, request) {
		// Define a payload for the Google Cloud Messaging toast notification.
		var payload = 
		    '{"data":{"message" : "Hello from Mobile Services!"}}';
		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		        push.gcm.send(null, payload, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse, payload);
		                request.respond();
		                },              
		            error: function (pushResponse) {
		                console.log("Error Sending push:", pushResponse);
		                request.respond(500, { error: pushResponse });
		                }
		            });
		        },
		    error: function(err) {
		        console.log("request.execute error", err)
		        request.respond();
		    }
		  });
		}

   	Isso registra um novo script de inserção, que usa o [objeto gcm] para enviar uma notificação por push a todos os dispositivos registrados depois que a inserção for bem-sucedida. 

##<a id="test"></a>Testar notificações de push no seu aplicativo

Você pode testar o aplicativo anexando um telefone Android com um cabo USB diretamente ou usando um dispositivo virtual no emulador.

###Configurando o emulador para teste

Quando você executa o aplicativo no emulador, certifique-se de usar um Android Virtual Device (AVD) que oferece suporte a APIs do Google.

1. Reinicie o Eclipse e, em seguida, no Gerenciador de Pacotes, clique com botão direito do mouse no projeto, clique em **Propriedades**, em **Android**, marque **APIs do Google** e clique em **OK**.

	![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

  	Isso tem como alvo o projeto para APIs do Google.

2. Na **Janela**, selecione **Android Virtual Device Manager**, selecione o dispositivo e clique em **Editar**.

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

3. Selecione **APIs do Google** em **Destino** e clique em OK.

   	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	Destina-se ao AVD para usar APIs do Google.

###Executando o teste

1. No menu **Executar** no Eclipse, clique em **Executar** para iniciar o aplicativo.

2. No aplicativo, digite um texto significativo, como _Uma nova tarefa de Serviços Móveis_ e clique no botão **Adicionar**.

  	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

3. Passe o dedo para baixo da parte superior da tela para abrir a Central de Notificação do dispositivo para ver a notificação.


Este tutorial foi concluído com êxito.


## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstra recurso básico de notificação por push fornecido pelos Serviços Móveis. Se seu aplicativo requer recursos mais avançados, como o envio de notificações em várias plataformas, roteamento baseado em assinatura ou volumes muito grandes, convém usar os Hubs de Notificação do Windows Azure com seu serviço móvel. Para obter mais informações, consulte um dos seguintes tópicos de Hubs de Notificação:

+ [Introdução aos Hubs de Notificação]
  <br/>Saiba como utilizar os Hubs de Notificação em seu aplicativo Android.

+ [Enviar notificações para assinantes]
	<br/>Saiba como os usuários podem registrar e receber notificações por push para categorias de interesse.

+ [Enviar notificações para usuários]
	<br/>Saiba como enviar as notificações por push de um Serviço Móvel para usuários específicos em qualquer dispositivo.

+ [Enviar notificações entre plataformas aos usuários]
	<br/>Saiba usar modelos para enviar notificações por push de um Serviço Móvel, sem precisar colocar cargas específicas de plataforma no seu back-end.

Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários do seu aplicativo com uma conta do Windows.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como registrar e usar scripts de servidor.

* [Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com HTML e JavaScript.  


<!-- Anchors. -->
[Registrar seu aplicativo para as notificações por push e configurar os Serviços Móveis]: #register
[Atualizar o código de notificação por push gerado]: #update-scripts
[Inserir dados para receber notificações]: #test
[Próximas etapas]:#next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Enviar uma página de aplicativo:]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-js/
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-js
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-js
[Notificações por push para usuários de aplicativos]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-js
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-push-js
[Configurar o SDK do Google Play Services]: http://go.microsoft.com/fwlink/?LinkId=389801
[Portal de Gerenciamento do Windows Azure]: https://manage.windowsazure.com/
[Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-html-js-client/
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introdução aos Hubs de Notificação]: /pt-br/manage/services/notification-hubs/getting-started-windows-dotnet/
[O que são Hubs de Notificação?]: /pt-br/develop/net/how-to-guides/service-bus-notification-hubs/
[Enviar notificações para assinantes]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet/
[Enviar notificações para usuários]: /pt-br/manage/services/notification-hubs/notify-users/
[Enviar notificações entre plataformas aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[objeto gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645

