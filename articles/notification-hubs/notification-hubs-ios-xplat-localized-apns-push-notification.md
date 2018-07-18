---
title: Enviar notificações por push localizadas para dispositivos iOS usando Hubs de Notificação do Azure | Microsoft Docs
description: Saiba como usar notificações por push localizadas para dispositivos iOS usando Hubs de Notificação do Azure.
services: notification-hubs
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: b3d74086ee233da50138aff00d8da78aa0243a75
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776491"
---
# <a name="tutorial-push-localized-notifications-to-ios-devices-using-azure-notification-hubs"></a>Tutorial: Enviar notificações por push localizadas para dispositivos iOS usando Hubs de Notificação do Azure 
> [!div class="op_single_selector"]
> * [C# da Windows Store](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 

Este tutorial mostra como usar o recurso [modelos](notification-hubs-templates-cross-platform-push-messages.md) dos Hubs de Notificação do Azure para difundir notificações de últimas notícias que foram localizadas por idioma e dispositivo. Neste tutorial você começa com o aplicativo iOS criado em [Usar Hubs de Notificação para enviar as últimas notícias]. Ao concluir, você poderá se registrar em categorias de seu interesse, especificar um idioma no qual deseja receber as notificações e receber notificações por push para as categorias selecionadas nesse idioma.

Há duas partes que compõem esse cenário:

* o aplicativo iOS permite que dispositivos cliente especifiquem um idioma e se inscrevam em diferentes categorias de últimas notícias;
* O back-end transmite as notificações usando os recursos **tag** e **template** dos Hubs de Notificação do Azure.

Neste tutorial, você deve executar as seguintes etapas:

> [!div class="checklist"]
> * Atualizar a interface do usuário do aplicativo
> * Compilar o aplicativo iOS
> * Enviar notificações de modelo localizado do aplicativo de console do .NET.
> * Enviar notificações de modelo localizado do dispositivo


## <a name="overview"></a>Visão geral
Em [Usar Hubs de Notificação para enviar as últimas notícias], você criou um aplicativo que usou **marcas** para assinar notificações de diferentes categorias de notícias. No entanto, muitos aplicativos são destinados a vários mercados e requerem localização. Isso significa que o conteúdo das notificações deve ser localizado e enviado para o conjunto de dispositivos correto. Este tutorial ensina a usar o recurso **modelo** dos Hubs de Notificação para enviar facilmente notificações de últimas notícias localizadas.

> [!NOTE]
> Uma maneira de enviar notificações localizadas é criar várias versões de cada marca. Por exemplo, para dar suporte a inglês, francês e mandarim, precisamos de três marcas diferentes para notícias do mundo: "world_en", "world_fr" e "world_ch". Em seguida, precisamos enviar uma versão localizada das notícias do mundo para cada uma dessas marcas. Neste tópico, use modelos para evitar a proliferação de marcas e a necessidade de enviar várias mensagens.

Em um alto nível, os modelos são uma maneira de especificar como um dispositivo específico deve receber uma notificação. O modelo especifica o formato exato da carga referindo-se às propriedades que fazem parte da mensagem enviada por seu aplicativo de back-end. No seu caso, você enviará uma mensagem independente de localidade contendo todos os idiomas com suporte:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Em seguida, você verificará se os dispositivos se registram com um modelo que faz referência à propriedade correta. Por exemplo, um aplicativo iOS que deseja se registrar para receber notícias em francês registrará o seguinte:

    {
        aps:{
            alert: "$(News_French)"
        }
    }

Para saber mais sobre o uso de modelos, consulte o artigo [Modelos](notification-hubs-templates-cross-platform-push-messages.md).

## <a name="prerequisites"></a>pré-requisitos

- Concluir o tutorial [Notificações por push para dispositivos iOS específicos](notification-hubs-ios-xplat-segmented-apns-push-notification.md) e ter o código disponível, pois este tutorial baseia-se diretamente naquele código.
- O uso do Visual Studio 2012 ou posterior é opcional.

## <a name="update-the-app-user-interface"></a>Atualizar a interface do usuário do aplicativo
Nesta seção, você modificará o aplicativo Últimas notícias que criou no tópico [Usar Hubs de Notificação para enviar as últimas notícias] a fim de enviar as últimas notícias localizadas usando modelos.

No seu MainStoryboard_iPhone.storyboard, adicione um controle segmentado com três idiomas: inglês, francês e mandarim.

![][13]

Em seguida, adicione um IBOutlet em seu ViewController.h, conforme mostrado na seguinte imagem:

![][14]

## <a name="build-the-ios-app"></a>Compilar o aplicativo iOS
1. No Notification.h, adicione o método *retrieveLocale* e modifique os métodos store e subscribe conforme mostrado no código abaixo:
   
    ```obj-c
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;
   
        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;
   
        - (NSSet*) retrieveCategories;
   
        - (int) retrieveLocale;
   
    ```
    No Notification.m, modifique o método *storeCategoriesAndSubscribe* adicionando o parâmetro locale e armazenando-o nos padrões do usuário:
   
    ```obj-c
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];
   
            [self subscribeWithLocale: locale categories:categories completion:completion];
        }
    ````
    Em seguida, modifique o método *subscribe* para incluir o locale:
   
    ```obj-c
        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];
   
            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }
   
            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];
   
            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }
       ```
    You use the method *registerTemplateWithDeviceToken*, instead of *registerNativeWithDeviceToken*. When you register for a template, you have to provide the json template and also a name for the template (as the app might want to register different templates). Make sure to register your categories as tags, as you want to make sure to receive the notifciations for those news.
   
    Add a method to retrieve the locale from the user default settings:
   
    ```obj-c
        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
   
            int locale = [defaults integerForKey:@"BreakingNewsLocale"];
   
            return locale < 0?0:locale;
        }
    ```
2. Agora que você modificou a classe Notifications, terá que fazer com que o ViewController use o novo UISegmentControl. Adicione a seguinte linha ao método *viewDidLoad* método para ter certeza de mostrar a localidade que está selecionado no momento:
   
    ```obj-c
        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
     ```  
    Em seguida, no método *subscribe*, altere a chamada a *storeCategoriesAndSubscribe* para o seguinte código:
   
    ```obj-c
        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];
    ```
3. Finalmente, você tem que atualizar o método *didRegisterForRemoteNotificationsWithDeviceToken* no seu AppDelegate.m, de forma que você possa atualizar corretamente seu registro quando seu aplicativo inicie. Altere sua chamada ao método *subscribe* de notificações com o seguinte código:
   
    ```obj-c
        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(opcional) Enviar notificações de modelo localizadas do aplicativo de console do .NET.
[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(opcional) Envie notificações de modelo localizado do dispositivo
Se você não tem acesso ao Visual Studio ou deseja apenas testar o envio das notificações de modelo localizado diretamente do aplicativo no dispositivo. Você pode apenas adicionar os parâmetros de modelo localizado ao método `SendNotificationRESTAPI` definido no tutorial anterior.

    ```obj-c
        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];

            NSString *json;

            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];

            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];

            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];

            [dataTask resume];
        }
    ```


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você enviou notificações localizadas para dispositivos iOS. Para saber como enviar notificações por push para usuários de aplicativos iOS específicos, vá para o seguinte tutorial: 

> [!div class="nextstepaction"]
>[Enviar notificações por push para usuários específicos](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Usar Hubs de Notificação para enviar as últimas notícias]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
