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

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações por push para um aplicativo iOS. Neste tutorial, você adicionará as notificações por push usando o APNS (Serviço de Notificação por Push da Apple) ao projeto de início rápido. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.


Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Gerar a solicitação de assinatura de certificado]
2. [Registrar o aplicativo e habilitar notificações por push]
3. [Criar um perfil de provisionamento para o aplicativo]
4. [Atualizar o servidor para enviar notificações por push](#update-server)
5. [Publicar o serviço móvel no Azure]
6. [Adicionar notificações por push ao aplicativo]
7. [Habilitar notificações por push para teste local](#local-testing)
8. [Testar o aplicativo no serviço móvel publicado]

Este tutorial exige o seguinte:

+ [SDK do iOS dos Serviços Móveis]
+ [XCode 4.5][Instalar o Xcode]
+ Um dispositivo compatível com o iOS 6.0 (ou versão posterior)
+ Associação no Programa de Desenvolvedores de iOS

   > [AZURE.NOTE] Devido aos requisitos de configuração das notificações por push, você deve implantar e testar as notificações por push em um dispositivo compatível com o iOS (iPhone ou iPad) em vez de usar o emulador.

Este tutorial baseia-se no guia de início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos serviços móveis] ou [adicionar os serviços móveis ao seu aplicativo][Introdução aos dados].


[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]


## Configurar Serviços Móveis para enviar solicitações por push

[AZURE.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]


##<a id="update-server"></a>Atualizar o servidor para enviar notificações por push

1. No Gerenciador de Soluções do Visual Studio, expanda a pasta **Controladores** no projeto do serviço móvel. Abra o TodoItemController.cs. Na parte superior do arquivo, adicione as seguintes instruções `using`:


		using System;
		using System.Collections.Generic;

2. Atualize a definição do método `PostTodoItem` com o seguinte código:  

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

    Esse código enviará uma notificação por push (com o texto do item inserido) depois de inserir um item de tarefa pendente. Se ocorrer um erro, o código adicionará uma entrada de log de erros que pode ser visualizada na guia **Logs** do serviço móvel no Portal de Gerenciamento.


<h2><a name="publish-the-service"></a>Publicar o serviço móvel no Azure</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Adicionar notificações de push para seu aplicativo

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

3. Em QSAppDelegate.m, adicione o método de manipulador a seguir dentro da implementação. Certifique-se de copiar os valores da Url do Serviço Móvel e da Chave de Aplicativo e colocá-los nos espaços reservados:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {

            // TODO: update @"MobileServiceUrl" and @"AppKey" placeholders
            MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

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

   > [AZURE.NOTE] Você deve adicionar esse código antes de chamar o método <strong>addItem</strong>.

Seu aplicativo foi atualizado para dar suporte a notificações por push.

##<a id="local-testing"></a> Habilitar notificações por push para testes locais

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## Testar notificações por push no seu aplicativo

1. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo em um dispositivo compatível com iOS; em seguida, clique em **OK** para aceitar as notificações por push

  	![][23]

    > [AZURE.NOTE] Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

2. No aplicativo, digite um texto significativo, como _A new Mobile Services task_ e, em seguida, clique no ícone de adição (**+**).

  	![][24]

3. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.

  	![][25]

4. Repita a etapa 2 e feche o aplicativo imediatamente; em seguida, verifique se a notificação por push a seguir é mostrada.

  	![][26]

Este tutorial foi concluído com êxito.

## Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo iOS para usar Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir o próximo tutorial, [Enviar notificações por push para usuários autenticados], que permite mostrar como usar marcas para enviar notificações por push de um Serviço Móvel para um único usuário autenticado.

<!--+ [Enviar notificações por push para usuários autenticados]
	<br/>Saiba como usar marcas para enviar notificações por push de um Serviço Móvel para apenas um usuário autenticado.

+ [Enviar notificações de difusão para assinantes]
	<br/>Saiba como os usuários podem registrar e receber notificações por push para categorias de interesse.

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

<!-- Anchors.  -->
[Gerar a solicitação de assinatura de certificado]: #certificates
[Registrar o aplicativo e habilitar notificações por push]: #register
[Criar um perfil de provisionamento para o aplicativo]: #profile
[Configurar os Serviços Móveis]: #configure
[Atualizar scripts para enviar notificações por push]: #update-scripts
[Adicionar notificações por push ao aplicativo]: #add-push
[Inserir dados para receber notificações]: #test
[Testar o aplicativo no serviço móvel publicado]: #test-app
[Próximas etapas]:#next-steps
[Baixar o serviço localmente]: #download-the-service-locally
[Testar o serviço móvel]: #test-the-service
[Publicar o serviço móvel no Azure]: #publish-mobile-service

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
[SDK do iOS dos Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Serviço de Notificação por Push da Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[Introdução aos dados]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Introdução à autenticação]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users

[Enviar notificações por push para usuários autenticados]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/

[O que são Hubs de Notificação?]: /en-us/documentation/articles/notification-hubs-overview/
[Enviar notificações de difusão para assinantes]: /en-us/documentation/articles/notification-hubs-ios-send-breaking-news/
[Enviar notificações baseadas em modelo aos assinantes]: /en-us/documentation/articles/notification-hubs-ios-send-localized-breaking-news/
\n<!--HONumber=42-->
