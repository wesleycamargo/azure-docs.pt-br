<properties 
	pageTitle="Adicionar notificações por push ao aplicativo iOS com o Serviço de Aplicativo do Azure" 
	description="Aprenda a usar o Serviço de Aplicativo do Azure para enviar notificações por push para o aplicativo iOS." 
	services="app-service\mobile" 
	documentationCenter="ios" 
	manager="dwrede"
	editor="" 
	authors="yuaxu"/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article"
	ms.date="02/19/2015" 
	ms.author="yuaxu"/>


# Adicionar as notificações por push ao seu aplicativo iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../includes/app-service-mobile-selector-get-started-push-preview.md)]

Este tópico mostra como usar o Serviço de Aplicativo do Azure para enviar notificações por push para um aplicativo iOS com o serviço de notificação por push da Apple (APNS).  Após a conclusão, o back-end do .NET enviará uma notificação por push para o seu aplicativo QuickStart ToDo cada vez que um registro for inserido.  O aplicativo é compatível com iOS 8 e versões anteriores.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Gerar solicitação de assinatura do certificado do iOS]
2. [Registrar o aplicativo e habilitar notificações por push]
3. [Criar um perfil de provisionamento para o aplicativo]
4. [Configurar seu back-end móvel para enviar solicitações por push]
5. [Atualizar o servidor para enviar notificações por push](#update-server)
6. [Publicar o back-end móvel no Azure]
7. [Adicionar notificações por push ao aplicativo]
8. [Testar seu aplicativo]

Este tutorial exige o seguinte:

+ [SDK do Aplicativo móvel do Serviço de Aplicativo iOS]
+ [Nuget de hubs de notificação do Azure ]
+ [XCode 4.5][Instalar o Xcode]
+ Um dispositivo compatível com o iOS 6.0 (ou versão posterior)
+ Associação no Programa de Desenvolvedores de iOS

   > [AZURE.NOTE] Devido aos requisitos de configuração das notificações por push, você deve implantar e testar as notificações por push em um dispositivo compatível com o iOS (iPhone ou iPad) em vez de usar o emulador.

Este tutorial se baseia no guia de início rápido do Aplicativo móvel do Serviço de Aplicativo.  Antes de iniciar este tutorial, você deve primeiro concluir uma [Introdução aos aplicativos móveis do Serviço de Aplicativo].

[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

## Configurar o aplicativo móvel para enviar solicitações por push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../includes/app-service-mobile-apns-configure-push-preview.md)]

##<a id="update-server"></a>Atualizar o servidor para enviar notificações por push

1. No Visual Studio, clique com o botão direito do mouse, em seguida, clique em **Gerenciar Pacotes NuGet**.

2. Pesquise por **Microsoft.Azure.NotificationHubs** e clique em **instalar** para todos os projetos na solução.

3. No Gerenciador de Soluções do Visual Studio, expanda a pasta **Controladores** no projeto do back-end móvel.  Abra o TodoItemController.cs.  Na parte superior do arquivo, adicione as seguintes instruções `using`:

		using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. Adicione o seguinte fragmento ao `PostTodoItem` método após a chamada **InsertAsync**:  

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

        // iOS payload
        var appleNotificationPayload = "{\"aps\":{\"alert\":\"" + item.Text + "\"}}";

        try
        {
            await Hub.Push.SendAppleNativeNotificationAsync(appleNotificationPayload);
        }
        catch (System.Exception ex)
        {
            throw;
        }

    Esse código informa o hub de notificação associado a esse aplicativo móvel para enviar uma notificação de envio depois de uma inserção de item todo.


<h2><a name="publish-the-service"></a>Publicar o back-end móvel no Azure</h2>

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Adicionar notificações por push ao seu aplicativo
1. Baixe e adicione a referência ao SDK do cliente de aplicativo móvel do Serviço de Aplicativo no xcode.

2. Em **QSAppDelegate.m**, adicione o seguinte em **application:didFinishLaunchingWithOptions** para registrar o cliente com o serviço de notificação de envio da Apple:

        // register iOS8 or previous devices for notifications
        if ([[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)]) {
            [[UIApplication sharedApplication] registerForRemoteNotifications];
        }
        else {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
        }

3. No mesmo arquivo, adicione o seguinte método manipulador dentro da implementação **QSAppDelegate**:

        // registration with APNs is successful
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {

            // make sure you have imported "QSTodoService.h"
            QSTodoService *todoService = [QSTodoService defaultService];
            MSClient *client = todoService.client;

            [client.push registerDeviceToken:deviceToken completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. Em seguida, adicione o método do manipulador de falha dentro da implementação:

        // handle any failure to register
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. Em QSAppDelegate.m, adicione o método de manipulador a seguir dentro da implementação:  

        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            
            NSDictionary *apsPayload = [userInfo objectForKey:@"aps"];
            NSString *alertString = [apsPayload objectForKey:@"alert"];
    
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                          alertString delegate:nil cancelButtonTitle:
                          @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

Seu aplicativo foi atualizado para dar suporte a notificações por push.

## Testar notificações por push em seu aplicativo

1. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo em um dispositivo compatível com iOS; em seguida, clique em **OK** para aceitar as notificações por push

  	![][23]

    > [AZURE.NOTE] Você deve aceitar explicitamente as notificações por push do seu aplicativo.  Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

2. No aplicativo, digite um texto significativo, por exemplo, _Uma nova tarefa de Serviços Móveis_, e clique no ícone de adição (**+**).

  	![][24]

3. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.

  	![][25]

4. Repita a etapa 2 e feche o aplicativo imediatamente; em seguida, verifique se a notificação por push a seguir é mostrada.

  	![][26]

Este tutorial foi concluído com êxito.

<!-- Anchors.  -->
[Gerar solicitação de assinatura do certificado do iOS]: #certificates
[Registrar o aplicativo e habilitar notificações por push]: #register
[Criar um perfil de provisionamento para o aplicativo]: #profile
[Adicionar notificações por push ao aplicativo]: #add-push
[Configurar seu back-end móvel para enviar solicitações por push]: #configure
[Atualizar o servidor para enviar notificações por push]: #update-server
[Publicar o back-end móvel no Azure]: #publish-mobile-service
[Testar seu aplicativo]: #test-the-service

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

<!-- URLs. -->
[Instalar o Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portal de Provisionamento do iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[SDK do iOS móvel de Serviço de Aplicativo]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Nuget de hubs de notificação do Azure ]: https://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Serviço de Notificação por Push da Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Introdução aos Serviços Móveis]: mobile-services-dotnet-backend-ios-get-started.md
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[objeto APNs]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!--HONumber=49-->