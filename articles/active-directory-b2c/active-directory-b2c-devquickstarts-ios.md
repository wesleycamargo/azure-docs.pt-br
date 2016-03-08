<properties pageTitle="Visualização do Azure Active Directory B2C: chamar uma API Web de um aplicativo do iOS | Microsoft Azure" description="Este artigo mostrará como criar um aplicativo de "lista de tarefas pendentes" do iOS que chama uma API Web do Node.js usando tokens de portador OAuth 2.0. O aplicativo do iOS e a API Web usam o Azure Active Directory B2C para gerenciar identidades de usuário e autenticar usuários." services="active-directory-b2c" documentationCenter="ios" authors="brandwe" manager="mbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="objectivec"
	ms.topic="hero-article"
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# Visualização do Azure AD B2C: chamar uma API Web de um aplicativo do iOS

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Usando o Azure Active Directory (Azure AD) B2C, você poderá adicionar recursos poderosos de gerenciamento de identidades de autoatendimento a seus aplicativos do iOS e APIs Web em poucas etapas. Este artigo mostra como criar um aplicativo de "lista de tarefas pendentes" do iOS que chama uma API Web do Node.js usando tokens de portador do OAuth 2.0. Tanto o aplicativo do iOS quanto a API Web usam o Azure AD B2C para gerenciar identidades de usuário e autenticar usuários.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
	Para funcionar plenamente, este Guia de início rápido requer que você já tenha uma API Web protegida pelo Azure AD B2C. Criamos uma para .NET e Node.js que você pode usar. Este passo a passo presume que o exemplo de API Web do Node.js esteja configurado. Confira o [exemplo de API Web do Azure Active Directory para Node.js ](active-directory-b2c-devquickstarts-api-node.md) para obter mais informações.)

> [AZURE.NOTE]
	Este artigo não aborda como implementar entrada, inscrição e gerenciamento de perfil com o Azure AD B2C. Ele se concentra na chamada a APIs Web depois que o usuário é autenticado. Se não estiver, você deve começar com o [tutorial de introdução ao aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para saber mais sobre os conceitos básicos do Azure AD B2C.

## Obter um diretório AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos etc. Se você ainda não tiver um, [crie um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir.

## Criar um aplicativo

Em seguida, você precisa criar um aplicativo em seu diretório B2C. Isso fornece ao AD do Azure as informações de que ele precisa para se comunicar de forma segura com seu aplicativo. O aplicativo e a API Web são representados por uma única **ID de aplicativo** nesse caso, pois abrangem um aplicativo lógico. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). É necessário que você:

- Inclua um **aplicativo Web/API Web** no aplicativo.
- Digite `http://localhost:3000/auth/openid/return` como uma **URL de Resposta**. É a URL padrão deste exemplo de código.
- Crie um **Segredo de aplicativo** para seu aplicativo e copie-o. Você precisará dela mais tarde.
- Copie a **ID de aplicativo** atribuída ao aplicativo. Você também precisará dela mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Criar suas políticas

No Azure AD B2C, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Esse aplicativo contém três experiências de identidade: inscrição, entrada e entrada usando o Facebook. Você precisa criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Ao criar as três políticas, não se esqueça de:

- Escolha o **Nome de exibição** e atributos de inscrição em sua política de inscrição.
- Escolha as declarações de aplicativo **Nome de exibição** e **ID de objeto** em cada política. Você pode escolher outras declarações também.
- Copie o **Nome** de cada política após criá-la. Ele deve ter o prefixo `b2c_1_`. Você precisará esses nomes de política mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as três políticas, você estará pronto para compilar o aplicativo.

Observe que este artigo não aborda como usar as políticas que você acabou de criar. Para saber mais sobre como as políticas funcionam no Azure AD B2C, inicie com o [tutorial de introdução ao aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Baixar o código

O código para este tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS). Para compilar o exemplo à medida que avança, [baixe um projeto de esqueleto como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip): Também é possível clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE] **Você deve baixar o esqueleto para concluir este tutorial.** Devido à complexidade da implementação de um aplicativo totalmente funcional no iOS, o **esqueleto** tem código de experiência do usuário que será executado depois que você concluir o tutorial. Essa é uma medida de economia de tempo para o desenvolvedor. O código de experiência do usuário não é relevante para o tópico sobre como adicionar o B2C a um aplicativo do iOS.

O aplicativo completo também está [disponível como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) ou na ramificação `complete` do mesmo repositório.

Em seguida, carregue `podfile` usando o CocoaPods. Isso criará um novo espaço de trabalho Xcode que será carregado. Se você não tiver o CocoaPods, [acesse o site para instalá-lo](https://cocoapods.org).

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## Configurar o aplicativo de tarefa do iOS

Para que o aplicativo de tarefa do iOS se comunique com o Azure AD B2C, você precisa fornecer alguns parâmetros comuns. Na pasta `Microsoft Tasks`, abra o arquivo `settings.plist` na raiz do projeto e substitua os valores na seção `<dict>`. Esses valores serão usados em todo o aplicativo.

```
<dict>
	<key>authority</key>
	<string>https://login.microsoftonline.com/<your tenant name>.onmicrosoft.com/</string>
	<key>clientId</key>
	<string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	<key>scopes</key>
	<array>
		<string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	</array>
	<key>additionalScopes</key>
	<array>
	</array>
	<key>redirectUri</key>
	<string>urn:ietf:wg:oauth:2.0:oob</string>
	<key>taskWebAPI</key>
	<string>http://localhost/tasks:3000</string>
	<key>emailSignUpPolicyId</key>
	<string><Enter your sign up policy name, e.g.g b2c_1_sign_up></string>
	<key>faceBookSignInPolicyId</key>
	<string><your sign in policy for FB></string>
	<key>emailSignInPolicyId</key>
	<string><Enter your sign in policy name, e.g. b2c_1_sign_in></string>
	<key>fullScreen</key>
	<false/>
	<key>showClaims</key>
	<true/>
</dict>
</plist>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

## Obter tokens de acesso e chamar a API da tarefa

Essa seção discute como você pode concluir uma troca de token do OAuth 2.0 em um aplicativo Web usando bibliotecas e estruturas da Microsoft. Se não estiver familiarizado com os códigos de autorização e tokens de acesso, você poderá saber mais na [referência de protocolo OAuth 2.0](active-directory-b2c-reference-protocols.md).

### Criar arquivos de cabeçalho usando métodos

Você precisará de métodos para obter um token com nossa política selecionada e, em seguida, chamar o servidor de tarefa. Configure-os agora.

Crie um arquivo chamado `samplesWebAPIConnector.h` em `/Microsoft Tasks` no projeto Xcode

Adicione o seguinte código para definir o que precisa fazer:

```
#import <Foundation/Foundation.h>
#import "samplesTaskItem.h"
#import "samplesPolicyData.h"
#import "ADALiOS/ADAuthenticationContext.h"

@interface samplesWebAPIConnector : NSObject<NSURLConnectionDataDelegate>

+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) doPolicy:(samplesPolicyData*)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock;

+(void) signOut;

@end
```

Essas são operações simples para criar, ler, atualizar e excluir (CRUD) na API e um método `doPolicy`. Usando esse método, você pode obter um token com a política desejada.

Observe que dois outros arquivos de cabeçalho precisam ser definidos. Eles manterão o item de tarefa e os dados de política. Crie-os agora:

Crie o arquivo `samplesTaskItem.h` com o seguinte código:

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

Crie também um arquivo `samplesPolicyData.h` para manter os dados de política:

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
### Adicionar uma implementação para os itens de tarefa e política

Agora que os arquivos de cabeçalho estão prontos, você pode escrever o código para armazenar os dados que usará para o exemplo.

Crie o arquivo `samplesPolicyData.m` com o seguinte código:

```
#import <Foundation/Foundation.h>
#import "samplesPolicyData.h"

@implementation samplesPolicyData

+(id) getInstance
{
    static samplesPolicyData *instance = nil;
    static dispatch_once_t onceToken;

    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
        NSDictionary *dictionary = [NSDictionary dictionaryWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"]];
        instance.policyName = [dictionary objectForKey:@"policyName"];
        instance.policyID = [dictionary objectForKey:@"policyID"];


    });

    return instance;
}


@end
```

### Escrever código de instalação para a chamada para ADAL para iOS

O código rápido para armazenar os objetos da interface do usuário agora está concluído. Em seguida, você precisa escrever o código para acessar a ADAL (Biblioteca de Autenticação do Active Directory) para iOS usando os parâmetros que coloca em `settings.plist`. Isso obterá um token de acesso para fornecer ao servidor de tarefa.

Todo o seu trabalho será feito em `samplesWebAPIConnector.m`.

Primeiro, crie a implementação `doPolicy()` que você escreveu no arquivo de cabeçalho `samplesWebAPIConnector.h`:

```
+(void) doPolicy:(samplesPolicyData *)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    [self getClaimsWithPolicyClearingCache:NO policy:policy params:nil parent:parent completionHandler:^(ADProfileInfo* userInfo, NSError* error) {

        if (userInfo == nil)
        {
            completionBlock(nil, error);
        }

        else {

            completionBlock(userInfo, nil);
        }
    }];

}


```

O método é simples. Ele usa como entradas o objeto `samplesPolicyData` criado, o `ViewController` pai e um retorno de chamada. O retorno de chamada é interessante, e vamos examiná-lo de forma mais aprofundada.

- Observe que `completionBlock` tem `ADProfileInfo` como um tipo que será retornado usando um objeto `userInfo`. `ADProfileInfo` é o tipo que contém todas as respostas do servidor, incluindo declarações.
- Observe também `readApplicationSettings`. Ele lê os dados que você forneceu em `settings.plist`.
- Finalmente, há um grande método `getClaimsWithPolicyClearingCache`. Essa é a chamada real para a ADAL para iOS que você precisa escrever. Vamos voltar a esse assunto depois.

Em seguida, escreva o método grande `getClaimsWithPolicyClearingCache`. Ele é grande o suficiente para ter sua própria seção.

### Criar sua chamada a ADAL para iOS

Depois de baixar o esqueleto do GitHub, você pode ver que há várias dessas chamadas para ajudar com o aplicativo de exemplo. Todos eles seguem o padrão de `get(Claims|Token)With<verb>ClearningCache`. Usando as convenções de Objective C, elas se parecem com o idioma inglês. Por exemplo, "Obter um token com parâmetros adicionais que forneço a você e limpar o cache" é `getTokenWithExtraParamsClearingCache()`.

Você escreverá "Obter declarações e um token com a política que forneço a você e não limpar o cache" ou `getClaimsWithPolicyClearingCache`. Você sempre obtém um token da ADAL; portanto, não é necessário especificar "declarações e um token" no método. No entanto, às vezes, você deseja apenas o token sem a sobrecarga de análise das declarações. Portanto, também fornecemos um método sem declarações chamado `getTokenWithPolicyClearingCache` no esqueleto.

Escreva esse código agora:

```
+(void) getClaimsWithPolicyClearingCache  : (BOOL) clearCache
                           policy:(samplesPolicyData *)policy
                           params:(NSDictionary*) params
                           parent:(UIViewController*) parent
                completionHandler:(void (^) (ADProfileInfo*, NSError*))completionBlock;
{
    SamplesApplicationData* data = [SamplesApplicationData getInstance];


    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    if(!data.correlationId ||
       [[data.correlationId stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]] length] == 0)
    {
        authContext.correlationId = [[NSUUID alloc] initWithUUIDString:data.correlationId];
    }

    [ADAuthenticationSettings sharedInstance].enableFullScreen = data.fullScreen;
    [authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

                           if (result.status != AD_SUCCEEDED)
                           {
                               completionBlock(nil, result.error);
                           }                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.profileInfo, nil);
                              }
                          }];
}


```

A primeira parte deve parecer familiar.

- Carregue as configurações que foram fornecidas em `settings.plist` e atribua-as a `data`.
- Configure `ADAuthenticationError`, que usa qualquer erro que vem da ADAL para iOS.
- Crie `authContext`, que define sua chamada para ADAL. Você passa sua autoridade a ele para começar.
- Dê a `authContext` uma referência ao controlador pai para que você possa voltar a ele.
- Converta `redirectURI`, que era uma cadeia de caracteres em `settings.plist`, no tipo NSURL que a ADAL espera.
- Configure o `correlationId`. Esse é apenas um UUID que segue a chamada do cliente para o servidor e vice-versa. Isso é útil para depuração.

Em seguida, você acessa a chamada real para ADAL. É aqui que a chamada é alterada do que você esperaria ver em usos anteriores da ADAL para iOS:

```
[authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

```

Você pode ver que a chamada é bastante simples.

`scopes`: os escopos que você passa ao servidor transmitindo que deseja solicitar do servidor para um usuário que entra. Para a visualização do B2C, você passa `client_id`. No entanto, espera-se que isso seja alterado para ler escopos no futuro. Planejamos atualizar este documento. `additionalScopes`: esses são os escopos adicionais que você pode querer usar no seu aplicativo. Eles devem ser usados no futuro. `clientId`: a ID de aplicativo que você obteve no portal. `redirectURI`: o redirecionamento em que você espera que o token seja postado novamente. `identifier`: uma maneira de identificar um usuário para que você possa ver se há um token utilizável no cache. Isso evita sempre ter que solicitar outro token ao servidor. Isso é realizado em um tipo chamado `ADUserIdentifier`, e você pode especificar o que deseja usar como uma ID. Você deve usar `username`. `promptBehavior`: isso foi preterido. Deve ser `AD_PROMPT_ALWAYS`. `extraQueryParameters`: qualquer extra que você deseje passar ao servidor no formato codificado de URL. `policy`: a política que você está invocando. Essa é a parte mais importante deste passo a passo.

Você pode ver em `completionBlock` que passa `ADAuthenticationResult`. Isso contém as informações de token e perfil (se a chamada foi bem-sucedida).

Usando o código acima, você pode adquirir um token para a política que fornecer. Em seguida, você pode usar esse token para chamar a API.

### Criar suas chamadas de tarefa para o servidor

Agora que você tem um token, precisa fornecê-lo à API para autorização.

Três métodos precisam ser implementados:

```
+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;
```

`getTasksList` fornece uma matriz que representa as tarefas no servidor. `addTask` e `deleteTask` executam as ações subsequentes e retornam `true` ou `false` se forem bem-sucedidos.

Grave `getTaskList` primeiro:

```

+(void) getTaskList:(void (^) (NSArray*, NSError*))completionBlock
             parent:(UIViewController*) parent;
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];

    [self craftRequest:[self.class trimString:data.taskWebApiUrlString]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

        if (error != nil)
        {
            completionBlock(nil, error);
        }
        else
        {

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                if (error == nil && data != nil){

                    NSArray *tasks = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                    //each object is a key value pair
                    NSDictionary *keyValuePairs;
                    NSMutableArray* sampleTaskItems = [[NSMutableArray alloc]init];

                    for(int i =0; i < tasks.count; i++)
                    {
                        keyValuePairs = [tasks objectAtIndex:i];

                        samplesTaskItem *s = [[samplesTaskItem alloc]init];
                        s.itemName = [keyValuePairs valueForKey:@"task"];

                        [sampleTaskItems addObject:s];
                    }

                    completionBlock(sampleTaskItems, nil);
                }
                else
                {
                    completionBlock(nil, error);
                }

            }];
        }
    }];

}

```

Uma discussão sobre o código da tarefa está fora do escopo deste passo a passo. Porém, você pode ter notado algo interessante: um método `craftRequest` que usa a URL da tarefa. Esse método é usado para criar a solicitação para o servidor usando o token de acesso que você recebeu. Escreva-o agora.

Adicione o seguinte código ao arquivo `samplesWebAPIConnector.m`:

```
+(void) craftRequest : (NSString*)webApiUrlString
               parent:(UIViewController*) parent
    completionHandler:(void (^)(NSMutableURLRequest*, NSError* error))completionBlock
{
    [self getClaimsWithPolicyClearingCache:NO parent:parent completionHandler:^(NSString* accessToken, NSError* error){

        if (accessToken == nil)
        {
            completionBlock(nil,error);
        }
        else
        {
            NSURL *webApiURL = [[NSURL alloc]initWithString:webApiUrlString];

            NSMutableURLRequest *request = [[NSMutableURLRequest alloc]initWithURL:webApiURL];

            NSString *authHeader = [NSString stringWithFormat:@"Bearer %@", accessToken];

            [request addValue:authHeader forHTTPHeaderField:@"Authorization"];

            completionBlock(request, nil);
        }
    }];
}
```

Isso usa um URI (uniform resource identifier) da Web, adiciona o token a ele usando o cabeçalho `Bearer` no HTTP e o retorna para você. Você chama a API `getTokenClearingCache`. Isso pode parecer estranho, mas basta usar essa chamada para obter um token do cache e garantir que ele ainda é válido. (As chamadas `getToken` fazem isso para você perguntando à ADAL.) Você usará esse código em cada chamada. Em seguida, crie seus métodos de tarefa adicionais.

Escreva `addTask`:

```
+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"POST"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSString *myString = [[NSString alloc] initWithData:requestBody encoding:NSUTF8StringEncoding];

            NSLog(@"Request was: %@", request);
            NSLog(@"Request body was: %@", myString);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

Isso segue o mesmo padrão, mas também introduz o método final que você precisa implementar: `convertTaskToDictionary`. Isso torna a matriz em um objeto de dicionário. Esse objeto é mais facilmente transformado nos parâmetros de consulta que você precisa passar para o servidor. O código é simples:

```
// Here we have some conversation helpers that allow us to parse passed items into dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }

    return dictionary;
}

```

Em seguida, grave `deleteTask`:

```
+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"DELETE"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSLog(@"%@", request);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

### Adicionar saída ao aplicativo

A última coisa a fazer é implementar a saída para o aplicativo. Isso é simples. No arquivo `sampleWebApiConnector.m`:

```
+(void) signOut
{
    [authContext.tokenCacheStore removeAll:nil];

    NSHTTPCookie *cookie;

    NSHTTPCookieStorage *storage = [NSHTTPCookieStorage sharedHTTPCookieStorage];
    for (cookie in [storage cookies])
    {
        [storage deleteCookie:cookie];
    }
}
```

## Executar o aplicativo de exemplo

Por fim, compile e execute o aplicativo no Xcode. Inscreva-se ou entre no aplicativo e crie tarefas para um usuário conectado. Saia e entre novamente como um usuário diferente e crie tarefas para esse usuário.

Observe que as tarefas são armazenados por usuário na API, pois a API extrai a identidade do usuário do token de acesso que recebe.

Para referência, o exemplo completo [é fornecido como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip). Você também pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## Próximas etapas

Agora você pode ir para tópicos mais avançados sobre o B2C. Você pode experimentar:

[Chamar uma API Web do Node.js em um aplicativo Web do Node.js]()

[Personalizar a experiência do usuário para um aplicativo B2C]()

<!---HONumber=AcomDC_0302_2016-->