<properties title="Azure Notification Hubs Secure Push" pageTitle="Azure Notification Hubs Secure Push" metaKeywords="Azure push notifications, Azure notification hubs, secure push" description="Learn how to send secure push notifications to an iOS app from Azure. Code samples written in Objective-C and C#." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />

# Push Seguro dos Hubs de Notificação do Azure

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/pt-br/documentation/articles/notification-hubs-windows-dotnet-secure-push/" title="Windows Universal">Windows Universal</a><a href="/pt-br/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/" title="iOS" class="current">iOS</a>
        <a href="/pt-br/documentation/articles/notification-hubs-aspnet-backend-android-secure-push/" title="Android">Android</a>
</div>

O suporte à notificação por push no Microsoft Azure permite que você acesse uma infraestrutura de envio por push fácil de usar, de multiplataforma e expansível que simplifica muito a implantação de notificações por push para aplicativos de consumidor e empresariais para plataformas móveis.

Devido a restrições regulatórias ou de segurança, às vezes, um aplicativo pode querer incluir algo na notificação que não pode ser transmitido por meio da infraestrutura de notificação por push padrão. Este tutorial descreve como obter a mesma experiência ao enviar informações confidenciais por meio de uma conexão segura e autenticada entre o dispositivo cliente e o back-end do aplicativo.

Em um nível superior, o fluxo é o seguinte:

1.  O back-end do aplicativo:

    -   Armazena uma carga segura no banco de dados de back-end.
    -   Envia a ID dessa notificação ao dispositivo (nenhuma informação segura é enviada).

2.  O dispositivo no aplicativo, ao receber a notificação:

    -   O dispositivo entra em contato com o back-end solicitando a carga segura.
    -   O aplicativo pode mostrar a carga como uma notificação no dispositivo.

É importante observar que no fluxo anterior (e neste tutorial), presumimos que o dispositivo armazena um token de autenticação no repositório local, após o usuário fazer logon. Isso garante uma experiência completamente ininterrupta, uma vez que o dispositivo pode recuperar a carga segura da notificação usando esse token. Se o seu aplicativo não armazenar tokens de autenticação no dispositivo, ou se esses tokens puderem expirar, o aplicativo do dispositivo, após receber a notificação, deverá exibir uma notificação genérica solicitando que o usuário inicie o aplicativo. O aplicativo, então, autentica o usuário e mostra a carga de notificação.

Este tutorial de Push Seguro mostra como enviar uma notificação por push de maneira segura. O tutorial baseia-se no tutorial **Notificação de Usuários**, por isso, você deve concluir as etapas nesse tutorial primeiro.

> [AZURE.NOTE] Este tutorial presume que você criou e configurou seu hub de notificação conforme descrito em [Introdução aos Hubs de Notificação (iOS)][Introdução aos Hubs de Notificação (iOS)].

[WACOM.INCLUDE [notification-hubs-aspnet-backend-securepush](../includes/notification-hubs-aspnet-backend-securepush.md)]

## Modificar o projeto iOS

Agora que você modificou o back-end do aplicativo para enviar apenas a *ID* de uma notificação, é preciso alterar o aplicativo iOS para manipular essa notificação e retornar a chamada do back-end para recuperar a mensagem segura a ser exibida.

Para isso, precisamos gravar a lógica para recuperar o conteúdo seguro do back-end do aplicativo.

1.  No **AppDelegate.m**, adicione uma seção de implementação no topo, com a seguinte declaração:

        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end

2.  Em seguida, adicione o código a seguir à seção de implementação, substituindo o espaço reservado `{back-end endpoint}` pelo ponto de extremidade para seu back-end obtido anteriormente:

        NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        {
            // check if authenticated
            ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
            NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
            if (!authenticationHeader) return;


            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];    
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                    NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                    completion([json objectForKey:@"Payload"], nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

    Esse método chama o seu back-end do aplicativo para recuperar o conteúdo da notificação usando as credenciais armazenadas nas preferências compartilhadas.

3.  Agora, precisamos manipular a notificação de entrada e utilizar o método acima para recuperar o conteúdo a ser exibido. Primeiro, precisamos habilitar o aplicativo iOS para ser executado em segundo plano ao receber uma notificação por push. Em **XCode**, selecione seu projeto de aplicativo no painel à esquerda e depois clique no seu destino de aplicativo principal na seção **Destinos** do painel central.

4.  Então, clique na guia **Recursos** na parte superior do painel central e marque a caixa de seleção **Notificações Remotas**.

    ![][0]

5.  Em **AppDelegate.m**, adicione o método a seguir para manipular notificações por push:

        -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"%@", userInfo);

            [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
                if (!error) {
                    // show local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                    localNotification.alertBody = payload;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    completionHandler(UIBackgroundFetchResultNewData);
                } else {
                    completionHandler(UIBackgroundFetchResultFailed);
                }
            }];

        }

    Observe que é preferível manipular os casos de propriedade de cabeçalho de autenticação ausente ou de rejeição por meio do back-end. A manipulação específica desses casos depende em grande parte da sua meta de experiência do usuário. Uma opção é exibir uma notificação com um aviso genérico para que o usuário realize a autenticação para recuperar a notificação real.

## Executar o aplicativo

Para executar o aplicativo, faça o seguinte:

1.  Certifique-se de que o **AppBackend** esteja implantado no Azure. Se estiver usando o Visual Studio, execute o aplicativo da API Web **AppBackend**. Uma página da Web do ASP.NET será exibida.

2.  No XCode, execute o aplicativo em um dispositivo iOS físico (as notificações por push não funcionarão no simulador).

3.  Na interface do usuário do aplicativo iOS, insira um nome de usuário e senha. Pode ser qualquer cadeia de caracteres, mas elas devem ter o mesmo valor.

4.  Na interface do usuário do aplicativo iOS, clique em **Logon**. Em seguida, clique em **Enviar push**. Você deve ver a notificação segura sendo exibida no centro de notificações.

  [0]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
