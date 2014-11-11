<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Introdução às notificações por push nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-javascript-backend-ios-get-started-push" title="iOS" class="current">iOS</a><a href="/pt-br/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android">Android</a><!---<a href="/pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>--></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="Back-end do .NET" >Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações por push para um aplicativo iOS. Neste tutorial, você adicionará as notificações por push usando o APNS (Serviço de Notificação por Push da Apple) ao projeto de início rápido. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.

> [WACOM.NOTE]Este tutorial demonstra a integração dos Serviços Móveis com Hubs de Notificação, que é o modo como você envia notificações por push de seu serviço móvel. Se estiver usando um serviço móvel antigo usando push herdado que ainda não foi atualizado para usar Hubs de Notificação, *recomendamos a atualização* como parte deste tutorial. Se escolher não atualizar agora, siga esta versão do tutorial: [Introdução às notificações por push (herdado)][Introdução às notificações por push (herdado)].

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1.  [Gerar a solicitação de assinatura de certificado][Gerar a solicitação de assinatura de certificado]
2.  [Registrar seu aplicativo e habilitar as notificações por push][Registrar seu aplicativo e habilitar as notificações por push]
3.  [Criar um perfil de provisionamento para o aplicativo][Criar um perfil de provisionamento para o aplicativo]
4.  [Configurar os Serviços Móveis][Configurar os Serviços Móveis]
5.  [Adicionar notificações por push ao aplicativo][Adicionar notificações por push ao aplicativo]
6.  [Atualizar scripts para enviar notificações por push][Atualizar scripts para enviar notificações por push]
7.  [Inserir dados para receber notificações][Inserir dados para receber notificações]

Este tutorial exige o seguinte:

-   [SDK do iOS dos Serviços Móveis][SDK do iOS dos Serviços Móveis]
-   [XCode 4.5][XCode 4.5]
-   Um dispositivo compatível com iOS 6.0 (ou versão posterior)
-   Associação no Programa de Desenvolvedores de iOS

> [WACOM.NOTE] Devido aos requisitos de configuração das notificações por push, você deve implantar e testar as notificações por push em um dispositivo compatível com o iOS (iPhone ou iPad), em vez de usar o emulador.

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

[WACOM.INCLUDE [Habilitar Notificações por Push para Apple](../includes/enable-apple-push-notifications.md)]

## Configurar Serviços Móveis para enviar solicitações de push

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## Adicionar notificações de push para seu aplicativo

1.  Em QSAppDelegate.m, substitua o método de manipulador a seguir na implementação:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2.  Em QSAppDelegate.m, adicione o método de manipulador a seguir na implementação:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            client.push.registerNative(deviceToken, @”uniqueTag”);
        }

3.  Em QSAppDelegate.m, adicione o método de manipulador a seguir na implementação:

        // Handle any failure to register. 
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

4.  Em QSAppDelegate.m, adicione o método de manipulador a seguir na implementação:

        // Because alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

5.  Em QSTodoListViewController.m, importe o arquivo QSAppDelegate.h para poder usar o representante para obter o token do dispositivo:

        #import "QSAppDelegate.h"

6.  Em QSTodoListViewController.m, modifique a ação **(IBAction)onAdd**, localizando a linha a seguir:

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 

Substitua isso por este código:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : [[NSString alloc] initWithData:delegate.deviceToken encoding:NSUTF8StringEncoding]
        };

Isso adicionará uma referência a **QSAppDelegate** para obter o token do dispositivo e modificará a carga de solicitação para incluir esse token de dispositivo.

> [WACOM.NOTE] Adicione esse código antes da chamada do método **addItem**.

Seu aplicativo foi atualizado para oferecer suporte a notificações de push.

## Atualizar o script de inserção registrados no Portal de Gerenciamento

1.  No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**.

    ![][0]

2.  Em **todoitem**, clique na guia **Script** e selecione **Inserir**.

    ![][1]

    Isso exibe a função que é chamada quando ocorre uma inserção na tabela **TodoItem**.

3.  Substitua a função de inserção com o seguinte código e **Salvar**:

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate push notifications
            setTimeout(function() {
                push.apns.send(null, {
                    alert: "Alert: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    Isso registra um novo script de inserção, que usa o [objeto apns][objeto apns] para enviar uma notificação por push (o texto inserido) para o dispositivo fornecido na solicitação de inserção.

    > [WACOM.NOTE] Esse script atrasa o envio da notificação para que você tenha tempo de fechar o aplicativo para receber uma notificação por push.

## Testar notificações por push no seu aplicativo

1.  Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo em um dispositivo compatível com iOS; em seguida, clique em **OK** para aceitar as notificações por push

    ![][2]

    > [WACOM.NOTE] Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

2.  No aplicativo, digite um texto significativo, como *Uma nova tarefa de Serviços Móveis* e clique no ícone do sinal de mais (**+**).

    ![][3]

3.  Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.

    ![][4]

4.  Repita a etapa 2 e feche o aplicativo imediatamente; em seguida, verifique se a notificação por push a seguir é mostrada.

    ![][5]

Este tutorial foi concluído com êxito.

## Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo iOS para usar Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir um dos seguintes tutoriais:

-   [Enviar notificações por push a usuários autenticados][Enviar notificações por push a usuários autenticados]
    Saiba como usar marcas para enviar notificações por push de um Serviço Móvel somente para um usuário autenticado.

-   [Enviar notificações de difusão aos assinantes][Enviar notificações de difusão aos assinantes]
    Saiba como os usuários podem registrar e receber notificações por push para categorias de interesse.
    <!--- + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->
    Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

-   [Introdução aos dados][Introdução aos dados]
    Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][Introdução à autenticação]
    Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando Serviços Móveis.

-   [O que são Hubs de Notificação?][O que são Hubs de Notificação?]
    Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

-   [Referência conceitual de como do Objective-C de Serviços Móveis][Referência conceitual de como do Objective-C de Serviços Móveis]
    Saiba mais sobre como usar os Serviços Móveis com Objective-C e iOS.

-   [Referência de script de servidor dos Serviços Móveis][Referência de script de servidor dos Serviços Móveis]
    Saiba mais sobre como implementar lógica de negócios em seu serviço móvel.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs.   -->

  [Gerar a solicitação de assinatura de certificado]: #certificates
  [Registrar seu aplicativo e habilitar as notificações por push]: #register
  [Criar um perfil de provisionamento para o aplicativo]: #profile
  [Configurar os Serviços Móveis]: #configure
  [Adicionar notificações por push ao aplicativo]: #add-push
  [Atualizar scripts para enviar notificações por push]: #update-scripts
  [Inserir dados para receber notificações]: #test
  [SDK do iOS dos Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-ios-get-started
  [Habilitar Notificações por Push para Apple]: ../includes/enable-apple-push-notifications.md
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  [0]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
  [objeto apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [4]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [5]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [Enviar notificações por push a usuários autenticados]: /pt-br/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/
  [Enviar notificações de difusão aos assinantes]: /pt-br/documentation/articles/notification-hubs-ios-send-breaking-news/
  [Introdução aos dados]: /pt-br/documentation/articles/mobile-services-ios-get-started-data
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-ios-get-started-users
  [Referência conceitual de como do Objective-C de Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
