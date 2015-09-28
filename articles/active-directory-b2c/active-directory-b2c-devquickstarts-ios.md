<properties pageTitle="Visualização do B2C do AD do Azure: chamando uma API Web de um aplicativo do iOS | Microsoft Azure" description="Este artigo mostrará como criar um aplicativo de "Lista de Tarefas Pendentes" do iOS que chama uma API Web do node.js usando tokens de portador OAuth 2.0. O aplicativo do iOS e a api Web usam o B2C do AD do Azure para gerenciar identidades de usuário e autenticar usuários." services="active-directory-b2c" documentationCenter="ios" authors="brandwe" manager="mbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="objectivec"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="brandwe"/>

# Visualização do B2C do AD do Azure: chamando uma API Web de um aplicativo do iOS

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Com o AD B2C do Azure, você pode adicionar recursos poderosos de gerenciamento de identidades de autoatendimento para seus aplicativos e apis do iOS em poucas etapas. Este artigo mostra como criar um aplicativo de "Lista de Tarefas Pendentes" do iOS que chama uma API Web do node.js usando tokens de portador OAuth 2.0. Tanto o aplicativo do iOS quanto a api Web usam o AD B2C do Azure para gerenciar identidades de usuário e autenticar usuários.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
> [AZURE.NOTE]Este guia de início rápido tem o pré-requisito de que você deve ter uma API Web protegida pelo AD do Azure com B2C para funcionar totalmente. Criamos uma para .Net e node.js que você pode usar. Este passo a passo pressupõe que o exemplo de API Web do node.js está configurado. Consulte o [Exemplo da API Web do Active Directory do Azure para Node.js](active-directory-b2c-devquickstarts-api-node.md`).

> [AZURE.NOTE]Este artigo não aborda como implementar conexão, registro e gerenciamento de perfil com o Azure AD B2C. Ele se concentra na chamada a APIs Web depois que o usuário já está autenticado. Se não estiver, você deve começar com o [tutorial de introdução ao aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para saber mais sobre os conceitos básicos do Azure AD B2C.

## 1\. Obter um diretório AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e assim por diante. Se você não tiver um, acesse [criar um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir.

## 2\. Criar um aplicativo

Agora você precisa criar um aplicativo no diretório B2C, que dá ao AD do Azure algumas informações que ele precisa para se comunicar de forma segura com seu aplicativo. O aplicativo e a API Web serão representados por uma única **ID de aplicativo** nesse caso, uma vez que incluem um aplicativo lógico. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). Certifique-se de

- Incluir um **aplicativo Web/api Web** no aplicativo
- Digitar `http://localhost:3000/auth/openid/return` como uma **URL de Resposta** - é a URL padrão para este exemplo de código.
- Criar um **Segredo do Aplicativo** para seu aplicativo e copiá-lo. Você precisará dele em breve.
- Copiar a **ID do Aplicativo** atribuída ao aplicativo. Você também precisará dela em breve.

## 3\. Criar suas políticas

No B2C do AD do Azure, cada experiência do usuário é definida por uma [**política**](active-directory-b2c-reference-policies.md). Este aplicativo contém três experiências de identidade: inscrição, entrada e entrada com o Facebook. Você precisará criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Ao criar suas três políticas, não se esqueça de:

- Escolher o **Nome de Exibição** e alguns outros atributos de inscrição em sua política de inscrição.
- Escolher as declarações de aplicativo **Nome de Exibição** e **ID do Objeto** em cada política. Você pode escolher outras declarações também.
- Copie o **Nome** de cada política após criá-lo. Ele deve ter o prefixo `b2c_1_`. Em breve, você precisará esses nomes de política. 

Depois de criar suas três políticas com êxito, você está pronto para criar o aplicativo.

Observe que este artigo não aborda como usar as políticas que você acabou de criar. Para saber mais sobre como as políticas funcionam AD B2C do Azure, você deve começar com o [Tutorial de introdução do aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## 4\. Baixar o código

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS). Para compilar o exemplo conforme você avança, você pode [baixar um projeto de esqueleto como .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip) ou clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE]**É necessário baixar o esqueleto para concluir este tutorial.** Devido à complexidade da implementação de um aplicativo totalmente funcional no iOS, o **esqueleto** tem código de experiência do usuário que será executado quando você concluir o tutorial abaixo. Esta é uma medida de economia de tempo para o desenvolvedor. O código de experiência do usuário não é relevante para o tópico da adição do B2C a um aplicativo do iOS.

O aplicativo completo também está [disponível como. zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) ou na ramificação `complete` do mesmo repositório.


Agora, carregue o podfile usando o cocoapods. Isso criará um novo espaço de trabalho XCode que será carregado. Se você não tiver Cocoapods, visite [o site para instalar o cocoapods](https://cocoapods.org).

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## 5\. Configurar o aplicativo de tarefa do iOS

Para que o aplicativo de tarefa do iOS se comunique com o B2C do AD do Azure, há alguns parâmetros comuns que você precisará fornecer. Na pasta `Microsoft Tasks`, abra o arquivo `settings.plist` na raiz do projeto e substitua os valores na seção `<dict>`: Esses valores serão usados em todo o aplicativo.

```
<dict>
	<key>authority</key>
	<string>https://login.microsoftonline.com/<your tenant name>.onmicrosoft.com/</string>
	<key>clientId</key>
	<string><Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	<key>scopes</key>
	<array>
		<string><Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	</array>
	<key>additionalScopes</key>
	<array>
		<string></string>
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

## 6\. Obter tokens de acesso e chamar a API da tarefa

Esta seção mostrará como concluir uma troca de token do OAuth 2.0 em um aplicativo Web usando bibliotecas e estruturas da Microsoft. Se você estiver familiarizado com **códigos de autorização** e **tokens de acesso**, pode ser uma boa idéia percorrer a [referência de protocolo do OAuth 2.0](active-directory-b2c-reference-protocols.md).

#### Crie arquivos de cabeçalho com nossos métodos que serão usados.

Precisaremos de métodos para obter um token com nossa política selecionada e, em seguida, chamar nosso servidor de tarefa. Vamos configurar isso agora.

Crie um arquivo chamado `samplesWebAPIConnector.h` em /Microsoft Tasks em seu projeto XCode

Adicione o seguinte código para definir o que precisamos fazer:

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

Você verá que essas são as operações CRUD simples em nossa API, bem como um método `doPolicy` que permite que você obtenha um token com a política desejada.

Você também verá que temos dois outros arquivos de cabeçalho que precisamos definir, os quais conterão o Item de Tarefa e nossos Dados de Política. Vamos criar isso agora:

Crie um arquivo chamado `samplesTaskItem.h` com o seguinte código:

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

Vamos também criar um arquivo `samplesPolicyData.h` para manter nossos dados de política:

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
#### Adicionar uma implementação para nossos itens de Tarefa e Política

Agora que temos nossos arquivos de cabeçalho, precisamos escrever um código para armazenar os dados que usaremos para nosso exemplo.

Crie um arquivo chamado `samplesPolicyData.m` com o seguinte código:

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

#### Escrever código de instalação para nossa chamada em ADAL para iOS

O código rápido para armazenar os objetos da interface do usuário foi concluído. Agora precisamos escrever nosso código para acessar a ADAL para iOS com os parâmetros que inserimos em nosso arquivo `settings.plist` a fim de obter um token de acesso para fornecer ao servidor de Tarefa. Isso pode ser detalhado, então, vamos manter o foco.

Todo o trabalho será feito em `samplesWebAPIConnector.m`.

Primeiro, vamos criar nossa implementação `doPolicy()` que escrevemos em nosso arquivo de cabeçalho `samplesWebAPIConnector.h`:

```
+(void) doPolicy:(samplesPolicyData *)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }
    
    NSDictionary* params = [self convertPolicyToDictionary:policy];
    
    [self getClaimsWithPolicyClearingCache:NO policy:policy params:params parent:parent completionHandler:^(ADProfileInfo* userInfo, NSError* error) {
        
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

Você verá que o método é muito simples. Ele utiliza como entrada o objeto `samplesPolicyData` que criamos há pouco, o pai ViewController e um retorno de chamada. O retorno de chamada é interessante e vamos examiná-lo.

1. Você verá que o `completionBlock` tem ADProfileInfo como um tipo que será é retornado com um objeto `userInfo`. ADProfileInfo é o tipo que contém toda a resposta do servidor, em particular, declarações. 

2. Você verá que `readApplicationSettings`. Isso lê os dados que fornecemos em `settings.plist`
3. Você verá que temos um método `convertPolicyToDictionary:policy` que usa nossa política e a formata como uma URL para enviar ao servidor. Vamos escrever esse método auxiliar em seguida.
4. Por fim, temos um método `getClaimsWithPolicyClearingCache` bastante grande. Essa é a chamada real para a ADAL para iOS que precisamos escrever. Faremos isso mais tarde.


Em seguida, escreveremos o método `convertPolicyToDictionary` abaixo do código que escrevemos:

```
// Here we have some converstion helpers that allow us to parse passed items in to dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];
    
    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }
    
    return dictionary;
}

+(NSDictionary*) convertPolicyToDictionary:(samplesPolicyData*)policy
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    
    if (policy.policyID){
        [dictionary setValue:policy.policyID forKey:@"p"];
    }
    
    return dictionary;
}

```
Esse código simples apenas acrescenta um p à nossa política, de modo que a aparência da consulta seja ?p=<policy>.

Agora vamos escrever nosso método grande `getClaimsWithPolicyClearingCache`. Ele é grande o suficiente para ter sua própria seção

#### Criar nossa chamada na ADAL para iOS

Se baixou o esqueleto do GitHub, você verá que já temos várias delas vigentes que ajudam com o aplicativo de exemplo. Todas elas seguem o padrão de `get(Claims|Token)With<verb>ClearningCache`. Usando as convenções de Objetive C, isso se parece muito com o idioma inglês. Por exemplo, "obter um Token com parâmetros extra que vou lhe fornecer e limpar o cache". Isso é `getTokenWithExtraParamsClearingCache()`. Muito simples.

Vamos escrever "obter declarações e um token com a política que vou fornecer a você e não limpar o cache" ou `getClaimsWithPolicyClearingCache`. Sempre obtemos um token da ADAL. Portanto, não é necessário especificar "Declarações e token" no método. No entanto, às vezes, você deseja apenas o token sem a sobrecarga de analisar as declarações, assim, fornecemos um método sem declarações chamado `getTokenWithPolicyClearingCache` no esqueleto.

Agora, vamos escrever esse código:

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

A primeira parte deve parecer familiar. Carregamos as configurações que foram fornecidas em `Settings.plist` e as atribuímos a `data`. Em seguida, configuramos um `ADAuthenticationError` que incluirá qualquer erro que vem da ADAL para iOS. Também criamos um `authContext` que está configurando nossa chamada para ADAL. Passamos a ele nossa *autoridade* para começar. Também fornecemos ao `authContext` uma referência ao controlador pai para que possamos voltar a ele. Também convertemos nosso `redirectURI`, que era uma cadeia de caracteres em nosso `settings.plist`, no tipo NSURL que a ADAL espera. Por fim, configuramos um `correlationId`, que é apenas um UUID que pode seguir a chamada do cliente para o servidor e vice-versa. Isso é útil para depuração.

Agora chegamos à chamada para a ADAL, e é aqui que a chamada é alterada do que você esperaria ver em usos anteriores da ADAL para iOS:

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

Aqui você vê que a chamada é bastante simples.

**escopos** - os escopos que passamos para o servidor que desejamos solicitar do servidor para o usuário que está fazendo logon. Para a Visualização do B2C, passamos o client\_id. No entanto, isso será alterado para ler escopos no futuro. Em seguida, esse documento será atualizado. **addtionalScopes** - os escopos adicionais que você talvez queira usar para seu aplicativo. Isso será usado no futuro **clientId** - ID do aplicativo que você obteve no portal **redirectURI** - o redirecionamento em que podemos esperar que o token seja postado. **identificador** - essa é uma maneira de identificar o usuário para que possamos determinar se há um token utilizável no cache em vez de sempre pedir outro token ao servidor. Você vê que isso é realizado em um tipo chamado `ADUserIdentifier`, e podemos especificar o que queremos usar como uma ID. Você deve usar o nome de usuário. **promptBehavior** - isso foi preterido e deve ser AD\_PROMPT\_ALWAYS **extraQueryParameters** - qualquer item extra que você deseje passar para o servidor no formato codificado de URL. **política** - a política que você está invocando. A parte posterior importante deste passo a passo.

Você pode ver no completionBlock, passamos o `ADAuthenticationResult` que tem nosso token e informações de perfil (se a chamada foi bem-sucedida)

Usando o código acima, você pode adquirir um token para a política que fornecer. Usaremos esse token para chamar a API.

#### Criar nossas Chamadas de Tarefa para o servidor

Agora que temos um token, precisamos fornecê-lo à nossa API para autorização.

Se você se lembra, tínhamos três métodos que precisamos implementar:

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

Nosso `getTasksList` fornece uma matriz que representa as tarefas em nosso servidor. Nossos `addTask` e `deleteTask` executam a ação subsequente e retornam TRUE ou FALSE se ela é bem-sucedida.

Vamos escrever nosso `getTaskList` primeiro:

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

Discutir o código da tarefa está fora do escopo deste passo a passo, mas há algo interessante que talvez você tenha percebido: um método `craftRequest` que usa nossa URL de tarefa. Esse método é o que usamos para criar a solicitação, com o token de acesso que recebemos do servidor. Vamos escrever isso agora.

Vamos adicionar o código a seguir ao arquivo 'samplesWebAPIConnector.m':

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

Como você pode ver, isso requer um URI da Web, adiciona o token a ele com o cabeçalho `Bearer` no HTTP e o retorna para nós. Chamamos a API `getTokenClearingCache`, o que pode parecer estranho no início, mas podemos simplesmente usar essa chamada para obter um token do cache e verificar se ele ainda é válido (as chamadas getToken* fazem isso para nós perguntando à ADAL). Usamos esse código em cada chamada. Agora, voltemos à criação de nossos métodos de tarefa adicionais.

Vamos escrever nosso `addTask`:

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

Isso segue o mesmo padrão, mas introduz outro método (final) que precisamos implementar: `convertTaskToDictionary`, que usa nossa matriz e a torna um objeto de dicionário, que é mais facilmente alterado para os parâmetros de consulta que precisamos passar ao servidor. Esse código é muito simples:

```
// Here we have some converstion helpers that allow us to parse passed items in to dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];
    
    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }
    
    return dictionary;
}

```

Por fim, vamos escrever nosso `deleteTask`:

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

### E a saída de nosso aplicativo.

A última coisa que precisamos fazer é implementar a saída de nosso aplicativo. Isso é bastante simples. Novamente, no arquivo `sampleWebApiConnector.m`:

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

## 9\. Executar o aplicativo de exemplo

Por fim, compile e execute o aplicativo no xCode. Inscreva-se ou entre no aplicativo e crie tarefas para o usuário conectado. Saia e entre novamente como um usuário diferente, criando tarefas para esse usuário.

Observe como as tarefas são armazenados por usuário na API, pois a API extrai a identidade do usuário do token de acesso que recebe.

Para referência, o exemplo concluído [é fornecido como. zip aqui](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## Próximas etapas

Agora você pode ir para tópicos mais avançados sobre o B2C. Você pode desejar experimentar:

[Chamando uma API Web do node.js em um aplicativo Web do node.js >>]()

[Personalizando seu aplicativo de experiência do usuário do B2C >>]()

<!---HONumber=Sept15_HO3-->