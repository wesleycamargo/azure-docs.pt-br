<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs Notify Users" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in Objective-C using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"></tags>

# Notificação de Usuários nos Hubs de Notificação do Azure

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/pt-br/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/pt-br/documentation/articles/notification-hubs-/" title="iOS" class="current">iOS</a>
        <a href="/pt-br/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

O suporte à notificação por push no Azure permite que você acesse uma infraestrutura de envio por push fácil de usar, multiplataforma e expansível que simplifica em muito a implementação de notificações por push para aplicativos de consumidor e empresariais para plataformas móveis. Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push a um usuário específico do aplicativo em um dispositivo específico. Um back-end da API Web do ASP.NET é usado para autenticar clientes e gerar notificações, como mostrado no tópico de instrução [Registrando-se a partir do back-end do aplicativo][Registrando-se a partir do back-end do aplicativo]. Este tutorial se baseia no hub de notificação que você criou no tutorial **Introdução aos Hubs de Notificação**.

Este tutorial também é um pré-requisito para o tutorial **Push Seguro**. Após ter concluído as etapas neste tutorial **Notificação de Usuários**, você poderá seguir para o tutorial **Push Seguro**, que mostra como modificar o código de **Notificação de Usuários** para enviar uma notificação por push com segurança.

> [AZURE.NOTE] Este tutorial presume que você criou e configurou seu hub de notificação conforme descrito em [Introdução aos Hubs de Notificação (iOS)][Introdução aos Hubs de Notificação (iOS)].

## Criar e configurar o hub de notificação

Antes de começar este tutorial, você deve criar um perfil de provisionamento do iOS e um certificado de push de desenvolvimento e, em seguida, criar um Hub de Notificação do Azure e conectá-lo a esse aplicativo. Siga as etapas em [Introdução aos Hubs de Notificação (iOS)][Introdução aos Hubs de Notificação (iOS)]; especificamente as seções 1 a 5.

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers][notification-hubs-aspnet-backend-notifyusers]]

## Modificar seu aplicativo iOS

1.  Siga as etapas em [Introdução aos Hubs de Notificação (iOS)][Introdução aos Hubs de Notificação (iOS)], seções 1 a 5, para criar um aplicativo de exibição em Página Única capaz de receber notificações por push de um hub de notificação.

   > [AZURE.NOTE] Nesta seção, presumimos que você configurou seu projeto com um nome de organização vazio. Se esse não for o caso, você terá que preceder o nome da sua organização em todos os nomes de classes no código abaixo.

1.  No Main.storyboard, adicione os seguintes componentes da biblioteca de objetos:

    -   Um UITextField com texto de espaço reservado **Nome de Usuário**
    -   Um UITextField com texto de espaço reservado **Senha** e marque a opção **Entrada Segura de Texto** no grupo de propriedades TextField no painel à direita
    -   Um UIButton denominado **1. Logon** e desmarque a opção **Habilitado** no grupo de propriedades Acessibilidade no painel à direita
    -   Um UIButton denominado **2. Enviar Push** e desmarque a opção **Habilitado** no grupo de propriedades Acessibilidade no painel à direita

    O storyboard deve ter a seguinte aparência:

    ![][]

2.  Crie saídas para UITextFields e UIButtons na parte da interface do ViewController.m

        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

3.  Crie ações para ambos os botões na implementação do ViewController.m:

        (IBAction)LogInAction:(id)sender { }
        (IBAction)SendPushAction:(id)sender { }

4.  Primeiro, criaremos uma classe RegisterClient para fazer interface com nosso back-end. Crie uma classe de Objetivo C chamada RegisterClient, que herde de NSObject. Em seguida, adicione o seguinte código na seção da interface RegisterClient.h:

        @property (strong, nonatomic) NSString* authenticationHeader;
        -(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;

5.  Em RegisterClient.m, adicione a seguinte seção da interface:

        @interface RegisterClient ()

        @property (strong, nonatomic) NSURLSession* session;
        -(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
        -(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
        -(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSString*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;

        @end

6.  Depois, adicione o código a seguir na seção de implementação e substitua o espaço reservado *{backend endpoint}* pelo ponto de extremidade utilizado para implantar o back-end do aplicativo na seção anterior.

        NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";
        NSString *const BackEndEndpoint = @"{backend endpoint}/api/register";

        - (instancetype)init
        {
            self = [super init];
            if (self) {
                NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
                _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
            }
            return self;
        }

        -(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;
        {
            [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
        }

        -(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
        {
            NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

            NSString *deviceTokenString = [[token description] stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
            deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""] uppercaseString];

            [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString completion:^(NSString* registrationId, NSError *error) {
                NSLog(@"regId: %@", registrationId);
                if (error) {
                    completion(error);
                    return;
                }

                [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                    if (error) {
                        completion(error);
                        return;
                    }

                    NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                    if (httpResponse.statusCode == 200) {
                        completion(nil);
                    } else if (httpResponse.statusCode == 410 && retry) {
                        [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                    } else {
                        NSLog(@"Registration error with response status: %ld", (long) httpResponse.statusCode);

                        completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode userInfo:nil]);
                    }

                }];
            }];
        }

        -(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSData*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;
        {
            NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token, @"Tags": [tags allObjects]};
            NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration options:NSJSONWritingPrettyPrinted error:nil];

            NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding]);

            NSString* endpoint = [NSString stringWithFormat:@"%@/%@", BackEndEndpoint, registrationId];
            NSURL* requestURL = [NSURL URLWithString:endpoint];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"PUT"];
            [request setHTTPBody:jsonData];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
            [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                if (!error)
                {
                    completion(response, error);
                }
                else
                {
                    NSLog(@"Error request: %@", error);
                    completion(nil, error);
                }
            }];
            [dataTask resume];
        }

        -(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
        {
            NSString* registrationId = [[NSUserDefaults standardUserDefaults] objectForKey:RegistrationIdLocalStorageKey];

            if (registrationId)
            {
                completion(registrationId, nil);
                return;
            }

            // request new one & save
            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@?handle=%@", BackEndEndpoint, token]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSString* registrationId = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];

                    // remove quotes
                    registrationId = [registrationId substringWithRange: NSMakeRange(1, [registrationId length]-2)];

                    [[NSUserDefaults standardUserDefaults] setObject:registrationId forKey:RegistrationIdLocalStorageKey];
                    [[NSUserDefaults standardUserDefaults] synchronize];

                    completion(registrationId, nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

    O código acima implementa a lógica explicada no artigo de orientação [Registrando-se a partir do back-end do seu aplicativo][Registrando-se a partir do back-end do aplicativo], utilizando NSURLSession para realizar chamadas de REST para o back-end do seu aplicativo e NSUserDefaults para armazenar localmente o registrationId retornado pelo hub de notificação.

    Observe que essa classe requer que a propriedade **authorizationHeader** seja configurada para funcionar adequadamente. Essa propriedade é definida pela classe **ViewController** após o logon.

7.  Em ViewController.h, adicione a propriedade a seguir, que conterá o token do dispositivo:

        @property (strong, nonatomic) NSData* deviceToken;

8.  Em ViewController.m, torne a classe ViewController um UITextFieldDelegate, adicione uma propriedade para fazer referência a uma instância RegisterClient e adicione uma declaração de método privado. Sua seção de interface deve ser:

        @interface ViewController () <UITextFieldDelegate>
        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

        @property (strong, nonatomic) RegisterClient* registerClient;

        -(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;
        @end

    O método privado será utilizado para criar o cabeçalho Autorização para realizar uma autenticação Básica com o back-end do aplicativo.

   > [AZURE.NOTE] Este não é um esquema de autenticação seguro; você deve substituir a implementação do **createAndSetAuthenticationHeaderWithUsername:AndPassword:** pelo seu mecanismo de autenticação específico que gera um token de autenticação a ser consumido pela classe do cliente de registro, p. ex., OAuth, Active Directory.

1.  Em seguida, na seção de implementação de ViewController.m, adicione o código a seguir:

        -(void) setDeviceToken: (NSData*) deviceToken
        {
            _deviceToken = deviceToken;
            self.LogInButton.enabled = YES;
        }

        -(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;
        {
            NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

            NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

            self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData encoding:NSUTF8StringEncoding];
        }

        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }

    Observe como a configuração do token do dispositivo ativa o botão de logon. Isso ocorre porque, como parte da ação de logon, o controlador de exibição se registrará para notificações por push no back-end do aplicativo. Como resultado, queremos evitar que o usuário pressione o botão de Logon antes que o token do dispositivo tenha sido configurado adequadamente. Em seu aplicativo, você pode querer desacoplar o logon do registro de push, contanto que o primeiro aconteça antes desse último.

2.  Em ViewController.m, adicione uma constante para seu ponto de extremidade do back-end e preencha as implementações dos métodos de ação para os UIButtons. Lembre-se de substituir o espaço reservado pelo ponto de extremidade do back-end utilizado na seção anterior.

        - (IBAction)LogInAction:(id)sender {
            // create authentication header and set it in register client
            NSString* username = self.UsernameField.text;
            NSString* password = self.PasswordField.text;

            [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

            __weak ViewController* selfie = self;
            [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil andCompletion: ^(NSError* error) {
                if (!error) {
                    dispatch_async(dispatch_get_main_queue(), ^{
                        selfie.SendPushButton.enabled = YES;

                        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Success" message:
                                              @"Registered successfully!" delegate:nil cancelButtonTitle:
                                              @"OK" otherButtonTitles:nil, nil];
                        [alert show];
                    });
                }
            }];
        }

        NSString *const SendNotificationEndpoint = @"{backend endpoint}/api/notifications";

        - (IBAction)SendPushAction:(id)sender {
            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:SendNotificationEndpoint];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.registerClient.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || httpResponse.statusCode != 200)
                {
                    NSLog(@"Error status: %d, request: %@", httpResponse.statusCode, error);
                }
            }];
            [dataTask resume];
        }

3.  Por fim, em **ViewDidLoad**, adicione o código a seguir para instanciar a instância RegisterClient e configurar o que foi delegado para seus campos de texto.

        self.UsernameField.delegate = self;
        self.PasswordField.delegate = self;
        self.registerClient = [[RegisterClient alloc] init];

4.  Agora, no **AppDelegate.m**, remova todo o conteúdo do método **application:didRegisterForPushNotificationWithDeviceToken:** e substitua-o por:

        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;

    Isso garante que o controlador de exibição contenha o token do dispositivo mais recente recuperado dos APNs.

5.  Ainda em **AppDelegate.m**, certifique-se de ter o seguinte método:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                  [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
                                  @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

## Executar o aplicativo

Para executar o aplicativo, faça o seguinte:

1.  Certifique-se de que o **AppBackend** esteja implantado no Azure. Se estiver usando o Visual Studio, execute o aplicativo da API Web **AppBackend**. Uma página da Web do ASP.NET será exibida.

2.  No XCode, execute o aplicativo em um dispositivo iOS físico (as notificações por push não funcionarão no simulador).

3.  Na interface do usuário do aplicativo iOS, insira um nome de usuário e senha. Pode ser qualquer cadeia de caracteres, mas elas devem ter o mesmo valor.

4.  Na interface do usuário do aplicativo iOS, clique em **Logon**. Em seguida, clique em **Enviar push**.

  [Windows Universal]: /pt-br/documentation/articles/notification-hubs-windows-dotnet-notify-users/ "Windows Universal"
  [iOS]: /pt-br/documentation/articles/notification-hubs-/ "iOS"
  [Android]: /pt-br/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/ "Android"
  [Registrando-se a partir do back-end do aplicativo]: http://msdn.microsoft.com/en-us/library/dn743807.aspx
  [Introdução aos Hubs de Notificação (iOS)]: http://azure.microsoft.com/pt-br/documentation/articles/notification-hubs-ios-get-started/
  [notification-hubs-aspnet-backend-notifyusers]: ../includes/notification-hubs-aspnet-backend-notifyusers.md
  []: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users1.png
