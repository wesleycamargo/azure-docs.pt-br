<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-mobile-service-ios" urlDisplayName="Notify iOS app users by using Mobile Services" pageTitle="Register the current user for push notifications by using a mobile service - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification iOS app" description="Learn how to request push notification registration in an iOS app with Azure Notification Hubs when registeration is performed by Azure Mobile Services." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Register the current user for push notifications by using a mobile service" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Registrar o usuário atual para notificações por push usando um serviço móvel

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/" title="iOS" class="current">iOS</a>
</div>

Este tópico mostra como solicitar o registro de notificações por push com os Hubs de Notificação do Azure, quando o registro é executado pelos Serviços Móveis do Azure. Este tópico estende o tutorial [Notificar usuários com Hubs de Notificação][Notificar usuários com Hubs de Notificação]. Você já deve ter concluído as etapas necessárias nesse tutorial para criar o serviço móvel autenticado. Para obter mais informações sobre o cenário de notificação aos usuários, consulte [Notificar os usuários com os Hubs de Notificação][Notificar usuários com Hubs de Notificação].

1.  No Xcode, abra o arquivo QSTodoService.h no projeto que você criou quando concluiu o tutorial de pré-requisito [Introdução à autenticação][Introdução à autenticação] e adicione a seguinte propriedade **deviceToken**:

        @property (nonatomic) NSData* deviceToken;

    Essa propriedade armazena o token do dispositivo.

2.  No arquivo QSTodoService.m, adicione o seguinte método **getDeviceTokenInHex**:

            - (NSString*)getDeviceTokenInHex {
                const unsigned *tokenBytes = [[self deviceToken] bytes];
                NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                      ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                      ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                      ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
                return hexToken;
            }

    Esse método converte o token do dispositivo em um valor de cadeia de caracteres hexadecimais.

3.  No arquivo QSAppDelegate.m, adicione as seguintes linhas de código ao método **didFinishLaunchingWithOptions**:

            [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

    Isso habilita as notificações por push em seu aplicativo.

4.  No arquivo QSAppDelegate.m, adicione o seguinte método:

            - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
                [QSTodoService defaultService].deviceToken = deviceToken;
            }

    Isso atualiza a propriedade **deviceToken**.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Neste ponto, n&atilde;o deve haver nenhum outro c&oacute;digo nesse m&eacute;todo. Se voc&ecirc; j&aacute; tiver uma chamada para o m&eacute;todo **registerNativeWithDeviceToken** que foi adicionado quando voc&ecirc; concluiu o tutorial <a href="/pt-br/manage/services/notification-hubs/get-started-notification-hubs-ios/" target="_blank">Introdu&ccedil;&atilde;o aos Hubs de Notifica&ccedil;&atilde;o</a>, voc&ecirc; deve comentar ou remover essa chamada.</p>
</div>

5.  (Opcional) No arquivo QSAppDelegate.m, adicione o seguinte método de manipulador:

            - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
                NSLog(@"%@", userInfo);
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            }

    Esse método exibe um alerta na interface do usuário quando seu aplicativo recebe notificações enquanto está em execução.

6.  No arquivo QSTodoListViewController.m, adicione o método **registerForNotificationsWithBackEnd**:

            - (void)registerForNotificationsWithBackEnd {    
                NSString* json = [NSString  stringWithFormat:@"{\"platform\":\"ios\", \"deviceToken\":\"%@\"}", [self.todoService getDeviceTokenInHex] ];

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

    Esse método cria uma carga json que contém o token do dispositivo. Em seguida, ele chama a API personalizada no seu serviço móvel para registro da notificação. Esse método cria um token de dispositivo para notificações por push e o envia juntamente com o tipo do dispositivo para o método da API personalizada que cria um registro nos Hubs de Notificação. Essa API personalizada foi definida em [Notificar usuários com Hubs de Notificação][Notificar usuários com Hubs de Notificação].

7.  Finalmente, no método **viewDidAppear**, adicione uma chamada para esse novo método **registerForNotificationsWithBackEnd** depois que o usuário for autenticado com êxito, como no exemplo a seguir:

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

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Isso garante que o registro seja solicitado sempre que a p&aacute;gina for carregada. Em seu aplicativo, conv&eacute;m fazer esse registro periodicamente apenas para garantir que o registro seja atual.</p>
</div>

Agora que o aplicativo cliente foi atualizado, retorne para [Notificar os usuários com Hubs de Notificação][Notificar usuários com Hubs de Notificação] e atualize o serviço móvel para enviar notificações por meio de Hubs de Notificação.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /pt-br/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/ "Windows Store C#"
  [iOS]: /pt-br/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/ "iOS"
  [Notificar usuários com Hubs de Notificação]: /pt-br/manage/services/notification-hubs/notify-users
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-ios/
  [Introdução aos Hubs de Notificação]: /pt-br/manage/services/notification-hubs/get-started-notification-hubs-ios/
