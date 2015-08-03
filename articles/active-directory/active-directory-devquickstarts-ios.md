<properties
	pageTitle="Introdução a iOS no Azure AD | Microsoft Azure"
	description="Como criar um aplicativo para iOS que se integre ao Azure AD para fazer login e que chame as APIs protegidas do Azure AD usando o OAuth."
	services="active-directory"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="07/17/2015"
	ms.author="brandwe"/>

# Integrar o Azure AD em um aplicativo iOS

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Para clientes iOS que precisam acessar recursos protegidos, o Azure AD fornece a biblioteca de autenticação do Active Directory, ou ADAL. A única finalidade da ADAL é tornar mais fácil para seu aplicativo obter tokens de acesso. Para demonstrar como é fácil, vamos criar aqui um aplicativo de lista de tarefas para Objective C que:

-	Obtém tokens de acesso para chamar a Graph API do Azure AD usando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-	Pesquisa um diretório para usuários com um determinado alias.

Para compilar o aplicativo em funcionamento completo, você precisará:

2. Registrar seu aplicativo no Azure AD.
3. Instalar e configurar a ADAL.
5. Usar a ADAL para obter tokens do AD do Azure.

Para começar, [baixe o esqueleto do aplicativo](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) ou [baixe o exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). Você também precisará de um locatário do AD do Azure no qual você possa criar usuários e registrar um aplicativo. Se você ainda não tiver um locatário [saiba como obter um](active-directory-howto-tenant.md).

## *1. Determine qual será seu URI de redirecionamento para iOS*

Para iniciar com segurança os aplicativos em determinados cenários SSO, exigimos que você crie um **URI de redirecionamento** em um formato específico. Um URI de redirecionamento é usado para garantir que os tokens retornem para o aplicativo correto que os solicitam.

O formato do iOS para um URI de redirecionamento é:

```
<app-scheme>://<bundle-id>
```

- 	**aap-scheme** - isso é registrado em seu projeto XCode. É como os outros aplicativos podem chamar você. Você pode encontrar isso em Info.plist -> tipos de URL -> identificador de URL. Você deve criar um se você ainda não tiver um ou mais configurados.
- 	**bundle-id** - esse é o identificador de pacote localizado em "identidade" nas configurações do seu projeto no XCode.
	
Um exemplo para este código do Guia de início rápido seria: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## *2. Registrar o aplicativo DirectorySearcher*
Para habilitar seu aplicativo para obter tokens, primeiro será necessário registrá-lo no seu locatário do AD do Azure e conceder permissão para acessar a Graph API do AD do Azure:

-	Entre no Portal de Gerenciamento do Azure
-	Clique em **Active Directory** no painel de navegação à esquerda
-	Selecione um locatário no qual registrar o aplicativo.
-	Clique na guia **Aplicativos** e clique em **Adicionar** na lista de botões.
-	Siga os prompts e crie um novo **Aplicativo cliente nativo**.  

    -	O **Nome** do aplicativo descreverá seu aplicativo para os usuários finais
    -	O **URI de redirecionamento** é uma combinação de esquema e de cadeia de caracteres que o AD do Azure usará para retornar respostas de tokens. Insira um valor específico para seu aplicativo com base nas informações acima.
-	Depois de concluir o registro, o AAD atribuirá a seu aplicativo um identificador de cliente único. Você precisará desse valor nas próximas seções, então copie-o da guia **Configurar**.
- Também na guia **Configurar**, clique na seção “Permissões para outros aplicativos”. Para o aplicativo "Active Directory do Azure", adicione a permissão **Acessar o diretório de sua organização** em **Permissões delegadas**. Isso permitirá que seu aplicativo consulte a Graph API para usuários.

## *3. Instalar e configurar a ADAL*
Agora que você tem um aplicativo no AD do Azure, você pode instalar a ADAL e escrever seu código relacionado à identidade. Para que a ADAL seja capaz de se comunicar com o Azure AD, é necessário fornecer algumas informações sobre o registro de seu aplicativo. Comece adicionando a ADAL ao projeto DirectorySearcher usando o Cocapods.

```
$ vi Podfile
```
Adicione o seguinte a esse podfile:

```
source 'https://github.com/CocoaPods/Specs.git'
link_with ['QuickStart']
xcodeproj 'QuickStart'

pod 'ADALiOS'
```

Agora, carregue o podfile usando o cocoapods. Isso criará um novo espaço de trabalho XCode que será carregado.

```
$ pod install
...
$ open QuickStart.xcworkspace
```

-	No projeto do Guia de início rápido, abra o arquivo plist `settings.plist`. Substitua os valores dos elementos na seção para refletir os valores inseridos por você no Portal do Azure. Seu código fará referência a esses valores sempre que ele usar a ADAL.
    -	O `tenant` é o domínio do seu locatário do AD do Azure, por exemplo, contoso.onmicrosoft.com.
    -	O `clientId` é a clientId do seu aplicativo que você copiou do portal.
    -	O `redirectUri` é a URL de redirecionamento registrada no portal.

## *4. Usar a ADAL para obter tokens do AAD*
O princípio básico da ADAL é que sempre que seu aplicativo precisar de um token de acesso, ele simplesmente chama um completionBlock `+(void) getToken : `, e a ADAL faz o resto.

-	No projeto `QuickStart`, abra `GraphAPICaller.m` e localize o comentário `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` perto da parte de cima. É aqui que você passa à ADAL as coordenadas necessárias, por um CompletionBlock, para se comunicar com o Azure AD e informar a ele como armazenar tokens em cache.

```ObjC
+(void) getToken : (BOOL) clearCache
           parent:(UIViewController*) parent
completionHandler:(void (^) (NSString*, NSError*))completionBlock;
{
    AppData* data = [AppData getInstance];
    if(data.userItem){
        completionBlock(data.userItem.accessToken, nil);
        return;
    }
    
    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];
    
    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:data.resourceId
                                 clientId:data.clientId
                              redirectUri:redirectUri
                           promptBehavior:AD_PROMPT_AUTO
                                   userId:data.userItem.userInformation.userId
                     extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                          completionBlock:^(ADAuthenticationResult *result) {
                              
                              if (result.status != AD_SUCCEEDED)
                              {
                                  completionBlock(nil, result.error);
                              }
                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                              }
                          }];
}

```

- Agora, precisamos usar esse token para pesquisar usuários no gráfico. Encontre o comentário `// TODO: implement SearchUsersList`. Esse método faz uma solicitação GET para que a Graph API do Azure AD procure por usuários cujo UPN comece com o termo de pesquisa fornecido. Mas para consultar a Graph API, você precisa incluir um access_token no cabeçalho `Authorization` da solicitação - é aí que entra a ADAL.

```ObjC
+(void) searchUserList:(NSString*)searchString
                parent:(UIViewController*) parent
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }
    
    AppData* data = [AppData getInstance];
    
    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];

    
    [self craftRequest:[self.class trimString:graphURL]
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
                     
                     NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];
                     
                     // We can grab the top most JSON node to get our graph data.
                     NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
                     
                     // Don't be thrown off by the key name being "value". It really is the name of the
                     // first node. :-)
                     
                     //each object is a key value pair
                     NSDictionary *keyValuePairs;
                     NSMutableArray* Users = [[NSMutableArray alloc]init];
                     
                     for(int i =0; i < graphDataArray.count; i++)
                     {
                         keyValuePairs = [graphDataArray objectAtIndex:i];
                         
                         User *s = [[User alloc]init];
                         s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                         s.name =[keyValuePairs valueForKey:@"givenName"];
                         
                         [Users addObject:s];
                     }
                     
                     completionBlock(Users, nil);
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
- Quando o aplicativo solicita um token chamando `getToken(...)`, a ADAL tentará retornar um token sem pedir as credenciais ao usuário. Se a ADAL determina que o usuário precisa entrar para obter um token, exibirá uma caixa de diálogo de logon, coletar as credenciais do usuário e retornar um token após uma autenticação bem-sucedida. Se a ADAL não puder retornar um token por qualquer motivo, o status exibirá `AdalException`.
- Observe que o objeto `AuthenticationResult` contém um objeto `tokenCacheStoreItem` que pode ser usado para coletar informações que seu aplicativo pode precisar. No Guia de início rápido, `tokenCacheStoreItem` é usado para determinar se a autenticação já ocorreu. 


## Etapa 5: Compile e execute o aplicativo



Parabéns! Agora você tem um aplicativo iOS que tem a capacidade de autenticar usuários, chamar APIs da Web com segurança usando OAuth 2.0 e obter informações básicas sobre o usuário. Se você ainda não fez isso, agora é o momento de preencher seu locatário com alguns usuários. Execute o aplicativo do Guia de início rápido e entre com um desses usuários. Procure por outros usuários com base em seus UPNs. Feche o aplicativo e execute-o novamente. Observe como a sessão do usuário permanece intacta.

A ADAL facilita a incorporar todos esses recursos comuns de identidade em seu aplicativo. Ele se encarrega de todo o trabalho difícil para você - gerenciamento de cache, suporte a protocolo OAuth, apresentação de uma IU de logon ao usuário, atualização de tokens expirados e mais. Tudo o que você realmente precisa saber é uma única chamada à API, `getToken`.

Para referência, o exemplo concluído (sem seus valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). Agora você pode passar para cenários de adicionais. Você pode desejar experimentar:

[Proteger uma API da Web Node.JS com o Azure AD >>](../active-directory-devquickstarts-webapi-nodejst.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=July15_HO4-->