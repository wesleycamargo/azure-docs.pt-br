<properties 
	pageTitle="Adicionar notificações por push a seu aplicativo Xamarin iOS com o Serviço de Aplicativo do Azure" 
	description="Aprenda a usar o Serviço de Aplicativo do Azure para enviar notificações por push para o aplicativo Xamarin.iOS" 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="wesmc7777"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="08/22/2015" 
	ms.author="wesmc"/>

# Adicionar notificações por push a seu aplicativo Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Visão geral

Neste tutorial, você adicionará notificações por push ao projeto de [início rápido do Xamarin.iOS] para que sempre que um registro for inserido, uma notificação por push seja enviada. Este tutorial baseia-se no tutorial de [início rápido do Xamarin.iOS], que você deve concluir primeiro. Se você não usar o projeto baixado do início rápido do servidor, você deve adicionar o pacote de extensão de notificação por push ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

O [simulador de iOS não dá suporte a notificações de push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html); portanto, você deve usar um dispositivo físico com iOS. Você também precisará de uma [assinatura do programa de desenvolvedores da Apple](https://developer.apple.com/programs/ios/).

##Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você ainda não tiver uma conta, inscreva-se para uma avaliação do Azure e obtenha até 10 aplicativos móveis grátis. Você pode continuar usando-os até mesmo após o término de sua avaliação. Consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

* Um Mac com [Xamarin Studio] e [Xcode] v 4.4 ou posterior instalado. Observe que é mais fácil de executar seu aplicativo Xamarin.iOS em um Mac usando o Xamarin Studio. Você pode executar o aplicativo Xamarin.iOS usando o Visual Studio no computador com o Windows se quiser, mas é um pouco mais complicado porque você precisa se conectar a um MAC em rede. Se você estiver interessado em fazer isso, confira [Instalação do Xamarin.iOS no Windows].

* Um dispositivo físico iOS.

* Conclua o [tutorial de início rápido](../app-service-mobile-xamarin-ios-get-started.md).

## <a id="register"></a>Registre o aplicativo para obter notificações por push

[AZURE.INCLUDE [Habilitar Notificações por push da Apple](../../includes/enable-apple-push-notifications.md)]

## Configurar o Azure para enviar notificações por push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>Atualize o projeto de servidor para enviar notificações por push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a name="publish-the-service"></a>Implante o projeto de servidor no Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## <a name="configure-app"></a>Configure seu projeto Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push"></a>Adicionar notificações de push para seu aplicativo

1. Em **QSTodoService**, adicione a seguinte propriedade para que **AppDelegate** possa adquirir o cliente móvel:
        
            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }

1. Adicione a seguinte declaração `using` no topo do arquivo **AppDelegate.cs**.

        using Microsoft.WindowsAzure.MobileServices;

2. Em **AppDelegate**, substitua o evento **FinishedLaunching**:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert 
                | UIUserNotificationType.Badge 
                | UIUserNotificationType.Sound, 
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. No mesmo arquivo, substitua o evento **RegisteredForRemoteNotifications**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken);
        }

4. Então substitua o evento **DidReceivedRemoteNotification**:

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

Seu aplicativo foi atualizado para oferecer suporte a notificações de push.

## <a name="test"></a>Testar notificações por push no seu aplicativo

1. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo em um dispositivo compatível com iOS; em seguida, clique em **OK** para aceitar as notificações por push.
	
	> [AZURE.NOTE]Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

2. No aplicativo, digite uma tarefa e, em seguida, clique no ícone do sinal de adição (**+**).

3. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.

4. Repita a etapa 2 e imediatamente feche o aplicativo e verifique se uma notificação é exibida.

Este tutorial foi concluído com êxito.

<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Instalação do Xamarin.iOS no Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333


 

<!---HONumber=Nov15_HO1-->