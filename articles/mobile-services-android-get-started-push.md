<properties linkid="develop-mobile-tutorials-get-started-with-push-android" urlDisplayName="Get Started with Push (Android)" pageTitle="Get started with push notifications (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Introdução às notificações por push nos Serviços Móveis (envio herdado)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/develop/mobile/tutorials/get-started-with-push-dotnet" title="C# da Windows Store">C# da Windows Store</a>
    <a href="/pt-br/develop/mobile/tutorials/get-started-with-push-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a>
    <a href="/pt-br/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a>
    <a href="/pt-br/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a>
    <a href="/pt-br/develop/mobile/tutorials/get-started-with-push-android" title="Android" class="current">Android</a>
<!--    <a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
    <a href="/pt-br/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Back-end do .NET">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-android-get-started-push/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações de push a um aplicativo do Android. Neste tutorial você adiciona notificações de push utilizando o serviço de Mensagens de Nuvem do Google (GCM) ao projeto de inicialização rápida. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.

> [WACOM.NOTE]Este tópico oferece suporte para os serviços móveis *existentes* que ainda não foram *atualizados* para usar a integração de Hubs de Notificação. Quando você cria um serviço móvel *novo*, esta funcionalidade integrada é habilitada automaticamente. Para novos serviços móveis, consulte [Introdução às notificações por push][Introdução às notificações por push].
>
> Os Serviços Móveis integram-se aos Hubs de Notificação do Azure para oferecer suporte à funcionalidade de notificação por push, como modelos, várias plataformas e escala aprimorada. *Você deve atualizar seus serviços móveis existentes para usar os Hubs de Notificação quando possível*. Assim que você tiver feito a atualização, consulte esta versão da [Introdução às notificações por push][Introdução às notificações por push].

Este tutorial explica as etapas básicas para habilitar as notificações por push:

-   [Habilitar o sistema de mensagens em nuvem do Google][Habilitar o sistema de mensagens em nuvem do Google]
-   [Configurar os Serviços Móveis][Configurar os Serviços Móveis]
-   [Adicionar notificações de push para seu aplicativo][Adicionar notificações de push para seu aplicativo]
-   [Atualizar scripts para enviar notificações por push][Atualizar scripts para enviar notificações por push]
-   [Inserir dados para receber notificações][Inserir dados para receber notificações]

Este tutorial exige o seguinte:

-   [SDK para Android de Serviços Móveis][SDK para Android de Serviços Móveis]
-   Uma conta ativa do Google

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

## <span id="register"></span></a>Habilitar o sistema de mensagens em nuvem do Google

[WACOM.INCLUDE [Habilitar GCM][Habilitar GCM]]

Em seguida, você usará esse valor de chave de API para ativar os Serviços Móveis para autenticar com o GCM e enviar notificações de push em nome do seu aplicativo.

## <span id="configure"></span></a>Configurar Serviços Móveis para enviar solicitações de push

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu aplicativo.

    ![][]

2.  Clique na guia **Push**, insira o valore **Chave do API** obtida do GCM no procedimento anterior e clique em **Salvar**.

    ![][1]

Seu serviço móvel agora está configurado para funcionar com o GCM para enviar notificações de push.

## <span id="add-push"></span></a>Adicionar notificações de push para seu aplicativo

### Inclua o Google Play Services no projeto

[WACOM.INCLUDE [Incluir Serviços de Reprodução][Incluir Serviços de Reprodução]]

### Incluir código

1.  Abra o arquivo de projeto **AndroidManifest.xml**. A mensagens de nuvem do Google tem alguns requisitos mínimos de nível do API para desenvolvimento e teste, o que a propriedade **minSdkVersion** no manifesto deve estar de acordo. Consulte [Definir o SDK Google Play Services][Definir o SDK Google Play Services] para determinar quão baixo você pode definir esse valor, se precisar defini-lo abaixo de 16, porque você está usando um dispositivo mais antigo. Defina a propriedade adequadamente.

2.  Certifique-se de que o elemento `uses-sdk`, o **targetSdkVersion** esteja definido para o número de uma plataforma SDK que foi instalada (etapa 1). É preferível definir para a versão mais recente disponível.

3.  A marca **uses-sdk** pode parecer com isso, dependendo das escolhas que você fez nas etapas anteriores:

        <uses-sdk
            android:minSdkVersion="17"
            android:targetSdkVersion="19" />

4.  No código nas duas próximas etapas, substitua *`**my_app_package**`* pelo nome do pacote do aplicativo para o seu projeto, que é o valor do atributo `package` da marca `manifest`.

5.  Adicione as seguintes novas permissões depois do elemento `uses-permission`:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

6.  Adicione o código a seguir após o marca de abertura `application`:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

7.  Abra o arquivo ToDoItem.java, adicione o seguinte código para a classe **TodoItem**:

            @com.google.gson.annotations.SerializedName("handle")
            private String mHandle;

            public String getHandle() {
                return mHandle;
            }

            public final void setHandle(String handle) {
                mHandle = handle;
            }

    Esse código cria uma nova propriedade que contém a ID de registro.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Quando o esquema din&acirc;mico estiver ativado no seu servi&ccedil;o m&oacute;vel, uma nova coluna <strong>handle</strong> &eacute; adicionada automaticamente na tabela <strong>TodoItem</strong> quando um novo item que cont&eacute;m essa propriedade &eacute; inserido.</p>
</div>

8.  Baixe e descompacte o [Mobile Services Android SDK][SDK para Android de Serviços Móveis], abra a pasta **notificações**, copie o arquivo **notifications-1.0.1.jar** para a pasta *libs* do seu projeto Eclipse e atualize a pasta *libs*.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Os n&uacute;meros no fim do nome do arquivo podem ser alterados em vers&otilde;es subsequentes do SDK.</p>
</div>

9.  Abra o arquivo *ToDoItemActivity.java* e adicione a seguinte declaração de importação:

        import com.microsoft.windowsazure.notifications.NotificationsManager;

10. Adicione a seguinte variável privada à classe, onde *`<PROJECT_NUMBER>`* é o Número do Projeto atribuído pelo Google para seu aplicativo no procedimento anterior:

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

11. No método **onCreate**, antes que a instância MobileServiceClient seja criada, adicione este código, que registra o Manipulador de Notificações para o dispositivo:

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

12. Adicione a seguinte linha de código ao método **addItem**, antes que o item seja inserido na tabela:

        item.setHandle(MyHandler.getHandle());

    Esse código define a propriedade `handle` do item como a ID de registro do dispositivo.

13. No Gerenciador de Pacotes, clique com o botão direito no pacote (no nó `src`), clique em **Novo** e em **Classe**.

14. Em **Nome**, digite `MyHandler`, no tipo **Superclasse**, digite \` `com.microsoft.windowsazure.notifications.NotificationsHandler` e clique em **Concluir**.

    ![][2]

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

Seu aplicativo foi atualizado para oferecer suporte a notificações de push.

## <span id="update-scripts"></span></a>Atualizar o script de inserção registrados no Portal de Gerenciamento

1.  No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**.

    ![][3]

2.  Em **todoitem**, clique na guia **Script** e selecione **Inserir**.

    ![][4]

    Isso exibe a função que é chamada quando ocorre uma inserção na tabela **TodoItem**.

3.  Substitua a função de inserção com o seguinte código e **Salvar**:

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

    Isso registra um novo script de inserção, que usa o [objeto gcm][objeto gcm] para enviar uma notificação por push (o texto inserido) para o dispositivo fornecido na solicitação de inserção.

## <span id="test"></span></a>Testar notificações de push no seu aplicativo

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
    <p>Quando voc&ecirc; executa o aplicativo no emulador, certifique-se de usar um Android Virtual Device (AVD) que oferece suporte a APIs do Google.</p>
</div>

1.  Reinicie o Eclipse, em seguida, no Gerenciador de Pacotes, clique com botão direito do mouse no projeto, clique em **Propriedades**, em **Android**, marque **Google APIs** e clique em **OK**.

    ![][5]

    Isso tem como alvo o projeto para APIs do Google.

2.  Na **Janela**, selecione **Android Virtual Device Manager**, selecione o dispositivo e clique em **Editar**.

    ![][6]

3.  Selecione **Google APIs** em **Destino** e clique em OK.

    ![][7]

    Destina-se ao AVD para usar APIs do Google.

4.  No menu **Executar**, clique em **Executar** para iniciar o aplicativo.

5.  No aplicativo, digite um texto significativo, como *Uma nova tarefa de Serviços Móveis* e clique no botão **Incluir**.

    ![][8]

6.  Você verá uma caixa de notificação preta aparecer rapidamente na parte inferior da tela.

    ![][9]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.    ![][27]-->

Este tutorial foi concluído com êxito.

## <a name="next-steps"></a>Próximas etapas

Neste exemplo simples, um usuário recebe uma notificação por push com os dados que você acabou de inserir. O token de dispositivo usado pelo GCM é fornecido para o serviço móvel pelo cliente na solicitação. No próximo tutorial, [Notificações de push para os usuários do aplicativo][Notificações de push para os usuários do aplicativo], você irá criar uma tabela separada de Dispositivos para armazenar tokens de dispositivo e enviar uma notificação por push para todos os canais armazenados quando ocorre uma inserção.

<!-- Images. --> 
<!-- URLs. -->

  [C# da Windows Store]: /pt-br/develop/mobile/tutorials/get-started-with-push-dotnet "C# da Windows Store"
  [JavaScript da Windows Store]: /pt-br/develop/mobile/tutorials/get-started-with-push-js "JavaScript da Windows Store"
  [Windows Phone]: /pt-br/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone"
  [iOS]: /pt-br/develop/mobile/tutorials/get-started-with-push-ios "iOS"
  [Android]: /pt-br/develop/mobile/tutorials/get-started-with-push-android "Android"
  [Appcelerator]: /pt-br/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push "Appcelerator"
  [Back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ "Back-end do .NET"
  [Back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-android-get-started-push/ "Back-end do JavaScript"
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Habilitar o sistema de mensagens em nuvem do Google]: #register
  [Configurar os Serviços Móveis]: #configure
  [Adicionar notificações de push para seu aplicativo]: #add-push
  [Atualizar scripts para enviar notificações por push]: #update-scripts
  [Inserir dados para receber notificações]: #test
  [SDK para Android de Serviços Móveis]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
  [Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-android
  [Habilitar GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [Incluir Serviços de Reprodução]: ../includes/mobile-services-add-Google-play-services.md
  [Definir o SDK Google Play Services]: http://go.microsoft.com/fwlink/?LinkId=389801
  [2]: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
  [4]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
  [objeto gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645
  [5]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
  [6]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [7]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [8]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [9]: ./media/mobile-services-android-get-started-push/mobile-push-icon.png
  [Notificações de push para os usuários do aplicativo]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-android
