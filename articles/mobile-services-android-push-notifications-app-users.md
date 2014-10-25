<properties linkid="develop-mobile-tutorials-push-notifications-to-users-android" urlDisplayName="" pageTitle="Push notifications to users (Android ) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Enviar notificações por push aos usuários usando os Serviços Móveis

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS">iOS</a><a href="/pt-br/develop/mobile/tutorials/push-notifications-to-users-android" title="Android" class="current">Android</a>
</div>

<div class="dev-onpage-left-content">
<p>Este t&oacute;pico estende o <a href="/pt-br/develop/mobile/tutorials/get-started-with-push-android">tutorial anterior de notifica&ccedil;&atilde;o por push</a> adicionando uma nova tabela para armazenar os URIs de registro do Google Cloud Messaging (GCM) que, em seguida, pode ser usada para enviar notifica&ccedil;&otilde;es por push a v&aacute;rios usu&aacute;rios do aplicativo Android. Neste tutorial, uma &uacute;nica atualiza&ccedil;&atilde;o ir&aacute; gerar notifica&ccedil;&otilde;es por push a todos os dispositivos registrados sempre que forem feitas inser&ccedil;&otilde;es na tabela ToDoList. No tutorial anterior, uma notifica&ccedil;&atilde;o era enviada somente ao dispositivo que fazia a inser&ccedil;&atilde;o.</p>
</div>

Este tutorial explica as etapas para atualizar notificações por push em seu aplicativo:

1.  [Criar a tabela Registration][Criar a tabela Registration]
2.  [Atualizar seu aplicativo][Atualizar seu aplicativo]
3.  [Atualizar scripts de servidor][Atualizar scripts de servidor]
4.  [Verificar o comportamento de notificação por push][Verificar o comportamento de notificação por push]

Esse tutorial se baseia no início rápido dos Serviços Móveis e se baseia no tutorial anterior [Introdução às notificações por push][tutorial anterior de notificação por push]. Antes de iniciar o tutorial, você deve primeiro concluir a [Introdução às notificações por push][tutorial anterior de notificação por push].

## <a name="create-table"></a>Criar uma nova tabela

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique no seu aplicativo.

    ![][]

2.  Clique na guia **Dados** e **Criar**.

    ![][1]

    Isso exibe a caixa de diálogo **Criar uma nova tabela**.

3.  Mantendo a configuração padrão **Qualquer pessoa com a chave do aplicativo** para todas as permissões, digite *Registration* em **Nome da tabela** e, em seguida, clique no botão de seleção.

    ![][2]

    Isso criará a tabela **Registration**, que armazena os URIs dos registros usados para enviar notificações por push separados dos dados do item.

Em seguida, você modificará o aplicativo de notificações por push para armazenar os dados de registro nessa nova tabela, e não na tabela **TodoItem**.

## <a name="update-app"></a>Atualizar seu aplicativo

1.  No Eclipse, no Gerenciador de Pacotes, clique com o botão direito no pacote (no nó `src`), clique em **Novo** e em **Classe**.

2.  Em **Nome**, digite `Registration` e clique em **Concluir**.

    ![][3]

    Isso criará a nova classe Registration.

3.  Abra o arquivo ToDoItem.java e recorte o código a seguir:

        @com.google.gson.annotations.SerializedName("handle")
        private String mHandle;

        public String getHandle() {
            return mHandle;
        }

        public final void setHandle(String handle) {
            mHandle = handle;
        }

4.  Cole o código recortado na etapa anterior no corpo da classe **Registration** que você criou anteriormente.

5.  Adicione o seguinte código à classe **Registration**:

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

7.  Localize a propriedade `mClient` e substitua-a pelo código a seguir:

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

8.  No arquivo **MyHandler**, adicione as seguintes instruções de importação:

        import android.util.Log;

        import com.microsoft.windowsazure.notifications.NotificationsHandler;

        import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
        import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
        import com.microsoft.windowsazure.mobileservices.TableOperationCallback;

9.  Adicione o código a seguir no final do método `onRegistered`:

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

1.  No Portal de Gerenciamento, clique na guia **Dados** e, em seguida, clique na tabela **Registration**.

    ![][4]

2.  Em **registration**, clique na guia **Script** e selecione **Inserir**.

    ![][5]

    Isso exibe a função que é invocada quando ocorre uma inserção na tabela **Registration**.

3.  Substitua a função de inserção com o seguinte código e **Salvar**:

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

4.  Clique em **TodoItem**, clique em **Script** e selecione **Inserir**.

    ![][6]

5.  Substitua a função de inserção com o seguinte código e **Salvar**:

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


    Esse script de inserção envia uma notificação por push (com o texto do item inserido) a todos os registros armazenados na tabela **Registration**.

## <a name="test-app"></a>Testar o aplicativo

1.  No Eclipse, no menu **Executar**, clique em **Executar** para iniciar o aplicativo.

2.  No aplicativo, digite um texto significativo, como *Uma nova tarefa de Serviços Móveis* e clique no botão **Incluir**.

3.  Você verá uma caixa de notificação preta aparecer rapidamente na parte inferior da tela.

    ![][7]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.      ![][27]-->

Este tutorial foi concluído com êxito.

## Próximas etapas

Isso conclui os tutoriais que demonstram os conceitos básicos de como trabalhar com notificações por push. Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

-   [Começar a trabalhar com dados][Começar a trabalhar com dados]
    Saiba mais sobre armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][Introdução à autenticação]
    Saiba como autenticar usuários de seu aplicativo com uma conta do Windows.

-   [Referência de script de servidor dos Serviços Móveis][Referência de script de servidor dos Serviços Móveis]
    Saiba mais sobre como registrar e usar scripts de servidor.

-   [Como usar a biblioteca de cliente Android para os Serviços Móveis][Como usar a biblioteca de cliente Android para os Serviços Móveis]
    Saiba mais sobre como usar os Serviços Móveis com o .NET.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows Phone]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-wp8 "Windows Phone"
  [iOS]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-ios "iOS"
  [Android]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-android "Android"
  [tutorial anterior de notificação por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-android
  [Criar a tabela Registration]: #create-table
  [Atualizar seu aplicativo]: #update-app
  [Atualizar scripts de servidor]: #update-scripts
  [Verificar o comportamento de notificação por push]: #test-app
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-table.png
  [3]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-class.png
  [4]: ./media/mobile-services-android-push-notifications-app-users/mobile-portal-data-tables-registration.png
  [5]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-registration.png
  [6]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-push2.png
  [7]: ./media/mobile-services-android-push-notifications-app-users/mobile-push-icon.png
  [Começar a trabalhar com dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-android
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-android
  [Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Como usar a biblioteca de cliente Android para os Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-android-client-library
