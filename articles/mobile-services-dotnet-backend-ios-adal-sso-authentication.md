<properties 
	pageTitle="Autenticar o seu aplicativo com Logon Único da biblioteca de autenticação do Active Directory (iOS) | Mobile Dev Center" 
	description="Saiba como autenticar usuários para logon único com ADAL no seu aplicativo iOS." 
	documentationCenter="ios" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="wesmc,mahender"/>

# Autenticar o seu aplicativo com Logon Único da biblioteca de autenticação do diretório ativo

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../includes/mobile-services-selector-adal-sso.md)]

Neste tutorial, adicione a autenticação ao projeto de início rápido usando uma Biblioteca de autenticação do Active Directory.

Para autenticar usuários, registre seu aplicativo com o Active Directory do Azure (AAD). Isso é feito em duas etapas. Primeiro, deve-se registrar o seu serviço móvel e expor nele as permissões. Em segundo lugar, você deve registrar seu aplicativo iOS e conceder acesso com essas permissões


>[AZURE.NOTE] O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem efetuar autenticação do Active Directory do Azure com logon único para os aplicativos iOS. Se esta for sua primeira experiência com os Serviços Móveis, conclua o tutorial [Introdução aos Serviços Móveis].

Este tutorial apresenta e explica as seguintes etapas básicas:

1. [Registrar o seu serviço móvel com o Active Directory do Azure]
2. [Registrar o seu aplicativo com o Active Directory do Azure]
3. [Configurar o serviço móvel para exigir autenticação]
4. [Adicionar o código de autenticação ao aplicativo do cliente]
5. [Testar o cliente usando autenticação]

Este tutorial exige o seguinte:

* XCode 4.5 e iOS 6.0 (ou versões posteriores)
* Conclusão do tutorial [Introdução aos Serviços Móveis] ou [Introdução aos Dados].
* SDK de Serviços Móveis do Microsoft Azure
* A [Biblioteca de Autenticação do Active Directory para iOS]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-client](../includes/mobile-services-dotnet-adal-register-client.md)]

## <a name="require-authentication"></a>Configurar o serviço móvel para exigir autenticação

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>Adicionar o código de autenticação ao aplicativo do cliente

1. Baixe a [Biblioteca de Autenticação do Active Directory para iOS] e inclua-a em seu projeto. Não se esqueça de adicionar também storyboards da fonte ADAL.

2. No QSTodoListViewController, inclua ADAL com o seguinte:

        #import "ADALiOS/ADAuthenticationContext.h"

2. Em seguida, adicione o seguinte método:

        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            NSString *authority = @"<INSERT-AUTHORITY-HERE>";
            NSString *resourceURI = @"<INSERT-RESOURCE-URI-HERE>";
            NSString *clientID = @"<INSERT-CLIENT-ID-HERE>";
            NSString *redirectURI = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError *error;
            ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
            NSURL *redirectUri = [[NSURL alloc]initWithString:redirectURI];

            [authContext acquireTokenWithResource:resourceURI clientId:clientID redirectUri:redirectUri completionBlock:^(ADAuthenticationResult *result) {
                if (result.tokenCacheStoreItem == nil)
                {
                    return;
                }
                else
                {
                    NSDictionary *payload = @{
                        @"access_token" : result.tokenCacheStoreItem.accessToken
                    };
                    [client loginWithProvider:@"windowsazureactivedirectory" token:payload completion:^(MSUser *user, NSError *error) {
                        [self refresh];
                    }];
                }
            }];
        }


6. No código para o método `loginAndGetData` acima, substitua **INSERT-AUTHORITY-HERE** pelo nome do locatário em que você provisionou o aplicativo, o formato deve ser https://login.windows.net/tenant-name.onmicrosoft.com. Esse valor pode ser copiado da guia Domínio do Active Directory do Azure no [Portal de gerenciamento do Azure].

7. No código para o método `loginAndGetData` acima, substitua **INSERT-RESOURCE-URI-HERE** pela **URI da ID do aplicativo** para o seu serviço móvel. Se o tópico [Como se registrar com o Active Directory do Azure] foi seguido, seu URI da ID do aplicativo deverá ser semelhante a https://todolist.azure-mobile.net/login/aad.

8. o código, para o método `loginAndGetData` acima, substitua **INSERT-CLIENT-ID-HERE** pela ID do cliente copiada do aplicativo do cliente nativo.

9. No código para o método `loginAndGetData` acima, substitua **INSERT-REDIRECT-URI-HERE** pelo ponto de extremidade /login/done de seu serviço móvel. Isso deve ser semelhante a https://todolist.azure-mobile.net/login/done.


3. No QSTodoListViewController, modifique `ViewDidLoad` substituindo `[self refresh]` pelo seguinte:

        [self loginAndGetData];

## <a name="test-client"></a>Testar o cliente usando autenticação

1. No menu Produto, clique em Executar para iniciar o aplicativo
2. Você receberá uma solicitação para fazer logon no seu Active Directory do Azure.  
3. O aplicativo autentica e retorna os itens todo.

   ![](./media/mobile-services-dotnet-backend-ios-adal-sso-authentication/mobile-services-app-run.png)

<!-- Anchors. -->
[Registrar o seu serviço móvel com o Active Directory do Azure]: #register-mobile-service-aad
[Registrar o seu aplicativo com o Active Directory do Azure]: #register-app-aad
[Configurar o serviço móvel para exigir autenticação]: #require-authentication
[Adicionar o código de autenticação ao aplicativo do cliente]: #add-authentication-code
[Testar o cliente usando autenticação]: #test-client

<!-- URLs. -->
[Introdução aos dados]: /en-us/documentation/articles/mobile-services-ios-get-started-data/
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[Como se registrar com o Active Directory do Azure]: /en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Biblioteca de Autenticação do Active Directory para iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
\n<!--HONumber=42-->
