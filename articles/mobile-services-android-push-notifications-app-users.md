<properties linkid="" urlDisplayName="" pageTitle="Enviar notificações por push aos usuários (Android ) | Mobile Dev Center" metaKeywords="" description="Saiba como usar os Serviços Móveis para enviar notificações por push aos usuários de seu aplicativo Android." metaCanonical="" services="" documentationCenter="Mobile" title="Enviar notificações por push aos usuários usando os Serviços Móveis" authors="ricksal" solutions="" manager="" editor="" />


# Enviar notificações por push aos usuários usando os Serviços Móveis

<div class="dev-center-tutorial-selector sublanding">
	<a href="/pt-br/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/push-notifications-to-users-android" title="Android" class="current">Android</a>
</div>

<div class="dev-onpage-left-content">
<p>Este tópico estende o <a href="/pt-br/develop/mobile/tutorials/get-started-with-push-android">tutorial anterior de notificação por push</a> adicionando uma nova tabela para armazenar os URIs de registro do Google Cloud Messaging (GCM) que, em seguida, pode ser usada para enviar notificações por push a vários usuários do aplicativo Android. Neste tutorial, uma única atualização irá gerar notificações por push a todos os dispositivos registrados sempre que forem feitas inserções na tabela ToDoList. No tutorial anterior, uma notificação era enviada somente ao dispositivo que fazia a inserção.</p>
</div>


Este tutorial explica as etapas para atualizar notificações por push em seu aplicativo:

1. [Criar a tabela Registration]
2. [Atualizar seu aplicativo]
3. [Atualizar scripts de servidor]
4. [Verificar o comportamento da notificação por push] 

Esse tutorial se baseia no quickstart dos Serviços Móveis e se baseia no tutorial anterior [Introdução às notificações por push]. Antes de iniciar o tutorial, você deve primeiro concluir a [Introdução às notificações por push].  

## <a name="create-table"></a>Criar uma nova tabela

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e, em seguida, clique em seu aplicativo.

   	![][0]

2. Clique na guia **Dados** e, em seguida, clique em **Criar**.

   	![][1]

   	Isso exibe a caixa de diálogo **Criar uma nova tabela**.

3. Mantendo a configuração padrão **Qualquer pessoa com a chave do aplicativo** para todas as permissões, digite _Registration_ em **Nome da tabela** e, em seguida, clique no botão de seleção.

   	![][2]

  	Isso criará a tabela **Registration**, que armazena os URIs dos registros usados para enviar notificações por push separados dos dados do item.

Em seguida, você modificará o aplicativo de notificações por push para armazenar os dados de registro nessa nova tabela, e não na tabela **TodoItem**.

## <a name="update-app"></a>Atualizar seu aplicativo

1. No Eclipse, no Gerenciador de Pacotes, clique com o botão direito do mouse no pacote (sob o nó `src`), clique em **Novo** e em **Classe**.

2. Em **Nome**, digite `Registration` e, em seguida, clique em **Concluir**.

	![][6]

	Isso criará a nova classe Registration.

3. Abra o arquivo ToDoItem.java e recorte o código a seguir:

		@com.google.gson.annotations.SerializedName("handle")
		private String mHandle;
	
		public String getHandle() {
			return mHandle;
		}
	
		public final void setHandle(String handle) {
			mHandle = handle;
		}
	

4. Cole o código recortado na etapa anterior no corpo da classe **Registration** que você criou anteriormente.



5. Adicione o seguinte código à classe **Registration**:

		@com.google.gson.annotations.SerializedName("id")
		private int mId;
	
		/**
		 * Returns the item id
		 */
		public int getId() {
			return mId;
		}
	
		/**
		 * Sets the item id
		 * 
		 * @param id : id to set
		 */
		public final void setId(int id) {
			mId = id;
		}


6.  Abra o arquivo **ToDoActivity.java** e, no método `addItem`, exclua as seguintes linhas:

		item.setHandle(MyHandler.getHandle());

7. Localize a propriedade `mClient` e substitua-a pelo código a seguir:

		/**
		 * Mobile Service Client reference
		 */
		private static MobileServiceClient mClient;
	
		/**
		 * Returns the client reference
		 */
		public static MobileServiceClient getClient() {
			return mClient;
		}
	




8. No arquivo **MyHandler**, adicione as seguintes instruções de importação:

		import android.util.Log;
		
		import com.microsoft.windowsazure.notifications.NotificationsHandler;

		import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
		import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
		import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
		import com.microsoft.windowsazure.mobileservices.TableOperationCallback;
		

9. Adicione o código a seguir no final do método `onRegistered`:

	    MobileServiceClient client = ToDoActivity.getClient();
	    MobileServiceTable<Registration> registrations = client.getTable(Registration.class);

	    // Create a new Registration
	    Registration registration = new Registration();
	    registration.setHandle(gcmRegistrationId);

	    // Insert the new Registration
	    registrations.insert(registration, new TableOperationCallback<Registration>() {

	        public void onCompleted(Registration entity, Exception exception, ServiceFilterResponse response) {

	            if (exception != null) {
	                Log.e("MyHandler", exception.getMessage());
	            } else {
	                Log.e("MyHandler", "Registration OK");
	            }
	        }
	    });
		

Seu aplicativo foi atualizado para oferecer suporte ao envio de notificações por push aos usuários.

## <a name="update-scripts"></a>Atualizar scripts de servidor

1. No Portal de Gerenciamento, clique na guia **Dados** e, em seguida, clique na tabela **Registration**. 

   	![][3]

2. Em **registration**, clique na guia **Script** e selecione **Inserir**.
   
   	![][4]

   	Isso exibe a função que é invocada quando ocorre uma inserção na tabela **Registration**.

3. Substitua a função de inserção pelo seguinte código e clique em **Salvar**:

		function insert(item, user, request) {
			var registrationTable = tables.getTable('Registration');
			registrationTable
				.where({ handle: item.handle })
				.read({ success: insertRegistrationIfNotFound });
	        function insertRegistrationIfNotFound(existingRegistrations) {
        	    if (existingRegistrations.length > 0) {
            	    request.respond(200, existingRegistrations[0]);
        	    } else {
            	    request.execute();
        	    }
    	    }
	    }

   	Esse script verifica se a tabela **Registration** contém um registro existente com o mesmo URI. A inserção continuará apenas se nenhum registro correspondente for localizado. Isso impede registros duplicados.

4. Clique em **TodoItem**, clique em **Script** e selecione **Inserir**. 

   	![][5]

5. Substitua a função de inserção pelo código a seguir e, em seguida, clique em **Salvar**:

		function insert(item, user, request) {
		    request.execute({
		        success: function() {
		            // Write to the response and then send the notification in the background
		            request.respond();
		            sendNotifications(item.text);
		
		        }
		    });
		
        function sendNotifications(item_text) {
            var registrationTable = tables.getTable('Registration');
            registrationTable.read({
                success: function(registrations) {
                    registrations.forEach(function(registration) {
                        push.gcm.send(registration.handle, item_text, {
                            success: function(response) {
                                console.log('Push notification sent: ', response);
                            }, error: function(error) {
                                console.log('Error sending push notification: ', error);
                            }
                        });
                    });
                }
            });
        }
    }

    Esse script de inserção envia uma notificação por push (com o texto do item inserido) a todos os registros armazenados na tabela **Registration**.

## <a name="test-app"></a>Testar o aplicativo

1. No Eclipse, no menu **Executar**, clique em **Executar** para iniciar o aplicativo.

5. No aplicativo, digite um texto significativo, como _Uma nova tarefa de Serviços Móveis_ e clique no botão **Adicionar**.

  
6. Você verá uma caixa de notificação preta aparecer rapidamente na parte inferior da tela. 

  	![][28]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.

  	![][27]-->

Este tutorial foi concluído com êxito.

## Próximas etapas

Isso conclui os tutoriais que demonstram os conceitos básicos de como trabalhar com notificações por push. Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando Serviços Móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários do seu aplicativo com uma conta do Windows.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como registrar e usar scripts de servidor.

* [Como usar a biblioteca de cliente Android para os Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com o .NET.
  
<!-- Anchors. -->
[Criar a tabela Registration]: #create-table
[Atualizar seu aplicativo]: #update-app
[Atualizar scripts de servidor]: #update-scripts
[Verificar o comportamento da notificação por push]: #test-app
[Próximas etapas]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-push-notifications-app-users/mobile-services-selection.png
[1]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-table.png
[2]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-table.png
[3]: ./media/mobile-services-android-push-notifications-app-users/mobile-portal-data-tables-registration.png
[4]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-registration.png
[5]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-push2.png
[6]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-class.png

[27]: ./media/mobile-services-android-push-notifications-app-users/mobile-quickstart-push2-android.png
[28]: ./media/mobile-services-android-push-notifications-app-users/mobile-push-icon.png

<!-- URLs. -->
[Notificações por push e Live Connect do Windows]: http://go.microsoft.com/fwlink/?LinkID=257677
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Painel Meus Aplicativos]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library/#create-new-service
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-android
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-android
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-android
[JavaScript e HTML]: mobile-services-win8-javascript/

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Como usar a biblioteca de cliente Android para os Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-android-client-library

