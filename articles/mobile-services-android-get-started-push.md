<properties 
	pageTitle="Introdução às notificações por push (Android) | Centro de Desenvolvimento de Serviços Móveis" 
	description="Saiba como usar serviços móveis do Azure para enviar notificações por push para seu aplicativo Android." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="Mobile-Android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="ricksal"/>

# Introdução às notificações por push nos Serviços Móveis (envio herdado)

<div class="dev-center-tutorial-selector sublanding"> <a href="/en-us/develop/mobile/tutorials/get-started-with-push-dotnet" title="Windows Store C#">Windows Store C#</a> <a href="/en-us/develop/mobile/tutorials/get-started-with-push-js" title="Windows Store JavaScript">Windows Store JavaScript</a> <a href="/en-us/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a> <a href="/en-us/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a> <a href="/en-us/develop/mobile/tutorials/get-started-with-push-android" title="Android" class="current">Android</a>
<!--    <a href="/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a> <a href="/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/en-us/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET backend">Back-end .NET</a> | <a href="/en-us/documentation/articles/mobile-services-android-get-started-push/"  title="JavaScript backend" class="current">Back-end JavaScript</a></div>

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações por push a um aplicativo do Android. Neste tutorial você adiciona notificações por push utilizando o serviço de Mensagens de Nuvem do Google (GCM) ao projeto de início rápido. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.

>[AZURE.NOTE]Este tópico oferece suporte a serviços móveis <em>existentes</em> que ainda <em>não foram atualizados</em> usar a integração de Hubs de Notificação. Quando você cria um <em>novo</em> serviço móvel, essa funcionalidade integrada é habilitada automaticamente. Para novos serviços móveis, consulte [Introdução às notificações por push](/en-us/documentation/articles/mobile-services-javascript-backend-android-get-started-push/).
>
>Os Serviços Móveis integram-se aos Hubs de Notificação do Azure para dar suporte à funcionalidade de notificação por push, como modelos, várias plataformas e escala aprimorada. <em>Você deve atualizar seus serviços móveis existentes para usar os Hubs de Notificação quando possível</em>. Assim que você tiver feito a atualização, consulte esta versão da [Introdução às notificações por push](/en-us/documentation/articles/mobile-services-javascript-backend-android-get-started-push/).

Este tutorial explica as etapas básicas para habilitar as notificações por push:

* [Habilitar o Google Cloud Messaging](#register)
* [Configurar os Serviços Móveis](#configure)
* [Adicionar notificações por push ao seu aplicativo](#add-push)
* [Atualizar scripts para enviar notificações por push](#update-scripts)
* [Inserir dados para receber notificações](#test)

Este tutorial exige o seguinte:

+ [SDK para Android de Serviços Móveis]
+ Uma conta ativa do Google

Este tutorial baseia-se no guia de início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis]. 

##<a id="register"></a>Habilitar o Google Cloud Messaging


[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Em seguida, você usará esse valor de chave de API para habilitar os Serviços Móveis para autenticar com o GCM e enviar notificações por push em nome do seu aplicativo.

##<a id="configure"></a>Configurar Serviços Móveis para enviar solicitações por push

1. Entre no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis**, e clique em seu aplicativo.

   	![][18]

2. Clique na guia **Enviar por Push**, insira o valor da **Chave da API** obtida do GCM no procedimento anterior e clique em **Salvar**.

   	![][19]

Seu serviço móvel agora está configurado para funcionar com o GCM para enviar notificações por push.

##<a id="add-push"></a>Adicionar notificações por push ao seu aplicativo


###Inclua o Google Play Services no projeto

[AZURE.INCLUDE [Adicionar Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Incluir código

1. Abra o arquivo de projeto **AndroidManifest.xml**. O Google Cloud Messaging tem alguns requisitos mínimos de nível de API para desenvolvimento e teste, com os quais a propriedade **minSdkVersion** no Manifesto deve estar em conformidade. Consulte [Definir o SDK Google Play Services] para determinar quão baixo você pode definir esse valor, se precisar defini-lo abaixo de 16, porque você está usando um dispositivo mais antigo. Defina a propriedade adequadamente.

2. Certifique-se de que o elemento `uses-sdk` o **targetSdkVersion** esteja definido para o número de uma plataforma SDK instalada (etapa 1). É preferível definir para a versão mais recente disponível. 

3. A marca **uses-sdk** pode ser semelhante à marca abaixo, dependendo das escolhas que você fez nas etapas anteriores:

	    <uses-sdk
	        android:minSdkVersion="17"
	        android:targetSdkVersion="19" />
	
4. No código nas duas próximas etapas, substitua _`**my_app_package**`_ pelo nome do pacote do aplicativo para o seu projeto, que é o valor do atributo `package` da marca `manifest`. 

5. Adicione as seguintes novas permissões conforme o elemento `uses-permission` existente:
     <permission android:name="**my_app_package**.permission.C2D_MESSAGE"          android:protectionLevel="signature" />     <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" />      <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />     <uses-permission android:name="android.permission.GET_ACCOUNTS" />     <uses-permission android:name="android.permission.WAKE_LOCK" />

6. Adicione o código a seguir após o marca de abertura `application`: 
     <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"         android:permission="com.google.android.c2dm.permission.SEND">         <intent-filter>             <action android:name="com.google.android.c2dm.intent.RECEIVE" />             <category android:name="**my_app_package**" />         </intent-filter>     </receiver>



7. Abra o arquivo ToDoItem.java, adicione o seguinte código à classe **TodoItem**:

			@com.google.gson.annotations.SerializedName("handle")
			private String mHandle;
		
			public String getHandle() {
				return mHandle;
			}
		
			public final void setHandle(String handle) {
				mHandle = handle;
			}
		
	Esse código cria uma nova propriedade que contém a ID de registro.
 > [AZURE.NOTE] Quando o esquema dinâmico estiver habilitado no seu serviço móvel, uma nova coluna **handle** é adicionada automaticamente na tabela **TodoItem** quando um novo item que contém essa propriedade é inserido.

8. Baixe e descompacte o [SDK dos Serviços Móveis do Android], abra a pasta **notifications** copie o arquivo **notifications-1.0.1.jar** na pasta *libs* do seu projeto do Eclipse e atualize a pasta *libs*.
 > [AZURE.NOTE] Os números no fim do nome do arquivo podem ser alterados em versões subsequentes do SDK.

9.  Abra o arquivo *ToDoItemActivity.java*, e adicione a seguinte declaração de importação:

		import com.microsoft.windowsazure.notifications.NotificationsManager;

10. Adicione a seguinte variável particular à classe, onde _`<PROJECT_NUMBER>`_ é o número do projeto atribuído pelo Google ao seu aplicativo no procedimento anterior:

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

11. No método **onCreate**, antes que a instância do MobileServiceClient seja criada, adicione este código que registra o Manipulador de Notificações para o dispositivo:

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);



12. Adicione a seguinte linha de código ao método **addItem**, antes que o item seja inserido na tabela:

		item.setHandle(MyHandler.getHandle());

	Esse código define a propriedade `handle` do item para a ID de registro do dispositivo.

13. No Gerenciador de Pacotes, clique com o botão direito no pacote (no nó `src` ), clique em **Novo**, e clique em **Classe**.

14. Em **Nome** digite `MyHandler`, em **Superclasse** digite `com.microsoft.windowsazure.notifications.NotificationsHandler`, depois clique em **Concluir**

	![][6]

	Isto criará a nova classe MyHandler.

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
	

17. Substitua o método **onRegistered** existente pelo seguinte código:

		@Override
		public void onRegistered(Context context, String gcmRegistrationId) {
			super.onRegistered(context, gcmRegistrationId);
			
			setHandle(gcmRegistrationId);
		}
		

Seu aplicativo foi atualizado para dar suporte a notificações por push.


##<a id="update-scripts"></a>Atualizar o script de inserção registrado no Portal de Gerenciamento

1. No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**. 

   	![][21]

2. Em **todoitem**, clique na guia **Script** e selecione **Inserir**.
   
  	![][22]

   	Isso exibirá a função chamada quando ocorrer uma inserção na tabela **TodoItem**.

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

> [AZURE.NOTE] Quando você executa o aplicativo no emulador, certifique-se de usar um Android Virtual Device (AVD) que dá suporte a APIs do Google.

1. Reinicie o Eclipse, em seguida, no Gerenciador de Pacotes, clique com botão direito no projeto, clique em **Propriedades**, clique em **Android**, verifique **APIs do Google**, em seguida, clique em **OK**.

	![][23]

  	Isso tem como alvo o projeto para APIs do Google.

2. Na **Janela**, selecione **Android Virtual Device Manager**, selecione o dispositivo e clique em **Editar**.

	![][24]

3. Selecione **APIs do Google** em **Destino**, e clique em OK.

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

Neste exemplo simples, um usuário recebe uma notificação por push com os dados que você acabou de inserir. O token de dispositivo usado pelo GCM é fornecido para o serviço móvel pelo cliente na solicitação. No próximo tutorial, [Notificações por push para usuários do aplicativo], você criará uma tabela separada de Dispositivos para armazenar tokens de dispositivo e enviar uma notificação por push para todos os canais armazenados quando ocorrer uma inserção. 


<!-- Images. -->

[1]: ./media/mobile-services-android-get-started-push/mobile-services-google-developers.png
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
[Console do Google Cloud]: https://cloud.google.com/console
[Apis do Google]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Portal de provisionamento do Android]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Configurar o SDK do Google Play Services]: http://go.microsoft.com/fwlink/?LinkId=389801
[Fazendo referência a um projeto de biblioteca]: http://go.microsoft.com/fwlink/?LinkId=389800
[SDK para Android de Serviços Móveis]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Introdução aos Serviços Móveis]: /en-us/develop/mobile/tutorials/get-started-android
[Introdução aos dados]: /en-us/develop/mobile/tutorials/get-started-with-data-android
[Introdução à autenticação]: /en-us/develop/mobile/tutorials/get-started-with-users-android
[Introdução às notificações por push]: /en-us/develop/mobile/tutorials/get-started-with-push-android
[Notificações por push para usuários do aplicativo]: /en-us/develop/mobile/tutorials/push-notifications-to-users-android
[Autorizar usuários com scripts]: /en-us/develop/mobile/tutorials/authorize-users-android

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Android de serviços móveis conceitual]: /en-us/develop/mobile/how-to-guides/work-with-android-client-library/
[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645

\n<!--HONumber=42-->
