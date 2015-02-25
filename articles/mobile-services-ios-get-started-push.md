<properties 
	pageTitle="Introdução às notificações por push (iOS) | Centro de Desenvolvimento Móvel" 
	description="Saiba como usar serviços móveis do Azure para enviar notificações por push para seu aplicativo iOS (push herdado)." 
	services="mobile-services" 
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
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Adicionar notificações por push ao seu aplicativo de Serviços Móveis (push herdado)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">C# da Windows Store</a>
    <a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">JavaScript da Windows Store</a>
    <a href="/en-us/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/en-us/documentation/articles/mobile-services-ios-get-started-push" title="iOS" class="current">iOS</a>
    <a href="/en-us/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
	<a href="/en-us/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET backend">Back-end .NET</a> | <a href="/en-us/documentation/articles/mobile-services-ios-get-started-push/"  title="JavaScript backend" class="current">Back-end JavaScript</a></div>

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações por push para um aplicativo iOS. Neste tutorial, você adicionará as notificações por push usando o APNS (Serviço de Notificação por Push da Apple) ao projeto de início rápido. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.


>[AZURE.NOTE]Este tópico oferece suporte a serviços móveis <em>existentes</em> que ainda <em>não foram atualizados</em> para usar a integração de Hubs de Notificação. Quando você cria um <em>novo</em> serviço móvel, essa funcionalidade integrada é habilitada automaticamente. Para novos serviços móveis, consulte [Introdução às notificações por push](/en-us/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/).
>
>Os Serviços Móveis integram-se aos Hubs de Notificação do Azure para dar suporte à funcionalidade de notificação por push, como modelos, várias plataformas e escala aprimorada. <em>Você deve atualizar seus serviços móveis existentes para usar os Hubs de Notificação quando possível</em>. Depois de ter atualizado, consulte esta versão de [Introdução às notificações por push](/ en-us/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/).

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Gerar a solicitação de assinatura de certificado]
2. [Registrar seu aplicativo e habilitar notificações por push]
3. [Criar um perfil de provisionamento para o aplicativo]
3. [Configurar os Serviços Móveis]
4. [Adicionar notificações por push ao aplicativo]
5. [Atualizar scripts para enviar notificações por push]
6. [Inserir dados para receber notificações]

Este tutorial exige o seguinte:

+ [SDK do iOS dos Serviços Móveis]
+ [Xcode 4.5][instalar o Xcode]
+ Um dispositivo compatível com o iOS 5.0 (ou versão posterior)
+ Associação no Programa de Desenvolvedores de iOS

   > [AZURE.NOTE] Devido aos requisitos de configuração das notificações por push, você deve implantar e testar as notificações por push em um dispositivo compatível com o iOS (iPhone ou iPad) em vez de usar o emulador.

Este tutorial baseia-se no Guia de início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis].

[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

## Configurar Serviços Móveis para enviar solicitações por push

[AZURE.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## Adicionar notificações por push ao aplicativo

1. No Xcode, abra o arquivo QSAppDelegate.h e adicione a propriedade a seguir abaixo da propriedade ***window**:

        @property (strong, nonatomic) NSString *deviceToken;

    > [AZURE.NOTE] Quando o esquema dinâmico estiver habilitado no seu serviço móvel, uma nova coluna 'deviceToken' é adicionada automaticamente à tabela **TodoItem** quando um novo item que contém essa propriedade é inserido.

2. Em QSAppDelegate.m, substitua o método de manipulador a seguir dentro da implementação:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. Em QSAppDelegate.m, adicione o método de manipulador a seguir dentro da implementação:

        // We are registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
        }

4. Em QSAppDelegate.m, adicione o método de manipulador a seguir dentro da implementação:

        // Handle any failure to register. In this case we set the deviceToken to an empty
        // string to prevent the insert from failing.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
            self.deviceToken = @"";
        }

5. Em QSAppDelegate.m, adicione o método de manipulador a seguir dentro da implementação:  

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

5. Em QSTodoListViewController.m, importe o arquivo QSAppDelegate.h para poder usar o representante para obter o token do dispositivo:

        #import "QSAppDelegate.h"

6. Em QSTodoListViewController.m, modifique a ação **(IBAction)onAdd** localizando a linha a seguir:

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };

   Replace this with the following code:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

   	Isso adicionará uma referência a **QSAppDelegate** para obter o token do dispositivo e, em seguida, modificará a carga de solicitação para incluir esse token de dispositivo.

   	> [AZURE.NOTE] Você deve adicionar esse código antes de chamar o método <strong>addItem</strong>.

Seu aplicativo foi atualizado para dar suporte a notificações de push.

## Atualizar o script de inserção registrado no Portal de Gerenciamento

1. No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**.

   	![][21]

2. Em **todoitem**, clique na guia **Script** e selecione **Inserir**.

  	![][22]

   	Isso exibe a função que é chamada quando ocorre uma inserção na tabela **TodoItem**.

3. Substitua a função de inserção pelo seguinte código e clique em **Salvar**:

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

   	Isso registra um novo script de inserção, que usa o [objeto apns] para enviar uma notificação por push (o texto inserido) para o dispositivo fornecido na solicitação de inserção.


   	> [AZURE.NOTE] Esse script atrasa o envio da notificação para que você tenha tempo de fechar o aplicativo para receber uma notificação do sistema.

## Testar notificações por push no seu aplicativo

1. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo em um dispositivo compatível com iOS; em seguida, clique em **OK** para aceitar as notificações por push

  	![][23]

    > [AZURE.NOTE] Você deve aceitar explicitamente as notificações por push do seu aplicativo. Essa solicitação ocorrerá apenas na primeira vez que o aplicativo for executado.

2. No aplicativo, digite um texto significativo, por exemplo, _Uma nova tarefa de Serviços Móveis_, e clique no ícone de adição (**+**).

  	![][24]

3. Verifique se uma notificação é recebida e clique em **OK** para ignorar a notificação.

  	![][25]

4. Repita a etapa 2 e feche o aplicativo imediatamente; em seguida, verifique se a notificação do sistema a seguir é mostrada.

  	![][26]

Este tutorial foi concluído com êxito.

## Próximas etapas

Neste exemplo simples, um usuário recebe uma notificação por push com os dados que você acabou de inserir. O token de dispositivo usado pelo APNS é fornecido para o serviço móvel pelo cliente na solicitação. No próximo tutorial, [Notificações por Push para usuários do aplicativo], você criará uma tabela separada de dispositivos para armazenar tokens de dispositivo e enviar uma notificação por push para todos os canais armazenados quando ocorre uma inserção.

<!-- Anchors. -->
[Gerar a solicitação de assinatura de certificado]: #certificates
[Registrar seu aplicativo e habilitar notificações por push]: #register
[Criar um perfil de provisionamento para o aplicativo]: #profile
[Configurar os Serviços Móveis]: #configure
[Atualizar scripts para enviar notificações por push]: #update-scripts
[Adicionar notificações por push ao aplicativo]: #add-push
[Inserir dados para receber notificações]: #test
[Próximas etapas]:#next-steps

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
[Introdução aos Serviços Móveis]: /en-us/develop/mobile/tutorials/get-started-ios
[Introdução aos dados]: /en-us/develop/mobile/tutorials/get-started-with-data-ios
[Introdução à autenticação]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
[Introdução às notificações por push]: /en-us/develop/mobile/tutorials/get-started-with-push-ios
[Notificações por push para usuários do aplicativo]: /en-us/develop/mobile/tutorials/push-notifications-to-users-ios
[Autorizar usuários com scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[objeto APNs]: http://go.microsoft.com/fwlink/p/?LinkId=272333
\n<!--HONumber=42-->
