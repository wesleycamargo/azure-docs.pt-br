<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Introdução às notificações por push nos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="iOS" class="current">iOS</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Android" class="current">Android</a>
<!-- <a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
</div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="Back-end do .NET" class="current">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/"  title="Back-end do JavaScript">Back-end do JavaScript</a></div>

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações por push para um aplicativo iOS. Neste tutorial, você adicionará as notificações por push usando o APNS (Serviço de Notificação por Push da Apple) ao projeto de início rápido. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1.  [Gerar a solicitação de assinatura de certificado][Gerar a solicitação de assinatura de certificado]
2.  [Registrar seu aplicativo e habilitar as notificações por push][Registrar seu aplicativo e habilitar as notificações por push]
3.  [Criar um perfil de provisionamento para o aplicativo][Criar um perfil de provisionamento para o aplicativo]
4.  [Baixar o serviço localmente][Baixar o serviço localmente]
5.  [Testar o serviço móvel][Testar o serviço móvel]
6.  [Atualizar o servidor para enviar notificações por push][Atualizar o servidor para enviar notificações por push]
7.  [Publicar o serviço móvel no Azure][Publicar o serviço móvel no Azure]
8.  [Adicionar notificações por push ao aplicativo][Adicionar notificações por push ao aplicativo]
9.  [Atualizar scripts para enviar notificações por push][Atualizar scripts para enviar notificações por push]
10. [Habilitar notificações por push para teste local][Habilitar notificações por push para teste local]
11. [Testar o aplicativo no serviço móvel publicado][Testar o aplicativo no serviço móvel publicado]

Este tutorial exige o seguinte:

-   [SDK do iOS dos Serviços Móveis][SDK do iOS dos Serviços Móveis]
-   [XCode 4.5][XCode 4.5]
-   Um dispositivo compatível com iOS 6.0 (ou versão posterior)
-   Associação no Programa de Desenvolvedores de iOS

> [WACOM.NOTE] Devido aos requisitos de configuração das notificações por push, você deve implantar e testar as notificações por push em um dispositivo compatível com o iOS (iPhone ou iPad), em vez de usar o emulador.

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

[WACOM.INCLUDE [Habilitar Notificações por Push para Apple][Habilitar Notificações por Push para Apple]]

## Configurar Serviços Móveis para enviar solicitações de push

[WACOM.INCLUDE [mobile-services-apns-configure-push][mobile-services-apns-configure-push]]

## <a name="download-the-service"></a><span class="short-header">Baixar o serviço</span>Baixar o serviço em seu computador local

[WACOM.INCLUDE [mobile-services-ios-download-service-locally][mobile-services-ios-download-service-locally]]

## <a name="test-the-service"></a><span class="short-header">Testar o serviço</span>Testar o serviço móvel

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][mobile-services-dotnet-backend-test-local-service]]

## <span id="update-server"></span></a>Atualizar o servidor para enviar notificações por push

1.  No Gerenciador de Soluções do Visual Studio, expanda a pasta **Controladores** no projeto do serviço móvel. Abra o TodoItemController.cs. Na parte superior do arquivo, inclua as seguintes instruções `using`:

        using System;
        using System.Collections.Generic;

2.  Atualize a definição do método `PostTodoItem` com o seguinte código:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            ApplePushMessage message = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Esse código irá enviar uma notificação por push (com o texto do item inserido) depois de inserir um item de tarefa pendente. Se ocorrer um erro, o código adicionará uma entrada no log de erros que pode ser visualizada na guia **Logs** do serviço móvel no Portal de Gerenciamento.

## <a name="publish-the-service"></a><span class="short-header">Publicar o serviço</span>Publicar o serviço móvel no Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

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

## <span id="local-testing"></span></a> Habilitar notificações por push para teste local

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push][mobile-services-dotnet-backend-configure-local-push]]

## Testar notificações por push no seu aplicativo

1.  Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo em um dispositivo compatível com iOS; em seguida, clique em **OK** para aceitar as notificações por push

    ![][]

    > [WACOM.NOTE] Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

2.  No aplicativo, digite um texto significativo, como *Uma nova tarefa de Serviços Móveis* e clique no ícone do sinal de mais (**+**).

    ![][1]

3.  Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.

    ![][2]

4.  Repita a etapa 2 e feche o aplicativo imediatamente; em seguida, verifique se a notificação por push a seguir é mostrada.

    ![][3]

Este tutorial foi concluído com êxito.

## Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo iOS para usar Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir o próximo tutorial, [Enviar notificações por push a usuários autenticados][Enviar notificações por push a usuários autenticados], que mostra como usar marcas para enviar notificações por push de um Serviço Móvel somente para um usuário autenticado.

<!--+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

-   [Introdução aos dados][Introdução aos dados]
    Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][Introdução à autenticação]
    Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando Serviços Móveis.

-   [O que são Hubs de Notificação?][O que são Hubs de Notificação?]
    Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

<!-- Anchors.  -->
<!-- Images. -->
<!-- URLs. -->

  [C# da Windows Store]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/ "C# da Windows Store"
  [JavaScript da Windows Store]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/ "JavaScript da Windows Store"
  [Windows Phone]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ "Windows Phone"
  [iOS]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ "iOS"
  [Android]: /pt-br/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ "Android"
  [Back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ "Back-end do .NET"
  [Back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/ "Back-end do JavaScript"
  [Gerar a solicitação de assinatura de certificado]: #certificates
  [Registrar seu aplicativo e habilitar as notificações por push]: #register
  [Criar um perfil de provisionamento para o aplicativo]: #profile
  [Baixar o serviço localmente]: #download-the-service-locally
  [Testar o serviço móvel]: #test-the-service
  [Atualizar o servidor para enviar notificações por push]: #update-server
  [Publicar o serviço móvel no Azure]: #publish-mobile-service
  [Adicionar notificações por push ao aplicativo]: #add-push
  [Atualizar scripts para enviar notificações por push]: #update-scripts
  [Habilitar notificações por push para teste local]: #local-testing
  [Testar o aplicativo no serviço móvel publicado]: #test-app
  [SDK do iOS dos Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started
  [Habilitar Notificações por Push para Apple]: ../includes/enable-apple-push-notifications.md
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  [mobile-services-ios-download-service-locally]: ../includes/mobile-services-ios-download-service-locally.md
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  []: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [Enviar notificações por push a usuários autenticados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/
  [Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
  [O que são Hubs de Notificação?]: /pt-br/documentation/articles/notification-hubs-overview/
