<properties 
	pageTitle="Introdução às notificações por push (iOS) | Centro de Desenvolvimento de Serviços Móveis" 
	description="Saiba como usar serviços móveis do Azure para enviar notificações por push para seu aplicativo iOS." 
	services="mobile-services, notification-hubs" 
	documentationCenter="ios" 
	manager="dwrede" 
	editor="" 
	authors="krisragh"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="krisragh"/>

# Adicionar notificações por push ao seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações por push por meio de um serviço APNS (Serviço de Notificação por Push da Apple) para um aplicativo iOS. Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure para o [projeto de início rápido](http://azure.microsoft.com/en-us/documentation/articles/mobile-services-ios-get-started/). Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Gerar a solicitação de assinatura de certificado](#certificates)
2. [Registrar o aplicativo e habilitar notificações por push](#register)
3. [Criar um perfil de provisionamento para o aplicativo](#profile)
4. [Configurar os Serviços Móveis](#configure)
5. [Adicionar notificações por push ao aplicativo](#add-push)
6. [Atualizar scripts para enviar notificações por push](#update-scripts)
7. [Inserir dados para receber notificações](#test)

Este tutorial exige o seguinte:

+ [SDK do iOS dos Serviços Móveis]
+ [XCode 4.5][Instalar o Xcode]
+ Um dispositivo compatível com o iOS 6.0 (ou versão posterior)
+ Associação no Programa de Desenvolvedores de iOS

   > [AZURE.NOTE] Devido aos requisitos de configuração das notificações por push, você deve implantar e testar as notificações por push em um dispositivo compatível com o iOS (iPhone ou iPad) em vez de usar o emulador.

Este tutorial baseia-se no Guia de início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir um dos tutoriais [Introdução aos Serviços Móveis] ou [Adicionar Serviços Móveis a um aplicativo existente][Introdução aos dados].


[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]


## <a id="configure"></a>Configurar Serviços Móveis para enviar solicitações por push

[AZURE.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## <a id="update-scripts"></a>Atualizar o script de inserção registrado no Portal de Gerenciamento

1. No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**.

   	![][21]

2. Em **todoitem**, clique na guia **Script** e selecione **Inserir**.

  	![][22]

   	Isso exibe a função que é chamada quando ocorre uma inserção na tabela **TodoItem**.

3. Substitua a função de inserção pelo código a seguir, e clique em **Salvar**:

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

   	Isso registra um novo script de inserção, que usa o [objeto apns] para enviar uma notificação por push (o texto inserido) para o dispositivo fornecido na solicitação de inserção.


   	> [AZURE.NOTE] Esse script atrasa o envio da notificação para que você tenha tempo de fechar o aplicativo para receber uma notificação por push.

## <a id="add-push"></a>Adicionar notificações por push ao seu aplicativo

1. Em QSAppDelegate.m, insira o seguinte trecho para importar o SDK de iOS dos Serviços Móveis:

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

2. Em QSAppDelegate.m, substitua o método de manipulador a seguir dentro da implementação:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. Em QSAppDelegate.m, adicione o método de manipulador a seguir dentro da implementação. Certifique-se de copiar os valores de chave a URL dos Serviço Móvel e os valores de chave do aplicativo e trocá-los por espaços reservados:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            // TODO: update @"MobileServiceUrl" and @"AppKey" placeholders
			MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"];

            [client.push registerNativeWithDeviceToken:deviceToken tags:@[@"uniqueTag"] completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. Em QSAppDelegate.m, adicione o método de manipulador a seguir dentro da implementação:

        // Handle any failure to register.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. Em QSAppDelegate.m, adicione o método de manipulador a seguir dentro da implementação:  

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

Seu aplicativo foi atualizado para dar suporte a notificações de push.

## <a id="test"></a>Testar notificações por push em seu aplicativo

1. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo em um dispositivo compatível com iOS; em seguida, clique em **OK** para aceitar as notificações por push

  	![][23]

    > [AZURE.NOTE] Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

2. No aplicativo, digite um texto significativo, por exemplo, _Uma nova tarefa de Serviços Móveis_, e clique no ícone de adição (**+**).

  	![][24]

3. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.

  	![][25]

4. Repita a etapa 2 e feche o aplicativo imediatamente; em seguida, verifique se a notificação por push a seguir é mostrada.

  	![][26]

Este tutorial foi concluído com êxito.

## <a id="next-steps"></a>Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo iOS para usar Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir um dos seguintes tutoriais:

+ [Enviar notificações por push para usuários autenticados]
	<br/>Saiba como usar marcas para enviar notificações por push de um Serviço Móvel para apenas um usuário autenticado.

+ [Enviar notificações de transmissão para assinantes]
	<br/>Saiba como os usuários podem registrar e receber notificações por push para categorias de interesse.
<!---
+ [Enviar notificações baseadas em modelo para assinantes]
	<br/>Saiba usar modelos para enviar notificações por push de um Serviço Móvel, sem precisar colocar cargas específicas de plataforma no seu back-end.
-->
Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os serviços móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários de seu aplicativo com diferentes tipos de conta usando serviços móveis.

* [O que são Hubs de Notificação?]
  <br/>Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

* [Depurar aplicativos de Hubs de Notificação](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obter orientação ao solucionar problemas ou ao depurar soluções de hubs de notificação. 

* [Referência conceitual do tutorial do Objective-C de Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com Objective-C e iOS.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como implementar a lógica de negócios no seu serviço móvel.

<!-- Anchors. -->


<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs.   -->
[Instalar o Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portal de Provisionamento do iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[SDK do iOS dos Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Serviço de Notificação por Push da Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-ios-get-started
[Introdução aos dados]: /en-us/documentation/articles/mobile-services-ios-get-started-data
[Introdução à autenticação]: /en-us/documentation/articles/mobile-services-ios-get-started-users
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[objeto APNs]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293

[Enviar notificações por push para usuários autenticados]: /en-us/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/

[O que são Hubs de Notificação?]: /en-us/documentation/articles/notification-hubs-overview/
[Enviar notificações de transmissão para assinantes]: /en-us/documentation/articles/notification-hubs-ios-send-breaking-news/
[Enviar notificações baseadas em modelo para assinantes]: /en-us/documentation/articles/notification-hubs-ios-send-localized-breaking-news/

[Referência conceitual do tutorial do Objective-C de Serviços Móveis]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
\n<!--HONumber=42-->
