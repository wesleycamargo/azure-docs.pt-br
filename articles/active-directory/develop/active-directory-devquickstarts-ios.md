---
title: "Introdução a iOS no Azure AD | Microsoft Docs"
description: Como criar um aplicativo para iOS que se integre ao Azure AD para fazer login e que chame as APIs protegidas do Azure AD usando o OAuth.
services: active-directory
documentationcenter: ios
author: xerners
manager: mbaldwin
editor: 
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: xerners
translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: c74e63621d422f8fa13bc1dd2730ec2c3325a46a
ms.lasthandoff: 01/18/2017


---
# <a name="integrate-azure-ad-into-an-ios-app"></a>Integrar o Azure AD em um aplicativo iOS
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Para clientes iOS que precisam acessar recursos protegidos, o Azure AD fornece a biblioteca de autenticação do Active Directory, ou ADAL.  A única finalidade da ADAL é tornar mais fácil para seu aplicativo obter tokens de acesso.  Para demonstrar como é fácil, vamos criar aqui um aplicativo de lista de tarefas para Objective C que:

* Obtém tokens de acesso para chamar a Graph API do Azure AD usando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Pesquisa um diretório para usuários com um determinado alias.

Para compilar o aplicativo em funcionamento completo, você precisará:

1. Registrar seu aplicativo no Azure AD.
2. Instalar e configurar a ADAL.
3. Usar a ADAL para obter tokens do AD do Azure.

Para começar, [baixe o esqueleto do aplicativo](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) ou [baixe o exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  Você também precisará de um locatário do AD do Azure no qual você possa criar usuários e registrar um aplicativo.  Se você ainda não tiver um locatário [saiba como obter um](active-directory-howto-tenant.md).

> [!TIP]
> Experimente a demonstração do nosso novo [portal do desenvolvedor](https://identity.microsoft.com/Docs/iOS) que ajudará você a executar o Azure Active Directory em apenas alguns minutos!  O portal do desenvolvedor orientará você pelo processo de registro de um aplicativo e integração do Azure AD em seu código.  Quando terminar, você terá um aplicativo simples que pode autenticar os usuários em seu locatário e um back-end que pode aceitar tokens e executar a validação. 
> 
> 

## <a name="1-determine-what-your-redirect-uri-will-be-for-ios"></a>1. Determine qual será seu URI de redirecionamento para iOS
Para iniciar com segurança os aplicativos em determinados cenários SSO, exigimos que você crie um **URI de redirecionamento** em um formato específico. Um URI de redirecionamento é usado para garantir que os tokens retornem para o aplicativo correto que os solicitam.

O formato do iOS para um URI de redirecionamento é:

```
<app-scheme>://<bundle-id>
```

* **aap-scheme** - isso é registrado em seu projeto XCode. É como os outros aplicativos podem chamar você. Você pode encontrar isso em Info.plist -> tipos de URL -> identificador de URL. Você deve criar um se você ainda não tiver um ou mais configurados.
* **bundle-id** - esse é o identificador de pacote localizado em "identidade" nas configurações do seu projeto no XCode.

Um exemplo para este código de Início Rápido seria: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>2. Registrar o aplicativo DirectorySearcher
Para habilitar seu aplicativo para obter tokens, primeiro será necessário registrá-lo no seu locatário do AD do Azure e conceder permissão para acessar a Graph API do AD do Azure:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique na sua conta e, na lista **Diretório**, escolha o locatário do Active Directory em que você deseja registrar seu aplicativo.
3. Clique em **Mais Serviços** no painel de navegação à esquerda e escolha **Azure Active Directory**.
4. Clique em **Registros do Aplicativo** e escolha **Adicionar**.
5. Siga os prompts e crie um novo **Aplicativo cliente nativo**.
  * O **Nome** do aplicativo descreverá seu aplicativo para os usuários finais
  * O **URI de redirecionamento** é uma combinação de esquema e de cadeia de caracteres que o AD do Azure usará para retornar respostas de tokens.  Insira um valor específico para seu aplicativo com base nas informações acima.
6. Depois de concluir o registro, o AAD atribuirá a seu aplicativo uma ID do Aplicativo única.  Você precisará desse valor nas próximas seções, portanto, copie-o da guia do aplicativo.
7. Na página **Configurações**, escolha **Permissões Necessárias** e escolha **Adicionar**. Escolha o **Microsoft Graph** como a API e adicione a permissão **Ler Dados do Diretório** em **Permissões Delegadas**.  Isso permitirá que seu aplicativo consulte a Graph API para usuários.

## <a name="3-install--configure-adal"></a>3. Instalar e Configurar o ADAL
Agora que você tem um aplicativo no AD do Azure, você pode instalar a ADAL e escrever seu código relacionado à identidade.  Para que a ADAL possa se comunicar com o Azure AD, é necessário fornecer a ela algumas informações sobre o registro do aplicativo.

* Inicie adicionando a ADAL ao projeto DirectorySearcher usando o Cocapods.

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

* No projeto do Guia de início rápido, abra o arquivo plist `settings.plist`.  Substitua os valores dos elementos na seção para refletir os valores inseridos por você no Portal do Azure.  Seu código fará referência a esses valores sempre que ele usar a ADAL.
  * O `tenant` é o domínio do seu locatário do AD do Azure, por exemplo, contoso.onmicrosoft.com.
  * O `clientId` é a clientId do seu aplicativo que você copiou do portal.
  * O `redirectUri` é a URL de redirecionamento registrada no portal.

## <a name="4----use-adal-to-get-tokens-from-aad"></a>4.    Usar o ADAL para obter tokens do AAD
O princípio básico da ADAL é que sempre que seu aplicativo precisar de um token de acesso, ele simplesmente chama um completionBlock `+(void) getToken : `, e a ADAL faz o resto.  

* No projeto `QuickStart`, abra `GraphAPICaller.m` e localize o comentário `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` perto da parte de cima.  É aqui que você passa à ADAL as coordenadas necessárias, por um CompletionBlock, para se comunicar com o Azure AD e informar a ele como armazenar tokens em cache.

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

* Agora, precisamos usar esse token para pesquisar usuários no gráfico. Encontre o comentário `// TODO: implement SearchUsersList`. Esse método faz uma solicitação GET para que a Graph API do Azure AD procure por usuários cujo UPN comece com o termo de pesquisa fornecido.  Mas para consultar a Graph API, você precisa incluir um access_token no cabeçalho `Authorization` da solicitação - é aí que entra a ADAL.

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
* Quando o aplicativo solicita um token chamando `getToken(...)`, a ADAL tentará retornar um token sem pedir as credenciais ao usuário.  Se a ADAL determina que o usuário precisa entrar para obter um token, exibirá uma caixa de diálogo de logon, coletar as credenciais do usuário e retornar um token após uma autenticação bem-sucedida.  Se a ADAL não puder retornar um token por qualquer motivo, o status exibirá `AdalException`.
* Observe que o objeto `AuthenticationResult` contém um objeto `tokenCacheStoreItem` que pode ser usado para coletar informações que seu aplicativo pode precisar.  No Guia de início rápido, `tokenCacheStoreItem` é usado para determinar se a autenticação já ocorreu.

## <a name="5-build-and-run-the-application"></a>5. Compile e execute o aplicativo
Parabéns! Agora você tem um aplicativo iOS que tem a capacidade de autenticar usuários, chamar APIs da Web com segurança usando OAuth 2.0 e obter informações básicas sobre o usuário.  Se você ainda não fez isso, agora é o momento de preencher seu locatário com alguns usuários.  Execute o aplicativo do Guia de início rápido e entre com um desses usuários.  Procure por outros usuários com base em seus UPNs.  Feche o aplicativo e execute-o novamente.  Observe como a sessão do usuário permanece intacta.

A ADAL facilita a incorporar todos esses recursos comuns de identidade em seu aplicativo.  Ele se encarrega de todo o trabalho difícil para você - gerenciamento de cache, suporte a protocolo OAuth, apresentação de uma IU de logon ao usuário, atualização de tokens expirados e mais.  Tudo o que você realmente precisa saber é uma única chamada à API, `getToken`.

Para referência, o exemplo concluído (sem seus valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  

## <a name="additional-scenarios"></a>Cenários adicionais
Agora você pode passar para cenários de adicionais.  Você pode desejar experimentar:

* [Proteger uma API Web Node.js com o Azure AD](active-directory-devquickstarts-webapi-nodejs.md)
* Saiba [como habilitar o SSO entre aplicativos no iOS usando a ADAL](active-directory-sso-ios.md)  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]


