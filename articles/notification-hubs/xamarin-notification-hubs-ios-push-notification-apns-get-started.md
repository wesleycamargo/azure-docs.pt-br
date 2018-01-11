---
title: "Introdução aos Hubs de Notificação do Azure para aplicativos do Xamarin.iOS | Microsoft Docs"
description: "Neste tutorial, você aprende a usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo Xamarin iOS."
services: notification-hubs
keywords: "notificações por push do IOS, mensagens por push, notificações por push, mensagem por push"
documentationcenter: xamarin
author: jwhiteDev
manager: kpiteira
editor: 
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: edb48cd8de9b1f7357c40cea73fa4b9a422cb8fa
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-xamarinios-apps"></a>Introdução aos Hubs de Notificação do Azure para aplicativos do Xamarin.iOS
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Visão geral
> [!NOTE]
> Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).
> 
> 

Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo iOS. Neste tutorial, você cria um aplicativo Xamarin.iOS em branco que recebe notificações por push usando o [Apple Push Notification Service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html). 

Ao finalizar, você poderá usar seu hub de notificação para transmitir notificações por push a todos os dispositivos que executam seu aplicativo. O código concluído está disponível na amostra do [aplicativo NotificationHubs][GitHub].

Este tutorial demonstra o cenário de transmissão de mensagens por push simples com Hubs de Notificação.

## <a name="prerequisites"></a>pré-requisitos
Este tutorial exige o seguinte:

* Versão mais recente do [Xcode][Install Xcode]
* Um dispositivo compatível com o iOS 10 (ou versão posterior)
* [Programa de Desenvolvedores de iOS](https://developer.apple.com/programs/) 
* [Visual Studio para Mac]
  
  > [!NOTE]
  > Devido aos requisitos de configuração das notificações por push do iOS, você deve implantar e testar o aplicativo de exemplo em um dispositivo iOS físico (iPhone ou iPad) em vez do simulador.
  > 
  > 

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre Hubs de Notificação para aplicativos Xamarin.iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Configurar o Hub de Notificação para notificações por push do iOS
Esta seção guia você pelas etapas para criar um novo hub de notificação e a configurar a autenticação com APNS usando o certificado push **.p12** que você criou anteriormente. Se você quiser usar um hub de notificação já criado, ignore a etapa 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>Clique no botão <b>Serviços de Notificação</b> e selecione <b>Apple (APNS)</b>. Certifique-se de selecionar <b>Certificado</b>, clique no ícone de arquivo e selecione o arquivo <b>.p12</b> que você exportou anteriormente. Especifique também a senha correta.</p>

<p>Selecione o modo de <b>Área Restrita</b>, pois se trata de desenvolvimento. Use a <b>Produção</b> apenas se quiser enviar notificações por push aos usuários que adquiriram seu aplicativo na loja.</p>
</li>
</ol>

&emsp;&emsp;&emsp;&emsp;![Configurar o APNS no Portal do Azure][6]

&emsp;&emsp;&emsp;&emsp;![Configurar certificação do APNS no Portal do Azure][7]

Seu hub de notificação agora está configurado para funcionar com o APNS e você tem as cadeias de conexão para registrar seu aplicativo e enviar notificações por push.

## <a name="connect-your-app-to-the-notification-hub"></a>Conectar seu aplicativo ao hub de notificação
#### <a name="create-a-new-project"></a>Criar um novo projeto
1. No Visual Studio, crie um novo projeto do iOS e selecione o modelo **Aplicativo de Modo de Exibição Único** e clique em **Avançar**
   
     ![Visual Studio – Selecione o Tipo de Aplicativo][31]

2. Digite o Nome do Aplicativo e o identificador da organização e clique em **Avançar**, depois em **Criar**

3. Na exibição de solução, clique duas vezes em *Into.plist* e em **Identidade** e verifique se o identificador de pacote corresponde ao usado ao criar o perfil de provisionamento. Em **assinatura**, verifique se sua conta de desenvolvedor está selecionada em **Equipe**, se "Gerenciar assinatura automaticamente" está selecionado e se seu certificado de autenticação e perfil de provisionamento são selecionados automaticamente.

    ![Configuração de aplicativo do Visual Studio-iOS][32]

4. Adicione o pacote de Mensagens do Azure. Na exibição Soluções, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Adicionar Pacotes NuGet**. Pesquise por **Xamarin.Azure.NotificationHubs.iOS** e adicione o pacote ao seu projeto.

5. Adicione um novo arquivo à sua classe, nomeie-o **Constants.cs** e adicione as seguintes variáveis e substitua os espaços reservados da cadeia de caracteres literal pelo *nome do hub* e a *DefaultListenSharedAccessSignature* que você anotou anteriormente.
   
    ```csharp
        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
    ```

6. Em **AppDelegate.cs**, adicione a seguinte instrução using:
   
    ```csharp
        using WindowsAzure.Messaging;
    ```

7. Declarar uma instância de **SBNotificationHub**:
   
    ```csharp
        private SBNotificationHub Hub { get; set; }
    ```

8. Em **AppDelegate.cs** atualize **FinishedLaunching()** para que ele corresponda ao seguinte:
   
    ```csharp
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());
   
                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }
   
            return true;
        }
    ```

9. Substituir o método **RegisteredForRemoteNotifications()** em **AppDelegate.cs**:
   
    ```csharp
        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);
   
            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }
   
                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }
    ```

10. Substituir o método **ReceivedRemoteNotification()** em **AppDelegate.cs**:
   
    ```csharp
        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
    ```

11. Criar o seguinte método **ProcessNotification()** em **AppDelegate.cs**:
   
    ```csharp
        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
   
                string alert = string.Empty;
   
                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();
   
                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }
    ```
   > [!NOTE]
   > Você pode optar por substituir **FailedToRegisterForRemoteNotifications()** para lidar com situações como a falta de conexão de rede. Isso é particularmente importante quando o usuário pode iniciar o aplicativo no modo offline (por exemplo, Avião) e você deseja manipular cenários de envio de mensagens por push específicos para o aplicativo.
  

12. Execute o aplicativo em seu dispositivo.

## <a name="sending-test-push-notifications"></a>Enviar Notificações por Push de Teste
Você pode testar o recebimento de notificações no aplicativo com a opção *Envio de Teste* no [Portal do Azure]. Isso envia uma notificação por push de teste para seu dispositivo.

![Portal do Azure – Envio de Teste][30]

As notificações por push normalmente são enviadas em um serviço de back-end como Aplicativos Móveis ou ASP.NET usando uma biblioteca compatível. Se uma biblioteca não estiver disponível para o seu back-end, você também poderá usar a API REST diretamente para enviar mensagens de notificação.

Como próxima etapa para envio de notificações de back-end do ASP.NET, recomendamos o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários](notification-hubs-aspnet-backend-ios-apple-apns-notification.md). No entanto, as abordagens a seguir podem ser usadas para o envio de notificações:

Aqui está uma lista de alguns outros tutoriais que talvez você queira examinar para o envio de notificações:
* Interface REST: você pode dar suporte à notificação por push em qualquer plataforma de back-end usando a [interface REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).
* **SDK do .NET dos Hubs de Notificações do Microsoft Azure**: no Gerenciador de Pacotes do Nuget para o Visual Studio, execute [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
* Node.js: [Como usar os Hubs de Notificação de Node.js](notification-hubs-nodejs-push-notification-tutorial.md).
* Java/PHP: para obter um exemplo de como enviar notificações por push usando as APIs REST, confira "Como usar os Hubs de Notificação do Java/PHP" ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

## <a name="next-steps"></a>Próximas etapas
Neste exemplo simples, você envia notificações por push para todos os seus dispositivos iOS. Para selecionar usuários de destino específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá ler [Usar Hubs de Notificação para enviar notícias mais recentes]. Saiba mais sobre como usar os Hubs de Notificação em [Diretrizes dos Hubs de Notificação] e em [Instruções sobre Hubs de Notificação para iOS].

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png



<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Instruções sobre Hubs de Notificação para iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio para Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Usar Hubs de Notificação para enviar notificações por push aos usuários]: /manage/services/notification-hubs/notify-users-aspnet
[Usar Hubs de Notificação para enviar notícias mais recentes]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]:https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Portal do Azure]: https://portal.azure.com
