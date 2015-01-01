<properties title="Azure Notification Hubs Notify Users" pageTitle="Notificação de Usuários nos Hubs de Notificação do Azure" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in Objective-C using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="yuaxu" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="11/22/2014" ms.author="yuaxu" />

#Notificação de Usuários nos Hubs de Notificação do Azure

<div class="dev-center-tutorial-selector sublanding">
    	<a href="/pt-br/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/pt-br/documentation/articles/notification-hubs-/" title="iOS" class="current">iOS</a>
		<a href="/pt-br/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

O suporte à notificação por push no Azure permite que você acesse uma infraestrutura de envio por push fácil de usar, multiplataforma e expansível que simplifica em muito a implementação de notificações por push para aplicativos de consumidor e empresariais para plataformas móveis. Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push a um usuário específico do aplicativo em um dispositivo específico. Um back-end da API Web ASP.NET é usado para autenticar clientes e gerar notificações, como mostrado no tópico de instrução [Registrando-se por meio do back-end do aplicativo](http://msdn.microsoft.com/pt-br/library/dn743807.aspx).

> [AZURE.NOTE] Este tutorial presume que você criou e configurou seu hub de notificação conforme descrito em [Introdução aos Hubs de Notificação (iOS)](http://azure.microsoft.com/pt-br/documentation/articles/notification-hubs-ios-get-started/). Este tutorial também é um pré-requisito para o tutorial [Push seguro (iOS)](http://azure.microsoft.com/pt-br/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/) .
> Se você estiver usando serviços móveis como seu serviço de back-end, consulte a [Versão dos Serviços Móveis](/pt-br/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/) deste tutorial.


## Criar e configurar o hub de notificação

Siga as seções de 1 a 5 em [Introdução aos Hubs de Notificação (iOS)](http://azure.microsoft.com/pt-br/documentation/articles/notification-hubs-ios-get-started/). Para obter recursos adicionais no fornecimento de dispositivos iOS, confira o guia em[Big Nerd Ranch](http://www.bignerdranch.com/we-teach/how-to-prepare/ios-device-provisioning.html).

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Modificar seu aplicativo iOS

1. Abra o aplicativo de exibição em Página Única que você criou seguindo as seções de 1 a 5 em [Introdução aos Hubs de Notificação (iOS)](http://azure.microsoft.com/pt-br/documentation/articles/notification-hubs-ios-get-started/).

> [AZURE.NOTE] Nesta seção, presumimos que você configurou seu projeto com um nome de organização vazio. Se não, você precisa prefixar o nome da organização a todos os nomes de classe.

2. Em seu Main.storyboard. adicione os seguintes componentes da biblioteca de objetos:
	+ Um UITextField com texto de espaço reservado **Nome de Usuário**
	+ Um UITextField com texto de espaço reservado**Senha**, e verifique a opção**Proteger** no Inspetor de Atributos, sob a tecla Enter do Textfield 
	+ Um UIButton com rótulo **1. Entre** e desmarque a opção **Habilitado** no Inspetor de Atributos, em Controle e Conteúdo
	+ Um UIButton com rótulo **2. Envie por Push**, e desmarque a opção **Habilitado**

	O storyboard deve ter a seguinte aparência:

    ![][IOS1]

3. Crie saídas para UITextFields e UIButtons na parte da interface do ViewController.h

	    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
		@property (weak, nonatomic) IBOutlet UITextField *PasswordField;
		@property (weak, nonatomic) IBOutlet UIButton *LogInButton;
		@property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

		- (IBAction)LogInAction:(id)sender;
		- (IBAction)SendPushAction:(id)sender;

4. Primeiro, criaremos uma classe RegisterClient para fazer interface com nosso back-end. Crie uma classe de Objetivo C chamada RegisterClient, que herde de NSObject. Em seguida, adicione o seguinte código na seção da interface RegisterClient.h:

		@property (strong, nonatomic) NSString* authenticationHeader;
		-(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;

5. Em RegisterClient.m, adicione a seguinte seção da interface:

		@interface RegisterClient ()

		@property (strong, nonatomic) NSURLSession* session;
		-(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
		-(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
		-(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSString*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;

		@end

6. Depois, adicione o código a seguir na seção de implementação e substitua o espaço reservado *{backend endpoint}* pela URL de destino utilizada para implantar o back-end do aplicativo na seção anterior.

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

	O código acima implementa a lógica explicada no artigo de orientação[Registrando-se no back-end do aplicativo](http://msdn.microsoft.com/pt-br/library/dn743807.aspx) usando NSURLSession para realizar chamadas REST ao back-end do aplicativo e NSUserDefaults para armazenar localmente o registrationId devolvido pelo hub de notificação.

	Observe que essa classe requer que a propriedade **authorizationHeader** seja configurada para funcionar adequadamente. Essa propriedade é definida pela classe **ViewController** após o logon.

7. No ViewController.h, adicione a seguinte declaração ao dispositivo de token e referência a uma instância RegisterClient:

		@property (strong, nonatomic) NSData* deviceToken;
		@property (strong, nonatomic) RegisterClient* registerClient;

8. No ViewController.m, transforme a classe ViewController em uma UITextFieldDelegate. Em seguida, adicione uma declaração de método privado:

		@interface ViewController () <UITextFieldDelegate>

		// create the Authorization header to perform Basic authentication with your app back-end
		-(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;

		@end

> [AZURE.NOTE] O trecho a seguir não é um esquema de autenticação seguro, você deve substituir a implementação de **createAndSetAuthenticationHeaderWithUsername:AndPassword:** pelo seu mecanismo de autenticação específico que gera um token de autenticação a ser consumido pela classe de cliente do registro, por exemplo, OAuth, Active Directory.

9. Em seguida, na seção de implementação de ViewController.m, adicione o código a seguir:

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

	Observe como a configuração do token do dispositivo ativa o botão de logon. Isso ocorre porque, como parte da ação de logon, o controlador de exibição registra-se para notificações por push no back-end do aplicativo. Portanto, não queremos que a ação de Logon seja acessível até que o token do dispositivo seja configurado corretamente. Você pode desacoplar o logon do registro de push, contanto que o primeiro ocorra antes do último.

10. No ViewController.m, adicione uma constante ao ponto de extremidade do back-end e use os seguintes techos para implementar os métodos de ação para os UIButtons. Substitua o ponto de extremidade do back-end de espaço reservado pela URL de destino que você usou para o seu backend.

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

11. Na função**ViewDidLoad**, adicione o código a seguir para instanciara instância RegisterClient e definir o delegado para os seus campos de texto.

		self.UsernameField.delegate = self;
		self.PasswordField.delegate = self;
		self.registerClient = [[RegisterClient alloc] init];

12. Em**AppDelegate.m**, remova todo o conteúdo do método**application:didRegisterForPushNotificationWithDeviceToken:** e substitua-o pelo seguinte para se certificar de que o controlador de exibição contenha o token de dispositivo mais recente, recuperado de APNs:

	    ViewController* rvc = (ViewController*) self.window.rootViewController;
	    rvc.deviceToken = deviceToken;

13. Finalmente em **AppDelegate.m**, verifique se você tem o seguinte método:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		                          [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		                          @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}

## Executar o aplicativo

1. No XCode, execute o aplicativo em um dispositivo iOS físico (as notificações por push não funcionarão no simulador).

2. Na interface do usuário do aplicativo iOS, insira um nome de usuário e senha. Pode ser qualquer cadeia de caracteres, mas devem ter o mesmo valor. Em seguida, clique em **Logon**.

3. Você deverá ver um pop-up informando sobre o sucesso da inscrição. Clique em **OK**.

4. Clique em**Enviar por push** e clique no botão da página inicial.Uma notificação por push aparecerá em breve.


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users1.png

<!--HONumber=35_1-->
