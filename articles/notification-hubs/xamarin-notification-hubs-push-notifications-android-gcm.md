---
title: "Introdução aos Hubs de Notificação para aplicativos Xamarin.Android | Microsoft Docs"
description: "Neste tutorial, você aprende a usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo Xamarin Android."
author: jwhitedev
manager: kpiteira
editor: 
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 7f978ecd128115e5f2fe562da46d8b29324e3d04
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="get-started-with-notification-hubs-for-xamarinandroid-apps"></a>Introdução aos Hubs de Notificação para aplicativos Xamarin.Android
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Visão geral
Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo Xamarin.Android. Você criará um aplicativo Xamarin.Android em branco que recebe notificações por push usando o FCM (Firebase Cloud Messaging). Ao finalizar, você poderá usar seu hub de notificação para transmitir notificações por push a todos os dispositivos que executam seu aplicativo. O código concluído está disponível na amostra do [aplicativo NotificationHubs][GitHub].

Este tutorial demonstra o cenário de transmissão simples usando Hubs de Notificação.

## <a name="before-you-begin"></a>Antes de começar
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

O código completo para este tutorial pode ser encontrado no GitHub [aqui][GitHub].

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial exige o seguinte:

* [Visual Studio com Xamarin] no Windows ou [Visual Studio para Mac] no OS X.
* Conta ativa do Google

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre Hubs de Notificação para aplicativos Xamarin.Android.

> [!IMPORTANT]
> Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).
> 
> 

## <a name="enable-firebase-cloud-messaging"></a>Habilitar o Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-your-notification-hub"></a>Configurar seu Hub de Notificação
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li><p>Escolha a guia <b>Configurar</b> na parte superior, insira o valor de <b>Chave de API</b> que você obteve na etapa anterior e selecione <b>Salvar</b>.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Seu hub de notificação está configurado para funcionar com o FCM e você tem as cadeias de conexão para registrar o aplicativo para receber notificações e enviar notificações por push.

## <a name="connect-your-app-to-the-notification-hub"></a>Conectar seu aplicativo ao hub de notificação
Primeiro, crie um novo projeto. 

1. No Visual Studio, escolha **Nova Solução** > **Aplicativo Android** e, em seguida, selecione **Avançar**.
   
      ![Visual Studio – Criar novo projeto Android][22]

2. Insira o **Nome do Aplicativo** e o **Identificador**. Escolha as **Plataformas de Destino** às quais você quer dar suporte e, em seguida, escolha **Avançar** e **Criar**.
   
      ![Visual Studio – Configuração do aplicativo Android][23]

    Isso cria um novo projeto Android.

3. Abra as propriedades do projeto clicando com o botão direito em seu novo projeto no modo de exibição Solução e escolha **Opções**. Selecione o item **Aplicativo Android** na seção **Build**.
   
    Certifique-se de que a primeira letra de seu **nome do pacote** esteja em minúscula.
   
   > [!IMPORTANT]
   > A primeira letra do nome do pacote deve estar em minúscula. Caso contrário, você receberá erros de manifesto do aplicativo ao registrar o aplicativo para notificações por push abaixo.
   > 
   > 
   
      ![Visual Studio – Opções de projeto do Android][24]
4. Opcionalmente, defina a **Versão mínima do Android** como outro Nível de API.
5. Opcionalmente, defina a **Versão de destino do Android** para outra versão de API que você queira ter como destino (deve ser API nível 8 ou superior).
6. Clique em **OK** e feche a caixa de diálogo Opções do Projeto.

### <a name="add-the-required-packages-to-your-project"></a>Adicionar os pacotes obrigatórios ao projeto

1. Clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Adicionar Pacotes NuGet**
2. Procure por **Xamarin.Azure.NotificationHubs.Android** e adicione-o ao projeto.
3. Procure por **Xamarin.Firebase.Messaging** e adicione-o ao projeto.

### <a name="set-up-notification-hubs-in-your-project"></a>Configurar hubs de notificação em seu projeto
1. Colete as informações a seguir para o aplicativo Android e o hub de notificação:
   
   * **Cadeia de conexão de escuta**: no painel, no [portal do Azure], escolha **Exibir cadeias de conexão**. Copiar a sequência de conexão *DefaultListenSharedAccessSignature* para esse valor.
   * **Nome do hub**: esse é o nome do seu hub no [portal do Azure]. Por exemplo, *mynotificationhub2*.
     
2. Crie uma classe **Constants.cs** para seu projeto de Xamarin e defina os valores de constantes a seguir na classe. Substitua os espaços reservados pelos seus valores.
    
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

6. Clique com o botão direito do mouse e adicione o `google-services.json` baixado do projeto Firebase anteriormente. Clique com o botão direito do mouse no arquivo adicionado e defina a ação de compilação como `GoogleServicesJson`

    ![Visual Studio – Configurar google-services.json][25]

7. Crie uma nova classe, **MyFirebaseIIDService**.

8. Adicione as seguintes instruções de uso a **MyFirebaseIIDService.cs**:
   
    ```csharp
        using System;
        using Android.App;
        using Firebase.Iid;
        using Android.Util;
        using WindowsAzure.Messaging;
        using System.Collections.Generic;
    ```

9. Em **MyFirebaseIIDService.cs**, adicione o seguinte acima da declaração **classe**, e faça com que sua classe herde de **FirebaseInstanceIdService**:
   
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
        using System;
        using System.Linq;
        using Android;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Firebase.Messaging;
    ```

13. Adicione o seguinte acima da declaração de classe e faça com que a classe herde de **FirebaseMessagingService**:
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseIIDService : FirebaseMessagingService
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

15. Executar aplicativo no dispositivo ou emulador carregado

## <a name="send-notifications-from-the-portal"></a>Enviar notificações do portal
Você pode testar o recebimento de notificações no aplicativo com a opção *Envio de Teste* no [portal do Azure]. Isso envia uma notificação por push de teste para seu dispositivo.

![Portal do Azure – Envio de Teste][30]

As notificações por push são geralmente enviadas em um serviço back-end, como Serviços Móveis ou ASP.NET, por meio de uma biblioteca compatível. Se uma biblioteca não estiver disponível para o seu back-end, você também poderá usar a API REST diretamente para enviar mensagens de notificação.

Veja a seguir uma lista de outros tutoriais que talvez você queira examinar para o envio de notificações:

* ASP.NET: confira [Usar Hubs de Notificação para enviar notificações por push aos usuários].
* SDK do Java para Hubs de Notificação do Azure: confira [Como usar os Hubs de Notificação do Java](notification-hubs-java-push-notification-tutorial.md) para enviar notificações do Java. Isso foi testado no Eclipse para desenvolvimento no Android.
* PHP: confira [Como usar Hubs de Notificação do PHP](notification-hubs-php-push-notification-tutorial.md).

## <a name="next-steps"></a>Próximas etapas
Neste exemplo simples, você envia notificações para todos os seus dispositivos Android. Para selecionar usuários de destino específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá ler [Usar Hubs de Notificação para enviar notícias mais recentes]. Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação] e em [Instruções sobre Hubs de Notificação para Android].

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
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Instruções sobre Hubs de Notificação para Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Usar Hubs de Notificação para enviar notificações por push aos usuários]: /manage/services/notification-hubs/notify-users-aspnet
[Usar Hubs de Notificação para enviar notícias mais recentes]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
