<properties
	pageTitle="Como usar o SDK para iOS para os Aplicativos Móveis do Azure"
	description="Como usar o SDK para iOS para os Aplicativos Móveis do Azure"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/30/2016"
	ms.author="krisragh"/>

# Como usar a Biblioteca de Cliente iOS para os Aplicativos Móveis do Azure

[AZURE.INCLUDE [App-Service-Mobile-Selector-Client-Library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia ensina a executar cenários comuns usando o mais recente [SDK de Aplicativos Móveis do Azure para iOS](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409). Se você for novo nos Aplicativos Móveis do Azure, primeiro conclua o [Início Rápido dos Aplicativos Móveis do Azure] para criar um back-end, criar uma tabela e baixar um projeto Xcode iOS pré-criado. Neste guia, abordaremos o SDK para iOS do lado do cliente. Para saber mais sobre o SDK .NET do lado do servidor para o back-end, consulte [Trabalhar com o Back-end do .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## Documentação de referência

A documentação de referência para o SDK do cliente do iOS está aqui: [Referência de cliente de aplicativos móveis do Azure](http://azure.github.io/azure-mobile-apps-ios-client/).

##<a name="Setup"></a>Configuração e Pré-requisitos

Este guia pressupõe que você tenha criado um back-end com uma tabela. Este guia pressupõe que a tabela tem o mesmo esquema das tabelas desses tutoriais. Este guia também pressupõe que em seu código, você referencia `MicrosoftAzureMobile.framework` e importa `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

##<a name="create-client"></a>Como: criar o cliente

Para acessar um back-end de Aplicativos Móveis do Azure em seu projeto, crie um `MSClient`. Substitua `AppUrl` pela URL do aplicativo. Você pode deixar `gatewayURLString` e `applicationKey` vazios. Se você configurar um gateway para autenticação, preencha `gatewayURLString` com a URL do gateway.

**Objective-C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


##<a name="table-reference"></a>Como criar uma referência de tabela

Para acessar ou atualizar dados, crie uma referência à tabela de back-end. Substitua `TodoItem` pelo nome da sua tabela

**Objective-C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```
let table = client.tableWithName("TodoItem")
```


##<a name="querying"></a>Como consultar dados

Para criar uma consulta de banco de dados, consulte o objeto `MSTable`. A consulta a seguir obtém todos os itens em `TodoItem` e registra o texto de cada item.

**Objective-C**:

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) { // error is nil if no error occured
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) { // items is NSArray of records that match query
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

**Swift**:

```
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="filtering"></a>Como filtrar dados retornados

Para filtrar os resultados, há muitas opções disponíveis.

Para filtrar usando um predicado, use `NSPredicate` e `readWithPredicate`. Os filtros a seguir retornaram dados para localizar apenas os itens pendentes não concluídos.

**Objective-C**:

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

**Swift**:

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="query-object"></a>Como usar o MSQuery

Para executar uma consulta complexa (incluindo classificação e paginação), crie um objeto de `MSQuery` diretamente ou usando um predicado:

**Objective-C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

O `MSQuery` permite que você controle vários comportamentos de consulta, incluindo o que está a seguir. Executa uma consulta `MSQuery` chamando `readWithCompletion` nela, conforme mostrado no exemplo a seguir.
* Especificar a ordem dos resultados
* Limitar quais campos retornar
* Limitar quantos registros retornar
* Especificar contagem total na resposta
* Especificar parâmetros de cadeia de consulta personalizada na solicitação
* Aplicar funções adicionais


## <a name="sorting"></a>Como classificar dados com MSQuery

Para classificar os resultados, vamos examinar um exemplo. Para primeiro classificar por ordem ascendente pelo campo `text` e descendente pelo campo `completion`, invoque `MSQuery` desta forma:

**Objective-C**:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

**Swift**:

```
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>Como limitar campos e expandir os parâmetros de cadeia de caracteres de consulta com MSQuery

Para limitar os campos a serem retornados em uma consulta, especifique os nomes dos campos na propriedade **selectFields**. Isso retorna apenas o texto e os campos preenchidos:

**Objective-C**:

```
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```
query.selectFields = ["text", "complete"]
```

Para incluir parâmetros de cadeia de caracteres de consulta adicionais na solicitação do servidor (por exemplo, porque é usado por um script personalizado do lado do servidor), popule `query.parameters` desta forma:

**Objective-C**:

```
query.parameters = @{
	@"myKey1" : @"value1",
	@"myKey2" : @"value2",
};
```

**Swift**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

##<a name="inserting"></a>Como inserir dados

Para inserir uma nova linha na tabela, crie um novo `NSDictionary` e invoque `table insert`. Os serviços móveis geram automaticamente novas colunas com base em `NSDictionary` quando a opção [Esquema dinâmico] não está desabilitada.

Se não for fornecida uma `id`, o back-end gera automaticamente uma ID exclusiva. Forneça sua própria `id` para usar endereços de email, nomes de usuários, ou seus próprios valores personalizados como ID. Fornecer sua própria identificação pode facilitar junções e lógicas de banco de dados comerciais.

O `result` contém o novo item que foi inserido. Dependendo de sua lógica de servidor, ele pode ter dados adicionais ou modificados em comparação com o que foi passado para o servidor.

**Objective-C**:

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**:

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

##<a name="modifying"></a>Como modificar dados

Para atualizar uma linha existente, modifique um item e chame `update`:

**Objective-C**:

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**:

```
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Como alternativa, forneça a ID da linha e o campo atualizado:

**Objective-C**:

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**:

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

No mínimo, o atributo `id` deve ser definido quando você faz atualizações.

##<a name="deleting"></a>Como excluir dados

Para excluir um item, invoque `delete` com o item:

**Objective-C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item ID: %@", itemId);
	}
}];
```

**Swift**:

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Como alternativa, exclua fornecendo uma ID de linha:

**Objective-C**:

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item ID: %@", itemId);
	}
}];
```

**Swift**:

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

No mínimo, o atributo `id` deve ser definido quando você faz exclusões.

##<a name="customapi"></a>Como chamar uma API personalizada

Com uma API personalizada, você pode expor qualquer funcionalidade de back-end. Ele não precisa mapear para uma operação de tabela. Não só você obtém mais controle sobre mensagens, mas pode até mesmo ler/definir os cabeçalhos e alterar o formato do corpo da resposta. Para saber como criar uma API personalizada no back-end, leia [APIs personalizadas](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Para chamar uma API personalizada, chame `MSClient.invokeAPI` conforme mostrado abaixo. O conteúdo de solicitação e resposta de conteúdo é tratado como JSON. Para usar outros tipos de mídia, [use a outra sobrecarga de `invokeAPI`](http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:)

Para fazer uma solicitação `GET` em vez de uma solicitação `POST`, defina o parâmetro de `HTTPMethod` como `"GET"` e o parâmetro `body` como `nil` (já que as solicitações GET não têm corpos de mensagem). Se sua API personalizada dá suporte a outros verbos HTTP, altere o `HTTPMethod` adequadamente.

**Objective-C**:
```
    [self.client invokeAPI:@"sendEmail"
                      body:@{ @"contents": @"Hello world!" }
                HTTPMethod:@"POST"
                parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
                   headers:nil
                completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                    if(error) {
                        NSLog(@"ERROR %@", error);
                    } else {
                        // Do something with result
                    }
                }];
```

**Swift**:

```
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

##<a name="templates"></a>Como registrar modelos de envio por push para enviar notificações entre plataformas

Para registrar os modelos, basta passar pelos modelos com seu método **client.push registerDeviceToken** no seu aplicativo de cliente.

**Objective-C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	}
}];
```

**Swift**:

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

Seus modelos serão do tipo NSDictionary e poderão conter vários modelos no seguinte formato:

**Objective-C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Observe que todas as marcas serão removidas imediatamente por segurança. Para adicionar marcas a instalações ou modelos dentro de instalações, consulte [Trabalhar com o SDK do servidor de back-end do .NET para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags).

Para enviar notificações usando esses modelos registrados, trabalhe com [APIs de Hubs de Notificação](https://msdn.microsoft.com/library/azure/dn495101.aspx).

##<a name="errors"></a>Como tratar erros

Quando uma chamada é feita ao serviço móvel, o bloco de conclusão contém um parâmetro `NSError`. Quando ocorre um erro, esse parâmetro é não nulo. No seu código, você deve marcar esse parâmetro e tratar o erro conforme necessário, conforme demonstrado nos trechos de código abaixo.

O arquivo [`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h) define as constantes `MSErrorResponseKey`, `MSErrorRequestKey` e `MSErrorServerItemKey` para obter mais dados relacionados ao erro, que podem ser obtidos da seguinte forma:

**Objective-C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Além disso, o arquivo define constantes para cada código de erro, que podem ser usadas da seguinte forma:

**Objective-C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Como autenticar usuários com a Biblioteca de Autenticação do Active Directory

Você pode usar a ADAL (Biblioteca de autenticação do Active Directory) para conectar os usuários ao seu aplicativo usando o Active Directory do Azure. Normalmente, será melhor usá-la em vez dos métodos `loginAsync()`, pois ela fornece uma aparência mais nativa do UX e permite personalização adicional.

1. Configure o seu back-end de aplicativo móvel para entrada no AAD seguindo o tutorial [Como configurar o Serviço de Aplicativo para logon no Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md). Complete a etapa opcional de registrar um aplicativo cliente nativo. Para iOS, recomendamos (mas não obrigamos) que o URI de redirecionamento esteja no formato `<app-scheme>://<bundle-id>`. Confira o [Início rápido da ADAL iOS](active-directory-devquickstarts-ios.md#em1-determine-what-your-redirect-uri-will-be-for-iosem) para saber mais.

2. Instale o ADAL usando o Cocoapods. Edite o Podfile para incluir o seguinte, substituindo **YOUR-PROJECT** pelo nome de seu projeto do Xcode:

		source 'https://github.com/CocoaPods/Specs.git'
		link_with ['YOUR-PROJECT']
		xcodeproj 'YOUR-PROJECT'
e o Pod:

		pod 'ADALiOS'

3. Usando o Terminal, execute `pod install` no diretório que contém o projeto e abra o espaço de trabalho do Xcode gerado (não o projeto).

4. Adicione o código abaixo ao seu aplicativo, de acordo com a linguagem que você está usando. Em cada um, faça as seguintes substituições:

* Substitua **INSERT-AUTHORITY-HERE** pelo nome do locatário onde você provisionou o aplicativo. O formato deve ser https://login.windows.net/contoso.onmicrosoft.com. Este valor pode ser copiado da guia Domínio no Active Directory do Azure no [Portal Clássico do Azure].

* Substitua **INSERT-RESOURCE-ID-HERE** pela ID do cliente do seu back-end de aplicativo móvel. Você pode obter isso na guia **Avançadas** em **Configurações do Azure Active Directory** no portal.

* Substitua **INSERT-CLIENT-ID-HERE** pela ID do cliente copiada do aplicativo cliente nativo.

* Substitua **INSERT-REDIRECT-URI-HERE** pelo ponto de extremidade _/.auth/login/done_ do site, usando o esquema HTTPS. Esse valor deve ser similar a \_https://contoso.azurewebsites.net/.auth/login/done_.

**Objective-C**:

	#import <ADALiOS/ADAuthenticationContext.h>
	#import <ADALiOS/ADAuthenticationSettings.h>
	// ...
	- (void) authenticate:(UIViewController*) parent
	           completion:(void (^) (MSUser*, NSError*))completionBlock;
	{
	    NSString *authority = @"INSERT-AUTHORITY-HERE";
	    NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
	    NSString *clientId = @"INSERT-CLIENT-ID-HERE";
	    NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
	    ADAuthenticationError *error;
	    ADAuthenticationContext authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
	    authContext.parentController = parent;
	    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
	    [authContext acquireTokenWithResource:resourceId
	                                 clientId:clientId
	                              redirectUri:redirectUri
	                          completionBlock:^(ADAuthenticationResult *result) {
	                              if (result.status != AD_SUCCEEDED)
	                              {
	                                  completionBlock(nil, result.error);;
	                              }
	                              else
	                              {
	                                  NSDictionary *payload = @{
	                                                            @"access_token" : result.tokenCacheStoreItem.accessToken
	                                                            };
	                                  [client loginWithProvider:@"aad" token:payload completion:completionBlock];
	                              }
	                          }];
	}


**Swift**:

	// add the following imports to your bridging header:
	//		#import <ADALiOS/ADAuthenticationContext.h>
	//		#import <ADALiOS/ADAuthenticationSettings.h>

	func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
		let authority = "INSERT-AUTHORITY-HERE"
		let resourceId = "INSERT-RESOURCE-ID-HERE"
		let clientId = "INSERT-CLIENT-ID-HERE"
		let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
		var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
		let authContext = ADAuthenticationContext(authority: authority, error: error)
		authContext.parentController = parent
		ADAuthenticationSettings.sharedInstance().enableFullScreen = true
		authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
		        if result.status != AD_SUCCEEDED {
		            completion(nil, result.error)
		        }
		        else {
		            let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
		            client.loginWithProvider("aad", token: payload, completion: completion)
		        }
    		}
	}


## <a name="facebook-sdk"></a>Instruções: autenticar usuários com o SDK do Facebook para iOS

Você pode usar o SDK do Facebook para iOS para conectar os usuários ao seu aplicativo usando o Facebook. Normalmente, será melhor usá-lo em vez dos métodos `loginAsync()`, pois ele fornece uma aparência mais nativa do UX e permite personalização adicional.

1. Configure o seu back-end de aplicativo móvel para entrar no Facebook seguindo o tutorial [Como configurar o Serviço de Aplicativo para logon do Facebook](app-service-mobile-how-to-configure-facebook-authentication.md).

2. Instale o SDK do Facebook para iOS seguindo a documentação [Facebook SDK para iOS - Introdução](https://developers.facebook.com/docs/ios/getting-started). Em vez de criar um novo aplicativo, você pode adicionar a plataforma do iOS para o seu registro existente.

    A documentação do Facebook inclui algum código Objective-C no Representante do Aplicativo. Se você estiver usando **Swift**, poderá usar as seguintes conversões para AppDelegate.swift:
  
		// Add the following import to your bridging header:
		//		#import <FBSDKCoreKit/FBSDKCoreKit.h>
		
		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
			FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
			// Add any custom logic here.
			return true
		}

		func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
			let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
			// Add any custom logic here.
			return handled
		}

3. Além de adicionar `FBSDKCoreKit.framework` ao seu projeto, adicione também uma referência ao `FBSDKLoginKit.framework` da mesma maneira.

4. Adicione o código abaixo ao seu aplicativo, de acordo com a linguagem que você está usando.

**Objective-C**:

	#import <FBSDKLoginKit/FBSDKLoginKit.h>
	#import <FBSDKCoreKit/FBSDKAccessToken.h>
	// ...
	- (void) authenticate:(UIViewController*) parent
	           completion:(void (^) (MSUser*, NSError*)) completionBlock;
	{	    
	    FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
	    [loginManager
	     logInWithReadPermissions: @[@"public_profile"]
	     fromViewController:parent
	     handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
	         if (error) {
	             completionBlock(nil, error);
	         } else if (result.isCancelled) {
	             completionBlock(nil, error);
	         } else {
	             NSDictionary *payload = @{
	                                       @"access_token":result.token.tokenString
	                                       };
	             [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
	         }
	     }];
	}


**Swift**:

	// Add the following imports to your bridging header:
	//		#import <FBSDKLoginKit/FBSDKLoginKit.h>
	//		#import <FBSDKCoreKit/FBSDKAccessToken.h>
	
	func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
		let loginManager = FBSDKLoginManager()
		loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
			if (error != nil) {
				completion(nil, error)
			}
			else if result.isCancelled {
				completion(nil, error)
			}
			else {
				let payload: [String: String] = ["access_token": result.token.tokenString]
				client.loginWithProvider("facebook", token: payload, completion: completion)
			}
		}
	}

## <a name="twitter-fabric"></a>Instruções: autenticar usuários com a Twitter Fabric para iOS

Você pode usar a Fabric para iOS para desconectar os usuários em seu aplicativo usando o Twitter. Normalmente, será melhor usá-lo em vez dos métodos `loginAsync()`, pois ele fornece uma aparência mais nativa do UX e permite personalização adicional.

1. Configurar o seu back-end de aplicativos móveis para entrar no Twitter, seguindo o tutorial [Como configurar o Serviço de Aplicativo para fazer logon no Twitter](app-service-mobile-how-to-configure-twitter-authentication.md).

2. Adicione a Fabric ao seu projeto seguindo a documentação [Fabric para iOS - Introdução](https://docs.fabric.io/ios/fabric/getting-started.html) e a configuração TwitterKit.

    > [AZURE.NOTE] Por padrão, a Fabric criará um novo aplicativo do Twitter para você. Você pode alterar isso registrando a chave do consumidor e o segredo do consumidor criado anteriormente usando os trechos de código abaixo. Como alternativa, você pode substituir os valores de chave do consumidor e segredo do consumidor que fornece ao Serviço de Aplicativo com os valores que vê no [Painel do Fabric](https://www.fabric.io/home). Se você escolher essa opção, certifique-se de definir a URL de retorno de chamada para um valor de espaço reservado, como `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

	Se você optar por usar os segredos que criou anteriormente, adicione o seguinte ao seu Representante de Aplicativo:
	
	**Objective-C**:

		#import <Fabric/Fabric.h>
		#import <TwitterKit/TwitterKit.h>
		// ...
		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
		    [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
		    [Fabric with:@[[Twitter class]]];
			// Add any custom logic here.
		    return YES;
		}
		
	**Swift**:
	
		import Fabric
		import TwitterKit
		// ...
		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
			Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
			Fabric.with([Twitter.self])
			// Add any custom logic here.
			return true
		}
	
3. Adicione o código abaixo ao seu aplicativo, de acordo com a linguagem que você está usando.

**Objective-C**:

	#import <TwitterKit/TwitterKit.h>
	// ...
	- (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
	{
		[[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
			if (session) {
				NSDictionary *payload = @{
											@"access_token":session.authToken,
											@"access_token_secret":session.authTokenSecret
										};
				[client loginWithProvider:@"twitter" token:payload completion:completionBlock];
			} else {
				completionBlock(nil, error);
			}
	    }];
	}

**Swift**:

	import TwitterKit
	// ...
	func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
		let client = self.table!.client
		Twitter.sharedInstance().logInWithCompletion { session, error in
			if (session != nil) {
				let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
				client.loginWithProvider("twitter", token: payload, completion: completion)
			} else {
				completion(nil, error)
			}
		}
	}

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Início Rápido dos Aplicativos Móveis do Azure]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Esquema dinâmico]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=AcomDC_0706_2016-->