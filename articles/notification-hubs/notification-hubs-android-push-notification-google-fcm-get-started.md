---
title: Notificações por push para aplicativos Android usando Hubs de Notificação do Azure e Firebase Cloud Messaging | Microsoft Docs
description: Neste tutorial, você aprende a usar os Hubs de Notificação do Azure e o Google Firebase Cloud Messaging para enviar notificações por push a dispositivos Android.
services: notification-hubs
documentationcenter: android
keywords: notificações por push, notificação por push, notificação por push do android, fcm, firebase cloud messaging
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/05/2019
ms.author: jowargo
ms.openlocfilehash: 9467197715d79527699eac0acf9c23f204b0e639
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814877"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-firebase-cloud-messaging"></a>Tutorial: Enviar notificações por push para dispositivos Android usando Hubs de Notificação do Azure e Google Firebase Cloud Messaging

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Este tutorial mostra como usar os Hubs de Notificação do Azure e o FCM (Firebase Cloud Messaging) para enviar notificações por push a um aplicativo do Android. Neste tutorial, você cria um aplicativo Android em branco que recebe notificações por push usando o FCM.

O código completo para este tutorial pode ser baixado no GitHub [clicando aqui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase).

Neste tutorial, você deve executar as seguintes etapas:

> [!div class="checklist"]
> * Criar um projeto do Android Studio.
> * Criar um projeto do Firebase que ofereça suporte ao Firebase Cloud Messaging.
> * Criar um hub de notificação.
> * Conectar seu aplicativo ao hub de notificação.
> * Testar o aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/free/).

* Além de uma conta ativa do Azure mencionada acima, este tutorial requer a última versão do [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797).
* Android 2.3 ou superior para Firebase Cloud Messaging.
* A revisão 27 ou superior do Repositório do Google é necessária para o Firebase Cloud Messaging.
* Google Play Services 9.0.2 ou superior para Firebase Cloud Messaging.
* A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre Hubs de Notificação para aplicativos Android.

## <a name="create-an-android-studio-project"></a>Criar um projeto do Android Studio

1. Inicie o Android Studio.
2. Selecione **Arquivo** no menu, aponte para **Novo** e selecione **Novo Projeto**. 
2. Na página **Escolher o projeto**, selecione **Atividade Vazia** e selecione **Avançar**. 
3. Na página **Configurar o projeto**, execute as seguintes etapas: 
    1. Insira um **nome** para o aplicativo.
    2. Especifique um local para salvar os arquivos de projeto. 
    3. Selecione **Concluir**. 

        ![Configurar o projeto](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Crie um projeto do Firebase que ofereça suporte ao FCM

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Configurar um hub de notificação

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Definir as configurações do Firebase Cloud Messaging para o hub

1. Selecione **Google (GCM/FCM)** em **Configurações** no menu à esquerda. 
2. Cole a **chave do servidor** do projeto do FCM salva anteriormente. 
3. Selecione **Salvar** na barra de ferramentas. 

    ![Hubs de Notificação do Azure – Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Você verá uma mensagem nos alertas indicando que os hubs de notificação foram atualizados com êxito. O botão **Salvar** está desabilitado. 

Agora, o hub de notificação está configurado para funcionar com o Firebase Cloud Messaging, e você tem as cadeias de conexão para registrar o aplicativo para receber e enviar notificações por push.

## <a id="connecting-app"></a>Conectar seu aplicativo ao hub de notificação

### <a name="add-google-play-services-to-the-project"></a>Adicionar serviços do Google Play ao projeto

[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Como adicionar bibliotecas dos Hubs de Notificação do Azure

1. No arquivo `Build.Gradle` para o **aplicativo**, adicione as linhas a seguir à seção **dependencies**.

    ```text
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. Adicione o seguinte repositório após a seção **dependências** .

    ```text
    repositories {
        maven {
            url "http://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Adicionar suporte ao Google Firebase

1. No arquivo `Build.Gradle` do **aplicativo**, adicione as linhas a seguir à seção **dependencies**, caso elas ainda não existam. 

    ```text
    implementation 'com.google.firebase:firebase-core:16.0.0'
    ```

2. Adicione o plug-in a seguir ao final do arquivo, caso ele ainda não exista. 

    ```text
    apply plugin: 'com.google.gms.google-services'
    ```

### <a name="updating-the-androidmanifestxml"></a>Atualização do arquivo AndroidManifest.xml

1. Para dar suporte ao FCM, implemente um serviço de escuta de ID da Instância em seu código, que será usado para [obter tokens do registro](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) usando a [API FirebaseInstanceId do Google](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId). Neste tutorial, o nome da classe é `MyInstanceIDService`.

    Adicione a seguinte definição de serviço ao arquivo Androidmanifest.xml, dentro da marcação `<application>` .

    ```xml
    <service android:name=".MyInstanceIDService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. Depois de receber seu token de registro FCM da API FirebaseInstanceId, use-o para [registrar com o Hub de Notificação do Azure](notification-hubs-push-notification-registration-management.md). Você oferecerá suporte a esse registro em segundo plano usando um `IntentService` chamado `RegistrationIntentService`. Esse serviço também é responsável por atualizar o token de registro do FCM.

    Adicione a seguinte definição de serviço ao arquivo Androidmanifest.xml, dentro da marcação `<application>` .

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    ```

3. Defina também um receptor para receber notificações. Adicione a seguinte definição do receptor ao arquivo AndroidManifest.xml, dentro da marcação `<application>` . 

    ```xml
    <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <category android:name="<your package name>" />
        </intent-filter>
    </receiver>
    ```

    > [!IMPORTANT]
    > Substitua o espaço reservado `<your package NAME>` pelo nome do pacote real mostrado na parte superior do arquivo `AndroidManifest.xml`.
4. Selecione **Sincronizar Agora** na barra de ferramentas.
5. Adicione as permissões necessárias relacionadas ao FCM a seguir **abaixo** da tag `</application>`.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="adding-code"></a>Como adicionar um código

1. Na Exibição de Projeto, expanda **app** > **src** > **main** > **java**. Clique na pasta do seu pacote em **java**, clique em **Novo** e então clique em **Classe Java**. Insira `NotificationSettings` para o nome e selecione **OK**.

    Atualize esses três espaços reservados no código a seguir para a classe `NotificationSettings`:

   * **HubListenConnectionString**: a cadeia de conexão **DefaultListenAccessSignature** do hub. Copie essa cadeia de conexão clicando em **Políticas de Acesso** no hub no [portal do Azure].
   * **HubName**: use o nome do hub de notificação que aparece na página do hub no [Portal do Azure].

     `NotificationSettings` :

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

    > [!IMPORTANT]
    > Insira o **nome** e a **DefaultListenSharedAccessSignature** do hub de notificação antes de prosseguir. 
2. Adicione outra classe chamada `MyInstanceIDService`. Essa classe é sua implementação do serviço de escuta de ID da Instância.

    O código para essa classe chama `IntentService` para [atualizar o token FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) em segundo plano.

    ```java
    import android.content.Intent;
    import android.util.Log;
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class MyInstanceIDService extends FirebaseInstanceIdService {

        private static final String TAG = "MyInstanceIDService";

        @Override
        public void onTokenRefresh() {

            Log.d(TAG, "Refreshing FCM Registration Token");

            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    };
    ```

3. Adicione outra classe nova ao projeto denominada `RegistrationIntentService`. Essa classe implementa a interface `IntentService` e processa a [atualização do token FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) e o [registro no hub de notificação](notification-hubs-push-notification-registration-management.md).

    Use o código a seguir para essa classe.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.firebase.iid.FirebaseInstanceId;
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
            String storedToken = null;

            try {
                String FCM_token = FirebaseInstanceId.getInstance().getToken();
                Log.d(TAG, "FCM Registration Token: " + FCM_token);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server,
                // otherwise your server should have already received the token.
                if (((regID=sharedPreferences.getString("registrationID", null)) == null)){

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                // Check if the token may have been compromised and needs refreshing.
                else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete registration", e);
                // If an exception happens while fetching the new token or updating our registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

4. Em sua classe `MainActivity`, adicione as seguintes instruções `import` acima da declaração da classe.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

5. Adicione os membros a seguir à parte superior da classe. Use esses campos para [verificar a disponibilidade do Google Play Services, conforme recomendado pelo Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

6. Na classe `MainActivity`, adicione o método a seguir para verificar a disponibilidade do Google Play Services.

    ```java
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
    ```

7. Na classe `MainActivity`, adicione o seguinte código que verifica o Google Play Services antes de chamar o `IntentService` para obter seu token de registro do FCM e registrá-lo com o hub de notificação.

    ```java
    public void registerWithNotificationHubs()
    {
        if (checkPlayServices()) {
            // Start IntentService to register this application with FCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

8. No método `OnCreate` da classe `MainActivity`, adicione o código a seguir para começar o processo de registro quando a atividade for criada.

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        MyHandler.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

9. Para verificar o estado do aplicativo e informar o status em seu aplicativo, adicione esses métodos adicionais a `MainActivity`.

    ```java
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

    public void ToastNotify(final String notificationMessage) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                TextView helloText = (TextView) findViewById(R.id.text_hello);
                helloText.setText(notificationMessage);
            }
        });
    }
    ```

10. O método `ToastNotify` usa o controle *"Hello World"* `TextView` para informar de forma persistente o status e as notificações no aplicativo. No layout **res** -> **layout** -> **activity_main.xml**, adicione a ID a seguir a esse controle.

    ```java
    android:id="@+id/text_hello"
    ```

11. Em seguida, adicione uma subclasse para o receptor que você definiu no AndroidManifest.xml. Adicione outra classe nova ao projeto denominada `MyHandler`.

12. Adicione as seguintes instruções de importação na parte superior de `MyHandler.java`:

    ```java
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import android.support.v4.app.NotificationCompat;
    import com.microsoft.windowsazure.notifications.NotificationsHandler;    
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    ```

13. Adicione o código a seguir à classe `MyHandler`, tornando-a uma subclasse de `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Esse código substitui o método `OnReceive` para que o manipulador informe as notificações recebidas. O manipulador também envia a notificação por push ao gerenciador de notificações do Android usando o método `sendNotification()` . O método `sendNotification()` deve ser executado quando o aplicativo não está em execução e uma notificação é recebida.

    ```java
    public class MyHandler extends NotificationsHandler {
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;
    
        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");
            sendNotification(nhMessage);
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
        }
    
        private void sendNotification(String msg) {
    
            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);
    
            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(
                    ctx,
                    NOTIFICATION_CHANNEL_ID)
                    .setContentText(msg)
                    .setPriority(NotificationCompat.PRIORITY_HIGH)
                    .setSmallIcon(android.R.drawable.ic_popup_reminder)
                    .setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL);
    
            notificationBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
        }
    
        public static void createChannelAndHandleNotifications(Context context) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
                NotificationsManager.handleNotifications(context, "", MyHandler.class);
            }
        }
    }
    ```

14. No Android Studio, na barra de menus, clique em **Compilar** > **Recompilar Projeto** para garantir que não haja erros presentes no código. Se você receber um erro sobre o ícone `ic_launcher`, remova a instrução a seguir do arquivo AndroidManifest.xml. 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
15. Execute o aplicativo em seu dispositivo e verifique se ele se registra com êxito no hub de notificação.

    > [!NOTE]
    > O registro poderá falhar na primeira inicialização até que o método `onTokenRefresh()` do serviço de ID da instância seja chamado. A atualização deve iniciar um registro bem-sucedido com o hub de notificação.

    ![Êxito no registro do dispositivo](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Teste enviar notificação pelo hub de notificação

Você pode enviar notificações por push pelo [Portal do Azure] da seguinte maneira:

1. No portal do Azure, na página **Hub de Notificação** do hub de notificação, selecione **Envio de Teste** na seção **Solução de Problemas**.
3. Em **Plataformas**, selecione **Android**.
4. Selecione **Enviar**.  Você ainda não vê uma notificação no dispositivo Android porque não executou o aplicativo móvel nele. Depois de executar o aplicativo móvel, selecione **Enviar** novamente para ver a mensagem de notificação.
5. Consulte o **resultado** da operação na lista na parte inferior.

    ![Hubs de notificação do Azure - Testar Enviar](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Você verá a mensagem de notificação no dispositivo. 

    ![Mensagem de notificação no dispositivo](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Executar o aplicativo móvel no emulador
Para testar notificações por push em um emulador, verifique se a imagem de emulador dá suporte ao nível de API do Google que você escolheu para o aplicativo. Se a imagem não der suporte às APIs nativas do Google, você poderá obter a exceção **SERVICE\_NOT\_AVAILABLE**.

Além disso, verifique se você adicionou a conta do Google ao emulador em execução em **Configurações** > **Contas**. Caso contrário, suas tentativas de se registrar no GCM podem resultar na exceção **AUTHENTICATION\_FAILED**.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você usou Firebase Cloud Messaging para enviar notificações por push a dispositivos Android. Para saber como enviar notificações por push usando o Google Cloud Messaging, avance ao seguinte tutorial:

> [!div class="nextstepaction"]
>[Notificações por push para dispositivos Android usando o Google Cloud Messaging](notification-hubs-android-push-notification-google-gcm-get-started.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Portal do Azure]: https://portal.azure.com
