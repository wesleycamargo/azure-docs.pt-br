<properties linkid="develop-mobile-tutorials-sso-with-adal-ios" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your iOS application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc,mahender" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc,mahender"></tags>

# Autenticar o seu aplicativo com Logon Único da biblioteca de autenticação do diretório ativo

<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="C# da Windows Store" >C# da Windows Store</a>
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" class="current">iOS</a>
<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS">Xamarin.iOS</a>
</div>

Neste tutorial, adicione a autenticação ao projeto de início rápido usando uma Biblioteca de autenticação do diretório ativo.

Para poder autenticar os usuários, deve-se registrar o seu aplicativo com o Azure Active Directory (AAD). Isso é feito em duas etapas. Primeiro, deve-se registrar o seu serviço móvel e expor nele as permissões. Em segundo lugar, você deve registrar seu aplicativo iOS e conceder acesso com essas permissões

> [WACOM.NOTE] O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem efetuar autenticação do Active Directory do Azure em logon único para os aplicativos iOS. Se esta for sua primeira experiência com os Serviços Móveis, conclua o tutorial [Introdução aos serviços móveis][Introdução aos serviços móveis].

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Registrar o seu serviço móvel com o Active Directory do Azure][Registrar o seu serviço móvel com o Active Directory do Azure]
2.  [Registrar o seu aplicativo com o Azure Active Directory][Registrar o seu aplicativo com o Azure Active Directory]
3.  [Configurar o serviço móvel para requerer autenticação][Configurar o serviço móvel para requerer autenticação]
4.  [Adicionar o código de autenticação ao aplicativo do cliente][Adicionar o código de autenticação ao aplicativo do cliente]
5.  [Testar o cliente usando autenticação][Testar o cliente usando autenticação]

Este tutorial exige o seguinte:

-   XCode 4.5 e iOS 6.0 (ou versões posteriores)
-   Conclusão do tutorial [Introdução aos Serviços Móveis][Introdução aos serviços móveis] ou [Introdução aos Dados][Introdução aos Dados].
-   SDK de Serviços Móveis do Microsoft Azure
-   A [Biblioteca de Autenticação do Active Directory para iOS][Biblioteca de Autenticação do Active Directory para iOS]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service][mobile-services-dotnet-adal-register-service]]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-client][mobile-services-dotnet-adal-register-client]]

## <a name="require-authentication"></a>Configurar o serviço móvel para requerer autenticação

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]]

## <a name="require-authentication"></a>Adicionar o código de autenticação ao aplicativo do cliente

1.  Baixar a [Biblioteca de Autenticação do Active Directory para iOS][Biblioteca de Autenticação do Active Directory para iOS] e incluí-la em seu projeto. Certifique-se de adicionar também storyboards da fonte ADAL.

2.  No QSTodoListViewController, inclua ADAL com o seguinte:

        #import "ADALiOS/ADAuthenticationContext.h"

3.  Em seguida, adicione o seguinte método:

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

4.  No código para o método acima `loginAndGetData`, substitua **INSIRA-AUTORIDADE-AQUI** pelo o nome do locatário no qual o seu aplicativo foi provisionado. O formato deve ser <https://login.windows.net/tenant-name.onmicrosoft.com>. Este valor pode ser copiado da guia Domínio no Azure Active Directory no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

5.  No código para o método `loginAndGetData` acima, substitua **INSIRA-RECURSO-URI-AQUI** com o **URI do ID do aplicativo** para o seu serviço móvel. Se o tópico [Como registrar-se com o Azure Active Directory][Como registrar-se com o Azure Active Directory] foi seguido, a sua URI do ID do aplicativo deve ser semelhante a <https://todolist.azure-mobile.net/login/aad>.

6.  No código, para o método `loginAndGetData` acima, substitua **INSIRA-ID-DO-CLIENTE-AQUI** com o ID do cliente copiado do aplicativo do cliente nativo.

7.  No código para o método `loginAndGetData` acima, substitua **INSERT-REDIRECT-URI-HERE** pelo ponto de extremidade /login/done de seu serviço móvel. Isso deve ser similar a <https://todolist.azure-mobile.net/login/done>.

8.  No QSTodoListViewController, modifique `ViewDidLoad` substituindo `[self refresh]` pelo seguinte:

        [self loginAndGetData];

## <a name="test-client"></a>Testar o cliente usando autenticação

1.  No menu Produto, clique em Executar para iniciar o aplicativo
2.  Você receberá uma solicitação para fazer logon no seu Azure Active Directory.
3.  O aplicativo autentica e retorna os itens todo.

![][]

<!-- Anchors. -->
<!-- URLs. -->

  [C# da Windows Store]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication "C# da Windows Store"
  [iOS]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication "iOS"
  [Xamarin.iOS]: /pt-br/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication "Xamarin.iOS"
  [Introdução aos serviços móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
  [Registrar o seu serviço móvel com o Active Directory do Azure]: #register-mobile-service-aad
  [Registrar o seu aplicativo com o Azure Active Directory]: #register-app-aad
  [Configurar o serviço móvel para requerer autenticação]: #require-authentication
  [Adicionar o código de autenticação ao aplicativo do cliente]: #add-authentication-code
  [Testar o cliente usando autenticação]: #test-client
  [Introdução aos Dados]: /pt-br/documentation/articles/mobile-services-ios-get-started-data/
  [Biblioteca de Autenticação do Active Directory para iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
  [mobile-services-dotnet-adal-register-service]: ../includes/mobile-services-dotnet-adal-register-service.md
  [mobile-services-dotnet-adal-register-client]: ../includes/mobile-services-dotnet-adal-register-client.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [Como registrar-se com o Azure Active Directory]: /pt-br/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  []: ./media/mobile-services-dotnet-backend-ios-adal-sso-authentication/mobile-services-app-run.png
