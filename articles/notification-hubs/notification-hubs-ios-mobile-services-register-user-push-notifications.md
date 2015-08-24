<properties 
	pageTitle="Registrar o usuário atual para notificações por push usando um serviço móvel | Microsoft Azure" 
	description="Saiba como solicitar o registro de notificação por push em um aplicativo iOS com Hubs de notificação do Azure quando o registro é executado por serviços móveis do Azure." 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="07/28/2015" 
	ms.author="yuaxu"/>

# Registrar o usuário atual para notificações por push usando um serviço móvel

> [AZURE.SELECTOR]
[Windows Store C#](notification-hubs-windows-store-mobile-services-register-user-push-notifications.md)
[iOS](notification-hubs-ios-mobile-services-register-user-push-notifications.md)

Este tópico mostra como solicitar o registro de notificações por push com os Hubs de Notificação do Azure, quando o registro é executado pelos Serviços Móveis do Azure. Este tópico estende o tutorial [Notificar usuários com Hubs de Notificação]. Você já deve ter concluído as etapas necessárias nesse tutorial para criar o serviço móvel autenticado. Para obter mais informações sobre o cenário de notificação aos usuários, consulte [Notificar os usuários com os Hubs de Notificação].

1. No Xcode, abra o arquivo QSTodoService.h no projeto que você criou quando concluiu o tutorial de pré-requisito [Introdução à autenticação] e adicione a seguinte propriedade **deviceToken**:

		@property (nonatomic) NSData* deviceToken;

 	Essa propriedade armazena o token do dispositivo.

2. No arquivo QSTodoService.m, adicione o seguinte método **getDeviceTokenInHex**:

			- (NSString*)getDeviceTokenInHex {
			    const unsigned *tokenBytes = [[self deviceToken] bytes];
			    NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
			                          ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
			                          ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
			                          ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
			    return hexToken;
			}

	Esse método converte o token do dispositivo em um valor de cadeia de caracteres hexadecimais.

3. No arquivo QSAppDelegate.m, adicione as seguintes linhas de código ao método **didFinishLaunchingWithOptions**:

			[[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

	Isso habilita as notificações por push em seu aplicativo.

4. 	No arquivo QSAppDelegate.m, adicione o seguinte método:

			- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
			    [QSTodoService defaultService].deviceToken = deviceToken;
			}

	Isso atualiza a propriedade **deviceToken**.

	> [AZURE.NOTE]Neste ponto, não deve haver nenhum outro código nesse método. Se já houver uma chamada para o método **registerNativeWithDeviceToken** adicionado quando você concluiu o tutorial [Introdução aos Hubs de Notificação](/manage/services/notification-hubs/get-started-notification-hubs-ios/"%20target="_blank"), será necessário comentar ou remover essa chamada.

5.  (Opcional) No arquivo QSAppDelegate.m, adicione o seguinte método de manipulador:

			- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
			    NSLog(@"%@", userInfo);
			    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
			                          [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
			                          @"OK" otherButtonTitles:nil, nil];
			    [alert show];
			}

 	Esse método exibe um alerta na interface do usuário quando seu aplicativo recebe notificações enquanto está em execução.

6. No arquivo QSTodoListViewController.m, adicione o método **registerForNotificationsWithBackEnd**:

			- (void)registerForNotificationsWithBackEnd {
			    NSString* json = [NSString  stringWithFormat:@"{"platform":"ios", "deviceToken":"%@"}", [self.todoService getDeviceTokenInHex] ];

			    [self.todoService.client invokeAPI:@"register_notifications" data:[json dataUsingEncoding:NSUTF8StringEncoding] HTTPMethod:@"POST" parameters:nil headers:nil completion:^(id result, NSHTTPURLResponse *response, NSError *error) {
			        if (error != nil) {
			            NSLog(@"Registration failed: %@", error);
			        } else {
			            // display UIAlert with successful login
			            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
			            [alert show];
			        }
			    }];
			}

	Esse método cria uma carga json que contém o token do dispositivo. Em seguida, ele chama a API personalizada no seu serviço móvel para registro da notificação. Esse método cria um token de dispositivo para notificações por push e o envia juntamente com o tipo do dispositivo para o método da API personalizada que cria um registro nos Hubs de Notificação. Essa API personalizada foi definida em [Notificar usuários com Hubs de Notificação].

7.	Finalmente, no método **viewDidAppear**, adicione uma chamada para esse novo método **registerForNotificationsWithBackEnd** depois que o usuário for autenticado com êxito, como no exemplo a seguir:

			- (void)viewDidAppear:(BOOL)animated
			{
			    MSClient *client = self.todoService.client;

			    if (client.currentUser != nil) {
			        return;
			    }

			    [client loginWithProvider:@"microsoftaccount" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
			        [self refresh];
			        [self registerForNotificationsWithBackEnd];
			    }];
			}

	> [AZURE.NOTE]Isso garante que o registro seja solicitado sempre que a página for carregada. Em seu aplicativo, convém fazer esse registro periodicamente apenas para garantir que o registro seja atual.
	
Agora que o aplicativo cliente foi atualizado, retorne para [Notificar os usuários com Hubs de Notificação] e atualize o serviço móvel para enviar notificações por meio de Hubs de Notificação.

<!-- Anchors. -->

<!-- Images. -->


<!-- URLs. -->
[Notificar os usuários com Hubs de Notificação]: /manage/services/notification-hubs/notify-users
[Notificar os usuários com os Hubs de Notificação]: /manage/services/notification-hubs/notify-users
[Notificar usuários com Hubs de Notificação]: /manage/services/notification-hubs/notify-users
[Introdução à autenticação]: /develop/mobile/tutorials/get-started-with-users-ios/

[Azure Management Portal]: https://manage.windowsazure.com/
[Get Started with Notification Hubs]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
 

<!---HONumber=August15_HO7-->