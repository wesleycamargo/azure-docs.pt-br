---
title: Notificações por push para aplicativos Xamarin.Android usando Hubs de Notificação do Azure | Microsoft Docs
description: Neste tutorial, você aprende a usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo Xamarin Android.
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: f75671e2e5511054f3db550a8c24e62d031492c3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-push-notifications-to-xamarinandroid-apps-using-azure-notification-hubs"></a>Tutorial: Notificações por push para aplicativos Xamarin.Android usando Hubs de Notificação do Azure
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Visão geral
Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo Xamarin.Android. Você cria um aplicativo Xamarin.Android em branco que recebe notificações por push usando o FCM (Firebase Cloud Messaging). Você usa seu hub de notificação para transmitir notificações por push a todos os dispositivos que executam seu aplicativo. O código concluído está disponível na amostra do [aplicativo NotificationHubs][GitHub].

Neste tutorial, você deve executar as seguintes etapas:

> [!div class="checklist"]
> * Criar um projeto de Firebase e habilitar Firebase Cloud Messaging
> * Criar um hub de notificação
> * Criar um aplicativo Xamarin.Android e conectar-se ao hub de notificação
> * Enviar notificações por push de teste do portal do Azure

## <a name="prerequisites"></a>pré-requisitos

- **Assinatura do Azure**. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- [Visual Studio com Xamarin] no Windows ou [Visual Studio para Mac] no OS X.
- Conta ativa do Google

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Criar um projeto de Firebase e habilitar Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>Criar um hub de notificação
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-settings-for-the-notification-hub"></a>Configurar o GCM para o hub de notificação

1. Selecione **Google (GCM)** na seção **CONFIGURAÇÕES DE NOTIFICAÇÃO**. 
2. Insira a **chave do servidor herdado** indicado para baixo do Google Firebase Console. 
3. Selecione **Salvar** na barra de ferramentas. 

    ![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Seu hub de notificação está configurado para funcionar com o FCM e você tem as cadeias de conexão para registrar o aplicativo para receber notificações e enviar notificações por push.

## <a name="create-xamainandroid-app-and-connect-it-to-notification-hub"></a>Criar um aplicativo Xamarin.Android e conectar-se ao hub de notificação

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Criar um projeto do Visual Studio e adicionar pacotes NuGet
1. No Visual Studio, aponte para **Arquivo**, selecione **Novo** e **Projeto**. 
   
      ![Visual Studio – Criar novo projeto Android](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog.png)
2. Na janela do **Gerenciador de Soluções**, expanda **Propriedades**e clique em **AndroidManifest.xml**. Atualize o nome do pacote para corresponder ao nome do pacote inserido ao adicionar Firebase Cloud Messaging ao seu projeto no Google Firebase Console. 

      ![Nome do pacote no GMC](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
3. Clique o botão direito do mouse no projeto e selecione **Gerenciar Pacotes NuGet...**. 
4. Selecione a guia **Procurar**. Pesquise por **Xamarin.GooglePlayServices.Base**. Selecione **Xamarin.GooglePlayServices.Base** na lista de resultados. Em seguida, selecione **Instalar**. 

      ![Google Play Services NuGet](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
5. Na janela do **Gerenciador de Pacotes do NuGet**, pesquise por **Xamarin.Firebase.Messaging**. Selecione **Xamarin.Firebase.Messaging** na lista de resultados. Em seguida, selecione **Instalar**. 
6. Agora, pesquise por **Xamarin.Azure.NotificationHubs.Android**. Selecione **Xamarin.Azure.NotificationHubs.Android** na lista de resultados. Em seguida, selecione **Instalar**. 

### <a name="add-the-google-services-json-file"></a>Adicione o arquivo JSON de Serviços do Google

1. Cópia **services.json google** que você baixou do Google Firebase Console para a pasta do projeto.
2. Adicione **google-services.json** ao projeto.
3. Selecione **google services.json** na janela **Gerenciador de Soluções**.
4. No painel **Propriedades**, defina a ação de compilação como **GoogleServicesJson**. Se você não vir **GoogleServicesJson**, feche o Visual Studio, reinicialize-o, reabra o projeto e tente novamente. 

      ![Ação de compilação GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Configurar hubs de notificação em seu projeto

#### <a name="registering-with-firebase-cloud-messaging"></a>Registrando com o Firebase Cloud Messaging

Abra o arquivo **AndroidManifest.xml** e insira os seguintes elementos `<receiver>` no elemento `<application>`:

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

1. Colete as informações a seguir para o aplicativo Android e o hub de notificação:
   
   * **Cadeia de conexão de escuta**: no painel, no [portal do Azure], escolha **Exibir cadeias de conexão**. Copiar a sequência de conexão *DefaultListenSharedAccessSignature* para esse valor.
   * **Nome do hub**: nome do seu hub no [portal do Azure]. Por exemplo, *mynotificationhub2*.
     
2. Clique com o botão direito do mouse em **projeto** na janela do **Gerenciador de Soluções**, aponte para **Adicionar** e selecione **Classe**. 
4. Crie uma classe **Constants.cs** para seu projeto de Xamarin e defina os valores de constantes a seguir na classe. Substitua os espaços reservados pelos seus valores.
    
    ```csharp
        public static class Constants
        {
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
        }
    ```
3. Adicionar as seguintes instruções à **MainActivity.cs**:
   
    ```csharp
        using Android.Util;
    ```
4. Adicione uma variável de instância a **MainActivity.cs** que será usada para mostrar uma caixa de diálogo de alerta quando o aplicativo estiver em execução:
   
    ```csharp
        public const string TAG = "MainActivity";
    ```
5. Adicione o seguinte código a `OnCreate` em **MainActivity.cs** após `base.OnCreate(savedInstanceState)`:

    ```csharp   
        if (Intent.Extras != null)
        {
            foreach (var key in Intent.Extras.KeySet())
            {
                if(key!=null)
                {
                    var value = Intent.Extras.GetString(key);
                    Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
                }
            }
        }
    ```
7. Criar uma nova classe, **MyFirebaseIIDService** como você criou a classe **Constantes**. 
8. Adicione as seguintes instruções de uso a **MyFirebaseIIDService.cs**:
   
    ```csharp
    using Android.Util;
    using WindowsAzure.Messaging;
    using Firebase.Iid;
    ```

9. Em **MyFirebaseIIDService.cs**, adicione a seguinte declaração de **classe**, e faça com que sua classe herde de **FirebaseInstanceIdService**:
   
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class MyFirebaseIIDService : FirebaseInstanceIdService
    ```
10. Em **MyFirebaseIIDService.cs**, adicione o seguinte código:
   
    ```csharp
        const string TAG = "MyFirebaseIIDService";
        NotificationHub hub;

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "FCM token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                      Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```
11. Crie uma nova classe para o seu projeto e denomine-o **MyFirebaseMessagingService**.
12. Adicione o seguinte usando instruções para **MyFirebaseMessagingService.cs**.
    
    ```csharp
        using Android.Util;
        using Firebase.Messaging;
    ```
13. Adicione o seguinte acima da declaração de classe e faça com que a classe herde de **FirebaseMessagingService**:
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseMessagingService : FirebaseMessagingService
    ```    
14. Adicione o seguinte código a **MyFirebaseMessagingService.cs**:
    
    ```csharp
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if(message.GetNotification()!= null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else 
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());

            }

        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                        .SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetContentIntent(pendingIntent);

            var notificationManager = NotificationManager.FromContext(this);

            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```
15. **Compile** o seu novo projeto. 
16. **Executar** aplicativo no dispositivo ou emulador carregado

## <a name="send-test-notification-from-the-azure-portal"></a>Enviar notificação por push de teste do portal do Azure
Você pode testar o recebimento de notificações no aplicativo com a opção *Envio de Teste* no [portal do Azure]. Isso envia uma notificação por push de teste para seu dispositivo.

![Portal do Azure – Envio de Teste](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

As notificações por push são geralmente enviadas em um serviço back-end, como Serviços Móveis ou ASP.NET, por meio de uma biblioteca compatível. Se uma biblioteca não estiver disponível para o seu back-end, você também poderá usar a API REST diretamente para enviar mensagens de notificação.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você envia notificações de transmissão para todos os seus dispositivos Android registrados com o back-end. Para saber como enviar notificações por push para dispositivos Android específicos, vá para o tutorial a seguir: 

> [!div class="nextstepaction"]
>[Notificações por push para dispositivos específicos](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)


<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio com Xamarin]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Visual Studio para Mac]: https://www.visualstudio.com/vs/visual-studio-mac/

[portal do Azure]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: /manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
