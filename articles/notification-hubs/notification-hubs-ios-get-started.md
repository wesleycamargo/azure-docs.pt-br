<properties
	pageTitle="Introdução aos Hubs de Notificação do Azure para aplicativos do iOS | Microsoft Azure"
	description="Neste tutorial, você aprenderá a usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo do iOS."
	services="notification-hubs"
	documentationCenter="ios"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="09/03/2015"
	ms.author="wesmc"/>

# Introdução aos Hubs de Notificação para aplicativos do iOS

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Visão geral

Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo iOS. Você criará um aplicativo iOS em branco que recebe notificações por push usando o serviço de Notificação por Push da Apple (APNs). Ao finalizar, você poderá usar seu hub de notificação para transmitir notificações por push a todos os dispositivos que executam seu aplicativo.

Este tutorial demonstra o cenário de transmissão simples usando Hubs de Notificação.

##Pré-requisitos

Este tutorial exige o seguinte:

+ [SDK do iOS dos Serviços Móveis]
+ [Xcode 6][Install Xcode]
+ Um dispositivo compatível com o iOS 8 (ou versão posterior)
+ Associação no Programa de Desenvolvedores de iOS

   >[AZURE.NOTE]Devido aos requisitos de configuração das notificações por push, você deve implantar e testá-las em um dispositivo compatível com o iOS (iPhone ou iPad), em vez de usar o Simulador de iOS.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre os Hubs de Notificação para aplicativos do iOS.

> [AZURE.NOTE]Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).

[AZURE.INCLUDE [Hubs de notificação habilitam notificações por push da Apple](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##Configurar seu Hub de Notificação

Esta seção apresenta como criar e configurar um novo hub de notificação com o certificado de push criado. Se deseja usar um hub de notificação que você já criou, ignore as etapas 2 a 5.


1. No Acesso ao Conjunto de Chaves, clique com o botão direito do mouse no novo certificado de push criado na categoria **Certificados**. Clique em **Exportar**, nomeie o arquivo, selecione o formato **.p12** e depois clique em **Salvar**.

    ![][1]

	Anote o nome do arquivo e o local do certificado exportado.

	>[AZURE.NOTE]Este tutorial cria um arquivo QuickStart.p12. O nome do arquivo e o local podem ser diferentes.

2. Entre no [portal do Azure] e clique em **+NOVO** na parte inferior da tela.

3. Clique em **Serviços de Aplicativos**, em **Barramento de Serviço**, em **Hub de Notificação** e em **Criação Rápida**.

   	![][2]

4. Digite um nome para o hub de notificação, selecione a região desejada e clique em **Criar um novo Hub de Notificação**.

   	![][3]

5. Clique no namespace que você acabou de criar (geralmente ***nome do hub de notificação*-ns**) para abrir seu painel.

   	![][4]

6. Clique na guia **Hubs de Notificação** na parte superior e no hub de notificação que você acabou de criar.

   	![][5]

7. Clique na guia **Configurar** na parte superior e, em seguida, clique no botão **Carregar** nas configurações de notificação da Apple para carregar a impressão digital do certificado. Depois, selecione o certificado **.p12** que você exportou anteriormente e a senha do certificado. Lembre-se de escolher se deseja usar a **Produção** (se desejar enviar notificações por push para os usuários que adquiriram seu aplicativo na loja) ou o serviço de push da **Área Restrita** (durante o desenvolvimento).

   	![][6]

8. Clique na guia **Painel** na parte superior e, em seguida, clique em **Exibir Cadeia de Conexão**. Anote as duas cadeias de conexão.

   	![][7]

Seu hub de notificação agora está configurado para funcionar com o APNs e você tem as cadeias de conexão para registrar seu aplicativo e enviar notificações.

##Conectar seu aplicativo ao hub de notificação

1. No Xcode, crie um novo projeto do iOS e selecione o modelo **Aplicativo de Modo de Exibição Único**.

   	![][8]

2. Ao definir as opções para o novo projeto, lembre-se de usar os mesmos **Nome do Produto** e **Identificador Organizacional** que você usou quando configurou a ID do pacote no portal de desenvolvimento da Apple.

	![][11]

3. Em **Destinos**, clique no nome do projeto, clique na guia **Configurações de Compilação** e expanda **Identidade de Assinatura de Código**. Depois, em **Depurar**, defina sua identidade de assinatura de código. Alterne **Níveis** de **Básico** para **Todos** e defina o **Perfil de Provisionamento** para o perfil de provisionamento que você criou anteriormente.

	Se você não vir o novo perfil de provisionamento que você criou no Xcode, tente atualizar os perfis da sua identidade de assinatura. Clique em **Xcode** na barra de menus, em **Preferências**, na guia **Conta**, no botão **Exibir Detalhes**, em sua identidade de assinatura e depois clique no botão Atualizar no canto inferior direito.

   	![][9]

4. Baixe a versão 1.2.4 do [SDK de Serviços Móveis do iOS] e descompacte o arquivo. No Xcode, clique com o botão direito do mouse no projeto e clique na opção **Adicionar Arquivos a** para adicionar a pasta **WindowsAzureMessaging.framework** ao seu projeto do Xcode. Selecione **Copiar itens se necessário** e depois clique em **Adicionar**.

   	![][10]

5. Abra seu arquivo AppDelegate.h e adicione a seguinte diretiva de importação:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. No arquivo AppDelegate.m, adicione o seguinte código ao método `didFinishLaunchingWithOptions` de acordo com a sua versão do iOS. Esse código registra seu identificador de dispositivo nas APNs:

	Para iOS 8:

	 	UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
												UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

    	[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    	[[UIApplication sharedApplication] registerForRemoteNotifications];

	Para versões do iOS anteriores a 8:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];


7. No mesmo arquivo, adicione os seguintes métodos e substitua os espaços reservados da cadeia de caracteres literal pelo *nome do hub* e a *DefaultListenSharedAccessSignature* que você anotou anteriormente. Esse código fornece o token do dispositivo ao hub de notificação para que o hub de notificação possa enviar notificações:

	    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<Enter your listen connection string>"
										notificationHubPath:@"<Enter your hub name>"];

		    [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
		        if (error != nil) {
		            NSLog(@"Error registering for notifications: %@", error);
		        }
				else {
				    [self MessageBox:@"Registration Status" message:@"Registered"];
				}
	    	}];
		}

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}


8. No mesmo arquivo, adicione o seguinte método para exibir um **UIAlert** caso a notificação seja recebida enquanto o aplicativo estiver ativo:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
		}

8. Compile e execute o aplicativo no dispositivo para verificar se não há falhas.

## Enviar notificações


Você pode testar o recebimento das notificações no aplicativo enviando notificações no portal do Azure por meio da guia de depuração no hub de notificação, como mostrado na tela abaixo. ![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

![][31]

1. No Xcode, abra Main.storyboard e adicione os seguintes componentes da interface do usuário da biblioteca de objetos para permitir que o usuário envie notificações por push no aplicativo:

	- Um rótulo sem texto de rótulo. Ele será usado para relatar erros no envio de notificações. A propriedade **Lines** deve ser definida como **0** para que ela dimensione automaticamente as margens restritas à direita e à esquerda e na parte superior da exibição.
	- Um campo de texto com o texto **Espaço Reservado** definido como **Inserir Mensagem de Notificação**. Restrinja o campo logo abaixo do rótulo, conforme mostrado abaixo. Defina o Controlador de Exibição como o delegado de saída.
	- Um botão chamado **Enviar Notificação** restrito logo abaixo do campo de texto e no centro horizontal.

	O modo de exibição deve ser semelhante ao seguinte:

	![][32]


2. Abra o arquivo ViewController.h e adicione as seguintes instruções `#import` e `#define`. Substitua a cadeia de caracteres literal no espaço reservado pela cadeia de conexão real de *DefaultFullSharedAccessSignature* e pelo *nome do hub*.


		#import <CommonCrypto/CommonHMAC.h>

		#define API_VERSION @"?api-version=2015-01"
		#define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
		#define HUBNAME @"<Enter the name of your hub>"


3. Adicione saídas para o rótulo e o campo de texto conectados à exibição e atualize a definição `interface` para dar suporte a `UITextFieldDelegate` e `NSXMLParserDelegate`. Adicione as três declarações de propriedade mostradas abaixo para ajudar a dar suporte a chamar à API REST e fazer a análise da resposta.

	O arquivo ViewController.h deve ser semelhante ao seguinte:

		#import <UIKit/UIKit.h>
		#import <CommonCrypto/CommonHMAC.h>

		#define API_VERSION @"?api-version=2015-01"
		#define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
		#define HUBNAME @"<Enter the name of your hub>"

		@interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
		{
			NSXMLParser *xmlParser;
		}

		// Make sure these outlets are connected to your UI by ctrl+dragging
		@property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
		@property (weak, nonatomic) IBOutlet UILabel *sendResults;

		@property (copy, nonatomic) NSString *statusResult;
		@property (copy, nonatomic) NSString *currentElement;

		@end


4. Abra ViewController.m e adicione o seguinte código para analisar a cadeia de conexão de *DefaultFullSharedAccessSignature*. Como mencionamos na [referência da API REST](http://msdn.microsoft.com/library/azure/dn495627.aspx), essas informações analisadas serão usadas para gerar um token de SaS para o cabeçalho de solicitação da **Autorização**.

		NSString *HubEndpoint;
		NSString *HubSasKeyName;
		NSString *HubSasKeyValue;

		-(void)ParseConnectionString
		{
			NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
			NSString *part;

			if ([parts count] != 3)
			{
				NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
					reason:@"Invalid full shared access connection string" userInfo:nil];

				@throw parseException;
			}

			for (part in parts)
			{
				if ([part hasPrefix:@"Endpoint"])
				{
					HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
				}
				else if ([part hasPrefix:@"SharedAccessKeyName"])
				{
					HubSasKeyName = [part substringFromIndex:20];
				}
				else if ([part hasPrefix:@"SharedAccessKey"])
				{
					HubSasKeyValue = [part substringFromIndex:16];
				}
			}
		}

5. Em ViewController.m, atualize o método `viewDidLoad` para analisar a cadeia de conexão quando a exibição for carregada. Adicione também os métodos de utilitário mostrados abaixo.


		- (void)viewDidLoad
		{
			[super viewDidLoad];
			[self ParseConnectionString];
		}

		-(NSString *)CF_URLEncodedString:(NSString *)inputString
		{
		   return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
				NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
		}

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}





6. Em ViewController.m, adicione o seguinte código para gerar o token de autorização de SaS que será fornecido no cabeçalho da **Autorização**, como mencionamos na [Referência da API REST](http://msdn.microsoft.com/library/azure/dn495627.aspx).

		-(NSString*) generateSasToken:(NSString*)uri
		{
			NSString *targetUri;
			NSString* utf8LowercasedUri = NULL;
			NSString *signature = NULL;
			NSString *token = NULL;

			@try
			{
				// Add expiration
				uri = [uri lowercaseString];
				utf8LowercasedUri = [self CF_URLEncodedString:uri];
				targetUri = [utf8LowercasedUri lowercaseString];
				NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
				int expiresInMins = 60; // 1 hour
				expiresOnDate += expiresInMins * 60;
				UInt64 expires = trunc(expiresOnDate);
				NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];

				// Get an hmac_sha1 Mac instance and initialize with the signing key
				const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
				const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
				unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
				CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
				NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
				signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];

				// Construct authorization token string
				token = [NSString stringWithFormat:@"SharedAccessSignature sr=%@&sig=%@&se=%qu&skn=%@",
					targetUri, signature, expires, HubSasKeyName];
			}
			@catch (NSException *exception)
			{
				[self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
			}
			@finally
			{
				if (utf8LowercasedUri != NULL)
					CFRelease((CFStringRef)utf8LowercasedUri);
				if (signature != NULL)
				CFRelease((CFStringRef)signature);
			}

			return token;
		}


7. Use Ctrl + arrastar do botão **Enviar Notificação** para ViewController.m para adicionar uma ação ao evento **Touch Down** que executa a chamada à API REST usando o código a seguir.

		- (IBAction)SendNotificationMessage:(id)sender
		{
			self.sendResults.text = @"";
			[self SendNotificationRESTAPI];
		}

		- (void)SendNotificationRESTAPI
		{
		    NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];

			// Apple Notification format of the notification message
		    NSString *json = [NSString stringWithFormat:@"{"aps":{"alert":"%@"}}",
								self.notificationMessage.text];

			// Construct the message's REST endpoint
			NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
												HUBNAME, API_VERSION]];

			// Generate the token to be used in the authorization header
			NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

			//Create the request to add the APNs notification message to the hub
			NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
			[request setHTTPMethod:@"POST"];
			[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

			// Signify Apple notification format
			[request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

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
		        	xmlParser = [[NSXMLParser alloc] initWithData:data];
		        	[xmlParser setDelegate:self];
		       		[xmlParser parse];
		    	}
		    }];
		    [dataTask resume];
		}


8. No ViewController.m, adicione o seguinte método de delegado para dar suporte ao fechamento do teclado para o campo de texto. Use Ctrl + arrastar do campo de texto para o ícone do Controlador de Exibição no designer de interface para definir o controlador de exibição como o representante de saída.

		//===[ Implement UITextFieldDelegate methods ]===

		-(BOOL)textFieldShouldReturn:(UITextField *)textField
		{
			[textField resignFirstResponder];
			return YES;
		}


9. Em ViewController.m, adicione os seguintes métodos de representante para dar suporte à análise da resposta usando `NSXMLParser`.

		//===[ Implement NSXMLParserDelegate methods ]===

		-(void)parserDidStartDocument:(NSXMLParser *)parser
		{
		    self.statusResult = @"";
		}

		-(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
			namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
			attributes:(NSDictionary *)attributeDict
		{
		    NSString * element = [elementName lowercaseString];
		    NSLog(@"*** New element parsed : %@ ***",element);

		    if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
		    {
		        self.currentElement = element;
		    }
		}

		-(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
		{
		    self.statusResult = [self.statusResult stringByAppendingString:
		        [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
		}

		-(void)parserDidEndDocument:(NSXMLParser *)parser
		{
			// Set the status label text on the UI thread
			dispatch_async(dispatch_get_main_queue(),
			^{
				[self.sendResults setText:self.statusResult];
			});
		}



10. Compile o projeto e verifique se não há erros.



Você encontrará todas as cargas de notificação possíveis no [Guia de programação de notificação local e por push] da Apple.



##Testar seu aplicativo

Para testar as notificações por push no iOS, você deve implantar o aplicativo em um dispositivo. Não é possível enviar notificações por push da Apple com o Simulador do iOS.

1. Execute o aplicativo, verifique se o registro foi bem-sucedido e pressione **OK**.

	![][33]

2. Toque dentro do campo de texto para inserir uma mensagem de notificação. Depois, pressione o botão **Enviar** no teclado ou o botão **Enviar Notificação** no modo de exibição para enviar a mensagem de notificação.

	![][34]

3. A notificação é enviada para todos os dispositivos registrados para receber a notificação.

	![][35]

Se você tiver problemas ou recomendações para melhorar este tutorial para todos os leitores, deixe um comentário na seção Disqus abaixo.


##Próximas etapas

Neste exemplo simples, você envia notificações para todos os seus dispositivos iOS. Para selecionar usuários de destino específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá ler [Usar Hubs de Notificação para enviar notícias mais recentes]. Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação].



<!-- Images. -->

[1]: ./media/notification-hubs-ios-get-started/notification-hubs-export-cert-p12.png
[2]: ./media/notification-hubs-ios-get-started/notification-hubs-create-from-portal.png
[3]: ./media/notification-hubs-ios-get-started/notification-hubs-create-from-portal2.png
[4]: ./media/notification-hubs-ios-get-started/notification-hubs-select-from-portal.png
[5]: ./media/notification-hubs-ios-get-started/notification-hubs-select-from-portal2.png
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-connection-strings.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-debug-hub-ios.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[SDK de Serviços Móveis do iOS]: http://go.microsoft.com/fwLink/?LinkID=266533
[SDK do iOS dos Serviços Móveis]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[portal do Azure]: https://manage.windowsazure.com/
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Usar Hubs de Notificação para enviar notificações por push aos usuários]: notification-hubs-aspnet-backend-ios-notify-users.md
[Usar Hubs de Notificação para enviar notícias mais recentes]: notification-hubs-ios-send-breaking-news.md

[Guia de programação de notificação local e por push]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1

<!---HONumber=Sept15_HO2-->