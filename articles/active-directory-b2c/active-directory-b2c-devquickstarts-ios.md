<properties
	pageTitle="Azure Active Directory B2C: chamar uma API Web de um aplicativo do iOS usando bibliotecas de terceiros | Microsoft Azure"
	description="Este artigo mostrará como criar um aplicativo de ";lista de tarefas pendentes"; do iOS que chama uma API Web do Node.js usando tokens de portador OAuth 2.0 usando uma biblioteca de terceiros"
	services="active-directory-b2c"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags ms.service="active-directory-b2c" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="objectivec" ms.topic="hero-article"

	ms.date="07/26/2016"
	ms.author="brandwe"/>

# Azure AD B2C : chamar uma API Web de um aplicativo iOS usando uma biblioteca de terceiros

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

A plataforma de identidade da Microsoft usa padrões abertos, como OAuth2 e OpenID Connect. Isso permite aos desenvolvedores aproveitar qualquer biblioteca que queiram integrar aos nossos serviços. Para auxiliar os desenvolvedores a usar nossa plataforma com outras bibliotecas, escrevemos alguns passo a passos como este para demonstrar como configurar bibliotecas de terceiros para que elas se conectem à plataforma de identidade da Microsoft. A maioria das bibliotecas que implementam [a especificação RFC6749 do OAuth2](https://tools.ietf.org/html/rfc6749) poderá conectar-se à Plataforma de Identidade da Microsoft.


Se você ainda não conhece o OAuth2 ou o OpenID Connect, grande parte desta configuração de exemplo poderá não fazer muito sentido para você. Recomendamos que você examine uma breve [visão geral do protocolo que documentamos aqui](active-directory-b2c-reference-protocols.md).

> [AZURE.NOTE]
    Alguns recursos de nossa plataforma que possuem uma expressão nesses padrões, como o Acesso Condicional e o gerenciamento de política do Intune, exigem a utilização das nossas Bibliotecas de Identidades do Microsoft Azure de software livre.
   
Nem todos os recursos e cenários do Azure Active Directory têm suporte na plataforma B2C. Para determinar se você deve usar a plataforma B2C, leia sobre as [limitações de B2C](active-directory-b2c-limitations.md).


## Obter um diretório AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos etc. Se você ainda não tiver um, [crie um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir.

## Criar um aplicativo

Em seguida, você precisa criar um aplicativo em seu diretório B2C. Isso fornece ao AD do Azure as informações de que ele precisa para se comunicar de forma segura com seu aplicativo. Nesse caso, aplicativo e a API Web são representados por uma única **ID do Aplicativo**, pois abrangem um aplicativo lógico. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). É necessário que você:

- Inclua um **dispositivo móvel** no aplicativo.
- Copie a **ID do Aplicativo** atribuída ao seu aplicativo. Você também precisará dela mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Criar suas políticas

No Azure AD B2C, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Esse aplicativo contém uma experiência de identidade: uma combinação de entrada e inscrição. Você precisa criar esta política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Ao criar a política, não se esqueça de fazer o seguinte:

- Escolha o **Nome de exibição** e atributos de inscrição em sua política.
- Escolha as declarações de aplicativo **Nome de exibição** e **ID do Objeto** em todas as políticas. Você pode escolher outras declarações também.
- Copie o **Nome** de cada política depois de criá-la. Ele deve ter o prefixo `b2c_1_`. Posteriormente, você precisará do nome da política.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as políticas, você estará pronto para compilar o aplicativo.


## Baixar o código

O código para este tutorial é mantido [no GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c). Para acompanhá-lo, você pode [baixar o aplicativo como .zip](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)/archive/master.zip) ou cloná-lo:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

Ou basta baixar o código completo e começar imediatamente:

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## Baixe a biblioteca de terceiros nxoauth2 e inicie um espaço de trabalho

Para este passo a passo, usaremos o OAuth2Client do GitHub, uma biblioteca do OAuth2 para Mac OS X e o iOS (Cocoa & Cocoa touch). Essa biblioteca baseia-se no rascunho 10 da especificação OAuth2. Ela implementa o perfil de aplicativo nativo e dá suporte ao ponto de extremidade de autorização do usuário final. Isso é tudo do que precisamos para a integração à plataforma de identidade da Microsoft.

### Adicionar a biblioteca ao seu projeto usando o CocoaPods

O CocoaPods é um gerenciador de dependência para projetos do Xcode. Ele gerencia automaticamente as etapas de instalação acima.

```
$ vi Podfile
```
Adicione o seguinte a esse podfile:

```
 platform :ios, '8.0'
 
 target 'SampleforB2C' do
 
 pod 'NXOAuth2Client'
 
 end
```

Agora, carregue o podfile usando o cocoapods. Isso criará um novo espaço de trabalho XCode que será carregado.

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## A estrutura do projeto

Temos a seguinte estrutura configurada para nosso projeto no esqueleto:

* Um **Modo de Exibição Mestre** com um painel de tarefas
* Um **Modo de Exibição Adicionar Tarefa** para os dados sobre a tarefa selecionada
* Um **Modo de Exibição de Logon** que permite que um usuário entre no aplicativo.

Entraremos em diversos arquivos no projeto para adicionar autenticação. Outras partes do código como o código visual não é similar à identidade e são fornecidos para você.

## Criar o arquivo `settings.plist` para seu aplicativo

Será mais fácil configurar o aplicativo se tivermos um local centralizado para colocar os valores de configuração. Isso também ajuda a entender o que cada configuração faz no aplicativo. Aproveitaremos a *Lista de Propriedades* como uma maneira de fornecer esses valores para o aplicativo.

* Criar/abrir o arquivo `settings.plist` em `Supporting Files` no espaço de trabalho do aplicativo

* Insira os valores a seguir (vamos abordá-los detalhadamente em breve)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>accountIdentifier</key>
	<string>B2C_Acccount</string>
	<key>clientID</key>
	<string><client ID></string>
	<key>clientSecret</key>
	<string></string>
	<key>authURL</key>
	<string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
	<key>loginURL</key>
	<string>https://login.microsoftonline.com/<tenant name>/login</string>
	<key>bhh</key>
	<string>urn:ietf:wg:oauth:2.0:oob</string>
	<key>tokenURL</key>
	<string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
	<key>keychain</key>
	<string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
	<key>contentType</key>
	<string>application/x-www-form-urlencoded</string>
	<key>taskAPI</key>
	<string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

Vamos examinar esses detalhes.


Para `authURL`, `loginURL`, `bhh`, `tokenURL`, você observará que precisa preencher o nome do locatário. Esse é o nome do locatário B2C que foi atribuído a você. Por exemplo, `kidventusb2c.onmicrosoft.com`. Se você usar as nossas Bibliotecas de Identidades do Microsoft Azure de software livre, obteremos esses dados para você usando nosso ponto de extremidade de metadados. Fizemos o trabalho pesado de extrair esses valores para você.

Para saber mais sobre nomes de locatário B2C, confira: [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)

O valor `keychain` é o contêiner que a biblioteca NXOAuth2Client usará na criação de um conjunto de chaves para armazenar seus tokens. Se quiser obter SSO entre vários aplicativos, você poderá especificar o mesmo conjunto de chaves em cada um dos aplicativos, bem como solicitar o uso desse conjunto de chaves em seus direitos de XCode. Isso é abordado na documentação da Apple.

O `<policy name>` no fim de cada URL consiste nos locais em que você colocaria a política que criou acima. O aplicativo chamará essas políticas dependendo do fluxo.

O `taskAPI` é o Ponto de Extremidade REST que chamaremos com seu token B2C para adicionar tarefas ou consultar tarefas existentes. Isso foi configurado especificamente para este exemplo. Não é necessário alterá-lo para que o exemplo funcione.

O restante desses valores é necessário para usar a biblioteca e simplesmente cria locais para você levar valores para o contexto.

Agora que o arquivo `settings.plist` foi criado, precisamos de código para lê-lo.

## Configurar uma classe AppData para ler as configurações

Vamos criar um arquivo simples que analisa apenas nosso arquivo `settngs.plist` criado acima e torna as configurações disponíveis no futuro para qualquer classe. Como não queremos criar uma nova cópia dos dados sempre que uma classe a solicita, usaremos um padrão Singleton e retornaremos apenas a mesma instância cada vez que uma solicitação for feita para as configurações

* Criar um arquivo `AppData.h`:

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* Criar um arquivo `AppData.m`:

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

Agora podemos facilmente acessar os dados apenas chamando `  AppData *data = [AppData getInstance];` em qualquer uma das classes, como você verá a seguir.



## Configurar a biblioteca NXOAuth2Client no AppDelegate

A biblioteca NXOAuthClient requer alguns valores para sua configuração. Assim que estiver concluída, você poderá usar o token obtido para chamar a API REST. Como sabemos que o `AppDelegate` será chamado sempre que carregarmos o aplicativo, faz sentido colocarmos nossos valores de configuração no arquivo.
* Abrir o arquivo `AppDelegate.m`

* Importe alguns arquivos de cabeçalho que usaremos mais tarde.

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* Adicionar o método `setupOAuth2AccountStore` a AppDelegate

Precisamos criar um AccountStore e, depois, fornecer os dados que acabamos de ler do arquivo `settings.plist`.

Há algumas coisas para as quais você deve estar atento em relação ao serviço B2C nesse ponto para tornar o código mais legível:


1. O Azure AD B2C usa a *política* conforme fornecido pelos parâmetros de consulta para atender à sua solicitação. Isso permite que o Azure Active Directory atue como um serviço independente apenas para seu aplicativo. Para fornecer esses parâmetros adicionais, precisamos fornecer o método `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:` com os parâmetros de política personalizada.

2. O Azure AD B2C usa escopos de maneira bastante semelhante a outros servidores OAuth2. No entanto, como o uso de B2C inclui a autenticação de um usuário e o acesso a recursos, alguns escopos são absolutamente necessários para que o fluxo funcione corretamente. Este é o escopo `openid`. Os SDKs de identidade da Microsoft oferecem automaticamente o escopo `openid` a você. Portanto, você não o verá na configuração de SDK. No entanto, como estamos usando uma biblioteca de terceiros, precisamos especificar esse escopo.

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
Em seguida, chame-o no AppDelegate no método `didFinishLaunchingWithOptions:`.

```
[self setupOAuth2AccountStore];
```


## Criar uma classe `LoginViewController` que usaremos para lidar com solicitações de autenticação

Usamos um modo de exibição da Web para entrar na conta. Isso nos permite solicitar ao usuário fatores adicionais, como mensagens de texto SMS (se configurado) ou o envio de mensagens de erro para o usuário. Aqui vamos configurar o modo de exibição da Web e, em seguida, escreveremos o código para manipular os retornos de chamada que ocorrerão no Modo de Exibição da Web do Serviço de Identidade da Microsoft.

* Criar uma classe `LoginViewController.h`

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

Vamos criar cada um desses métodos abaixo.

> [AZURE.NOTE] 
    Associe o `loginView` ao modo de exibição da Web real que está dentro de seu storyboard. Caso contrário, você não terá um modo de exibição da Web que pode ser exibido no momento da autenticação.

* Criar uma classe `LoginViewController.m`

* Adicionar algumas variáveis para transportar o estado durante a autenticação

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory 
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE; 
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* Substituir os métodos do Modo de Exibição da Web para manipular a autenticação

Precisamos dizer ao modo de exibição da Web o comportamento que queremos quando um usuário precisar fazer logon conforme discutido acima. Você pode simplesmente recortar e colar o código a seguir.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* Escrever código para manipular o resultado da solicitação de OAuth2

Precisaremos de um código para manipular a redirectURL proveniente do Modo de Exibição da Web. Caso ele não obtenha êxito, tentaremos novamente. Enquanto isso, a biblioteca fornecerá o erro que você pode ver no console ou com o qual lidará assincronamente.

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* Configure as fábricas de Notificação.

Criamos o mesmo método que criamos no `AppDelegate` acima, mas, desta vez, adicionaremos alguns `NSNotification`s para nos dizer o que está acontecendo no serviço. Configuramos um observador que nos informará quando algo no token for alterado. Assim que obtivermos o token, o usuário retornará ao `masterView`.



```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* Adicionar código que lida com o usuário sempre que uma solicitação é iniciada para entrada nativa

Vamos criar um método que será chamado sempre que tivermos uma solicitação de autenticação. Esse será o método que criará realmente um modo de exibição da Web

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* Por fim, vamos chamar todos esses métodos que escrevemos acima sempre que o `LoginViewController` for carregado. Fazemos isso adicionando esses métodos ao método `viewDidLoad` fornecido pela Apple

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

Agora você concluiu criando a criação da forma principal como interagiremos com o aplicativo para a entrada. Após a entrada, precisaremos usar os tokens que recebemos. Para isso, vamos criar código auxiliar que chamará APIs REST para que possamos usar esta biblioteca.


## Criar uma classe `GraphAPICaller` para lidar com nossos pedidos para uma API REST

Temos uma configuração carregada sempre que carregarmos o aplicativo. Agora, precisamos fazer algo com ele assim que tivermos um token.

* Criar um arquivo `GraphAPICaller.h`

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

Você vê no código que estamos criando dois métodos: uma para obter as tarefas de uma API e outro para adicionar tarefas à API.

Agora que criamos a interface, vamos adicionar a implementação real:

* Criar um `GraphAPICaller.m file`

```objc
@implementation GraphAPICaller

// 
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

// 
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## Executar o aplicativo de exemplo

Por fim, compile e execute o aplicativo no Xcode. Inscreva-se ou entre no aplicativo e crie tarefas para um usuário conectado. Saia e entre novamente como um usuário diferente e crie tarefas para esse usuário.

Observe que as tarefas são armazenados por usuário na API, pois a API extrai a identidade do usuário do token de acesso que recebe.


## Próximas etapas

Agora você pode ir para tópicos mais avançados sobre o B2C. Você pode experimentar:

[Chamar uma API Web do Node.js em um aplicativo Web do Node.js]()

[Personalizar a experiência do usuário para um aplicativo B2C]()

<!---HONumber=AcomDC_0727_2016-->