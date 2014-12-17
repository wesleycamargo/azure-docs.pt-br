<properties linkid="develop-mobile-tutorials-get-started-with-push-android" urlDisplayName="Get Started with Push (Android)" pageTitle="Introdução às notificações por push (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Introdução às notificações por push nos Serviços Móveis (envio herdado)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/develop/mobile/tutorials/get-started-with-push-dotnet" title="Windows Store C#">C# da Windows Store</a>
    <a href="/pt-br/develop/mobile/tutorials/get-started-with-push-js" title="Windows Store JavaScript">JavaScript da Windows Store</a>
    <a href="/pt-br/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a>
    <a href="/pt-br/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a>
    <a href="/pt-br/develop/mobile/tutorials/get-started-with-push-android" title="Android" class="current">Android</a>
<!--    <a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/pt-br/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET backend">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-android-get-started-push/"  title="JavaScript backend" class="current">Back-end do JavaScript</a></div>

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações de push a um aplicativo do Android. Neste tutorial você adiciona notificações de push utilizando o serviço de Mensagens de Nuvem do Google (GCM) ao projeto de início rápido. Ao concluir, seu serviço móvel enviará uma notificação de push sempre que um registro é inserido.

>[WACOM.NOTE]Este tópico dá suporte aos serviços móveis <em>existentes</em> que <em>ainda não foram atualizados</em> para usar a integração dos Hubs de Notificação. Quando você cria um serviço móvel <em>novo</em>, esta funcionalidade integrada é habilitada automaticamente. Para novos serviços móveis, consulte [Introdução às notificações por push](/pt-br/documentation/articles/mobile-services-javascript-backend-android-get-started-push/).
>
>Os Serviços Móveis integram-se aos Hubs de Notificação do Azure para dar suporte à funcionalidade de notificação por push, como modelos, várias plataformas e escala aprimorada. <em>Você deve atualizar seus serviços móveis existentes para usar os Hubs de Notificação quando possível</em>. Assim que você tiver feito a atualização, consulte esta versão da [Introdução às notificações por push](/pt-br/documentation/articles/mobile-services-javascript-backend-android-get-started-push/).

Este tutorial explica as etapas básicas para habilitar as notificações por push:

* [Habilitar o sistema de mensagens em nuvem do Google](#register)
* [Configurar os Serviços Móveis](#configure)
* [Adicionar notificações de push para seu aplicativo](#add-push)
* [Atualizar scripts para enviar notificações por push](#update-scripts)
* [Inserir dados para receber notificações](#test)

Este tutorial exige o seguinte:

+ [SDK para Android de Serviços Móveis]
+ Uma conta ativa do Google

Este tutorial baseia-se no início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis]. 

##<a id="register"></a>Habilitar o sistema de mensagens em nuvem do Google


[WACOM.INCLUDE [Habilite o GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Em seguida, você usará esse valor de chave de API para ativar os Serviços Móveis para autenticar com o GCM e enviar notificações de push em nome do seu aplicativo.

##<a id="configure"></a>Configurar Serviços Móveis para enviar solicitações de push

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e no aplicativo.

   	![][18]

2. Clique na guia **Enviar por Push**, insira o valor da **Chave da API** obtida do GCM no procedimento anterior e clique em **Salvar**.

   	![][19]

Seu serviço móvel agora está configurado para funcionar com o GCM para enviar notificações de push.

##<a id="add-push"></a>Adicionar notificações de push para seu aplicativo


###Inclua o Google Play Services no projeto

[WACOM.INCLUDE [Incluir Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Incluir código

1. Abra o arquivo de projeto **AndroidManifest.xml**. O serviço de Mensagens de Nuvem do Google tem alguns requisitos mínimos de nível do API para desenvolvimento e teste, com os quais a propriedade **minSdkVersion** no manifesto deve estar em conformidade. Consulte [Definir o SDK Google Play Services] para determinar quão baixo você pode definir esse valor, se precisar defini-lo abaixo de 16, porque você está usando um dispositivo mais antigo. Defina a propriedade adequadamente.

2. Verifique se, no elemento `uses-sdk`, o **targetSdkVersion** está definido como o número de uma plataforma SDK que foi instalada (etapa 1). É preferível definir para a versão mais recente disponível. 

3. A marca **uses-sdk** pode ser semelhante à marca abaixo, dependendo das escolhas que você fez nas etapas anteriores:

	    <uses-sdk
	        android:minSdkVersion="17"
	        android:targetSdkVersion="19" />
	
4. No código nas duas próximas etapas, substitua _`**my_app_package**`_ pelo nome do pacote do aplicativo de seu projeto, que é o valor do atributo `package` da marca `manifest`. 

5. Adicione as seguintes novas permissões depois do elemento `uses-permission` existente:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

6. Adicione o código a seguir após o marca de abertura `application`: 

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>



7. Abra o arquivo ToDoItem.java, adicione o seguinte código à classe **TodoItem**:

			@com.google.gson.annotations.SerializedName("handle")
			private String mHandle;
		
			public String getHandle() {
				return mHandle;
			}
		
			public final void setHandle(String handle) {
				mHandle = handle;
			}
		
	This code creates a new property that holds the registration ID.

    <div class="dev-callout"><b>Observação</b>
	<p>Quando o esquema dinâmico estiver ativado no seu serviço móvel, uma nova coluna <strong>handle</strong> é adicionada automaticamente na tabela <strong>TodoItem</strong> quando um novo item que contém essa propriedade é inserido.</p>
    </div>

8. Baixe e descompacte o [SDK dos Serviços Móveis para Android], abra a pasta **notifications**, copie o arquivo **notifications-1.0.1.jar** para a pasta *libs* do seu projeto do Eclipse e atualize a pasta *libs*.

    <div class="dev-callout"><b>Note</b>
	<p>The numbers at the end of the file name may change in subsequent SDK releases.</p>
    </div>

9.  Abra o arquivo *ToDoItemActivity.java* e adicione a seguinte declaração de importação:

		import com.microsoft.windowsazure.notifications.NotificationsManager;

10. Adicione a seguinte variável particular à classe, onde _`<PROJECT_NUMBER>`_ é o número do projeto atribuído pelo Google para seu aplicativo no procedimento anterior:

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

11. No método **onCreate**, antes que a instância do MobileServiceClient seja criada, adicione este código que registra o Manipulador de Notificações para o dispositivo:

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);



12. Adicione a seguinte linha de código ao método **addItem**, antes que o item seja inserido na tabela:

		item.setHandle(MyHandler.getHandle());

	Esse código define a propriedade `handle` do item como a ID de registro do dispositivo.

13. No Gerenciador de Pacotes, clique com o botão direito do mouse no pacote (no nó `src`), clique em **Novo**, depois em **Classe**.

14. Em **Nome**, digite `MyHandler`, em **Superclasse**, digite `com.microsoft.windowsazure.notifications.NotificationsHandler`, e, em seguida, clique em **Concluir**

	![][6]

	This creates the new MyHandler class.

15. Adicione as seguintes declarações de importação:

		import android.content.Context;
		
		import com.microsoft.windowsazure.notifications.NotificationsHandler;
		

16. Adicione os códigos a seguir:

		@com.google.gson.annotations.SerializedName("handle")
		private static String mHandle;
	
		public static String getHandle() {
			return mHandle;
		}
	
		public static final void setHandle(String handle) {
			mHandle = handle;
		}
	

17. Troque o método de substituição **onRegistered** existente pelo seguinte código:

		@Override
		public void onRegistered(Context context, String gcmRegistrationId) {
			super.onRegistered(context, gcmRegistrationId);
			
			setHandle(gcmRegistrationId);
		}
		

Seu aplicativo foi atualizado para oferecer suporte a notificações de push.


##<a id="update-scripts"></a>Atualizar o script de inserção registrados no Portal de Gerenciamento

1. No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**. 

   	![][21]

2. Em **todoitem**, clique na guia **Script** e selecione **Inserir**.
   
  	![][22]

   	Isso exibirá a função invocada quando ocorrer uma inserção na tabela **TodoItem**.

3. Substitua a função de inserção pelo seguinte código e clique em **Salvar**:

		function insert(item, user, request) {
			request.execute({
				success: function() {
					// Write to the response and then send the notification in the background
					request.respond();
					push.gcm.send(item.handle, item.text, {
						success: function(response) {
							console.log('Push notification sent: ', response);
						}, error: function(error) {
							console.log('Error sending push notification: ', error);
						}
					});
				}
			});
		}

   	Isso registra um novo script de inserção, que usa o [objeto gcm] para enviar uma notificação por push (o texto inserido) para o dispositivo fornecido na solicitação de inserção. 

##<a id="test"></a>Testar notificações por push no seu aplicativo

<div class="dev-callout"><b>Observação</b>
	<p>Quando você executa o aplicativo no emulador, certifique-se de usar um Android Virtual Device (AVD) que oferece suporte a APIs do Google.</p>
</div>

1. Reinicie o Eclipse e, em seguida, no Gerenciador de Pacotes, clique com botão direito do mouse no projeto, clique em **Propriedades**, clique em **Android**, marque **APIs do Google** e clique em **OK**.

	![][23]

  	Isso tem como alvo o projeto para APIs do Google.

2. Na **Janela**, selecione **Android Virtual Device Manager**, selecione o dispositivo e clique em **Editar**.

	![][24]

3. Selecione **APIs do Google** em **Destino** e clique em OK.

   	![][25]

	Destina-se ao AVD para usar APIs do Google.

4. No menu **Executar**, clique em **Executar** para iniciar o aplicativo.

5. No aplicativo, digite um texto significativo, como _Uma nova tarefa de Serviços Móveis_ e clique no botão **Adicionar**.

  	![][26]

6. Você verá uma caixa de notificação preta aparecer rapidamente na parte inferior da tela. 

  	![][28]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.

  ![][27]-->

Este tutorial foi concluído com êxito.

## <a name="next-steps"></a>Próximas etapas

Neste exemplo simples, um usuário recebe uma notificação por push com os dados que você acabou de inserir. O token de dispositivo usado pelo GCM é fornecido para o serviço móvel pelo cliente na solicitação. No próximo tutorial, [Notificações de push para os usuários do aplicativo], você criará uma tabela separada de Dispositivos para armazenar tokens de dispositivo e enviar uma notificação por push para todos os canais armazenados quando ocorre uma inserção. 


<!-- Images. -->

<google-developers.png
[2]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server.png
[3]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server2.png
[4]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server3.png
[5]: ./media/mobile-services-android-get-started-push/mobile-services-android-sdk-manager.png
[6]: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
[7]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-new-project.png
[8]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-sms-verify.png
[9]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-project-created.png
[10]: ./media/mobile-services-android-get-started-push/mobile-google-choose-play-api.png
[18]: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
[21]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
[24]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
[25]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
[26]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
[27]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push2-android.png
[28]: ./media/mobile-services-android-get-started-push/mobile-push-icon.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

<!-- URLs. -->
[Google Cloud Console]: https://cloud.google.com/console
[Apis do Google]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Portal de Provisionamento Android]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Configurar o SDK do Google Play Services]: http://go.microsoft.com/fwlink/?LinkId=389801
[Fazendo referência a um projeto de biblioteca]: http://go.microsoft.com/fwlink/?LinkId=389800
[SDK para Android de Serviços Móveis]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-android
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-android
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-android
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-android
[Notificações por push para usuários de aplicativos]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-android
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-android

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Android de Serviços Móveis conceitual]: /pt-br/develop/mobile/how-to-guides/work-with-android-client-library/
[objeto gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645

