<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app (legacy push)." metaCanonical="http://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Introdução às notificações por push nos Serviços Móveis (envio herdado)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="C# da Windows Store">C# da Windows Store</a>
    <a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="JavaScript da Windows Store">JavaScript da Windows Store</a>
    <a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/pt-br/documentation/articles/mobile-services-ios-get-started-push" title="iOS" class="current">iOS</a>
    <a href="/pt-br/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
    <a href="/pt-br/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="Back-end do .NET">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-ios-get-started-push/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações por push para um aplicativo iOS. Neste tutorial, você adicionará as notificações por push usando o APNS (Serviço de Notificação por Push da Apple) ao projeto de início rápido. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.

> [WACOM.NOTE]Este tópico oferece suporte para os serviços móveis *existentes* que ainda não foram *atualizados* para usar a integração de Hubs de Notificação. Quando você cria um serviço móvel *novo*, esta funcionalidade integrada é habilitada automaticamente. Para novos serviços móveis, consulte [Introdução às notificações por push][Introdução às notificações por push].
>
> Os Serviços Móveis integram-se aos Hubs de Notificação do Azure para oferecer suporte à funcionalidade de notificação por push, como modelos, várias plataformas e escala aprimorada. *Você deve atualizar seus serviços móveis existentes para usar os Hubs de Notificação quando possível*. Assim que você tiver feito a atualização, consulte esta versão da [Introdução às notificações por push][Introdução às notificações por push].

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
-   Um dispositivo compatível com o iOS 5.0 (ou versão posterior)
-   Associação no Programa de Desenvolvedores de iOS

	> [WACOM.NOTE] Devido aos requisitos de configuração das notificações por push, você deve implantar e testar as notificações por push em um dispositivo compatível com o iOS (iPhone ou iPad), em vez de no emulador.

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

[WACOM.INCLUDE [Habilitar as Notificações por Push da Apple][Habilitar as Notificações por Push da Apple]]

## Configurar Serviços Móveis para enviar solicitações de push

[WACOM.INCLUDE [mobile-services-apns-configure-push][mobile-services-apns-configure-push]]

## Adicionar notificações de push para seu aplicativo

1.  Em Xcode, abra o arquivo QSAppDelegate.h e adicione a seguinte propriedade abaixo da propriedade \***window**:

        @property (strong, nonatomic) NSString *deviceToken;

    > [WACOM.NOTE] Quando o esquema dinâmico estiver habilitado no seu serviço móvel, uma nova coluna 'deviceToken' será adicionada automaticamente à tabela**TodoItem** quando um novo item que contém essa propriedade for inserido.

2.  Em QSAppDelegate.m, substitua o método de manipulador a seguir dentro da implementação:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3.  Em QSAppDelegate.m, adicione o método de manipulador a seguir dentro da implementação:

        // We are registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
        }

4.  Em QSAppDelegate.m, adicione o método de manipulador a seguir dentro da implementação:

        // Handle any failure to register. In this case we set the deviceToken to an empty
        // string to prevent the insert from failing.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
            self.deviceToken = @"";
        }

5.  Em QSAppDelegate.m, adicione o método de manipulador a seguir dentro da implementação:

        // Because toast alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

6.  Em QSTodoListViewController.m, importe o arquivo QSAppDelegate.h para poder usar o representante para obter o token do dispositivo:

        #import "QSAppDelegate.h"

7.  Em QSTodoListViewController.m, modifique a ação **(IBAction)onAdd** localizando a linha a seguir:

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 

	Substitua isso por este código:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

    This adds a reference to the **QSAppDelegate** to obtain the device token and then modifies the request payload to include that device token.

    > [WACOM.NOTE] You must add this code before to the call to the <strong>addItem</strong> method.

Seu aplicativo foi atualizado para oferecer suporte a notificações de push.

## Atualizar o script de inserção registrados no Portal de Gerenciamento

1.  No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**.

    ![][]

2.  Em **todoitem**, clique na guia **Script** e selecione **Inserir**.

    ![][1]

    Isso exibe a função que é chamada quando ocorre uma inserção na tabela **TodoItem**.

3.  Substitua a função de inserção com o seguinte código e **Salvar**:

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send(item.deviceToken, {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    Isso registra um novo script de inserção, que usa o [objeto apns][objeto apns] para enviar uma notificação por push (o texto inserido) para o dispositivo fornecido na solicitação de inserção.

    > [WACOM.NOTE] Esse script atrasa o envio da notificação para que você tenha tempo de fechar o aplicativo para receber uma notificação do sistema.

## Testar notificações de push no seu aplicativo

1.  Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo em um dispositivo compatível com iOS; em seguida, clique em **OK** para aceitar as notificações por push

    ![][2]

    > [WACOM.NOTE] Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

2.  No aplicativo, digite um texto significativo, como por exemplo *Uma nova tarefa de Serviços Móveis* e clique no ícone de adição (**+**).

    ![][3]

3.  Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.

    ![][4]

4.  Repita a etapa 2 e feche o aplicativo imediatamente; em seguida, verifique se a notificação do sistema a seguir é mostrada.

    ![][5]

Este tutorial foi concluído com êxito.

## Próximas etapas

Neste exemplo simples, um usuário recebe uma notificação por push com os dados que você acabou de inserir. O token de dispositivo usado pelo APNS é fornecido para o serviço móvel pelo cliente na solicitação. No próximo tutorial, [Notificações de push para os usuários do aplicativo][Notificações de push para os usuários do aplicativo], você irá criar uma tabela separada de Dispositivos para armazenar tokens de dispositivo e enviar uma notificação por push para todos os canais armazenados quando ocorre uma inserção.


<!-- Anchors. --> 

<!-- Images. --> 
<!-- URLs. -->

  [C# da Windows Store]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "C# da Windows Store"
  [JavaScript da Windows Store]: /pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-push "JavaScript da Windows Store"
  [Windows Phone]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone"
  [iOS]: /pt-br/documentation/articles/mobile-services-ios-get-started-push "iOS"
  [Android]: /pt-br/documentation/articles/mobile-services-android-get-started-push "Android"
  [Appcelerator]: /pt-br/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push "Appcelerator"
  [Back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ "Back-end do .NET"
  [Back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-ios-get-started-push/ "Back-end do JavaScript"
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Gerar a solicitação de assinatura de certificado]: #certificates
  [Registrar seu aplicativo e habilitar as notificações por push]: #register
  [Criar um perfil de provisionamento para o aplicativo]: #profile
  [Configurar os Serviços Móveis]: #configure
  [Adicionar notificações por push ao aplicativo]: #add-push
  [Atualizar scripts para enviar notificações por push]: #update-scripts
  [Inserir dados para receber notificações]: #test
  [SDK do iOS dos Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-ios
  [Habilitar as Notificações por Push da Apple]: ../includes/enable-apple-push-notifications.md
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  []: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
  [objeto apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [4]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [5]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [Notificações de push para os usuários do aplicativo]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-ios
