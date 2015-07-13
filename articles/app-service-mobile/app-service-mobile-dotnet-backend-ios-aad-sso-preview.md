<properties 
        pageTitle="Autenticar os usuários do aplicativo iOS com o Logon Único do Active Directory do Azure" 
        description="Saiba como fazer logon de usuários em seu aplicativo iOS com a biblioteca de autenticação do Active Directory." 
        documentationCenter="Mobile" 
        authors="mattchenderson" 
        services="app-service\mobile" 
        manager="dwrede" />

<tags ms.service="app-service"
ms.workload="mobile"
ms.tgt_pltfrm="mobile-ios" 
ms.devlang="objective-c" 
ms.topic="article" 
ms.date="05/19/2015" 
ms.author="mahender" />

# Adicionar logon único do Active Directory do Azure ao seu aplicativo iOS

[AZURE.INCLUDE [app-service-mobile-selector-aad-sso](../../includes/app-service-mobile-selector-aad-sso.md)]

Neste tutorial, adicione a autenticação ao projeto de início rápido usando uma Biblioteca de autenticação do diretório ativo.

Para ser capaz de autenticar usuários, você deve registrar seu aplicativo com seu locatário do AAD (Active Directory do Azure). Isso é feito em duas etapas. Primeiro, você deve registrar seu serviço de aplicativo e expor permissões nele. Em segundo lugar, você deve registrar seu aplicativo iOS e conceder acesso com essas permissões.

Este tutorial exige o seguinte:

* XCode 4.5 e iOS 6.0 (ou versões posteriores)
* Conclusão do tutorial de [Introdução a Aplicativos Móveis].
* SDK de Serviços Móveis do Microsoft Azure
* A [Biblioteca de Autenticação do Active Directory para iOS]

## <a name="register-application"></a>Registrar seu aplicativo com o Active Directory do Azure

[AZURE.INCLUDE [app-service-mobile-adal-register-app](../../includes/app-service-mobile-adal-register-app.md)]

## <a name="require-authentication"></a>Configurar o aplicativo para exigir autenticação

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-adal"></a>Adicione uma referência à biblioteca de autenticação do Active Directory

1. Baixe a [Biblioteca de Autenticação do Active Directory para iOS].

2. No navegador do Xcode, selecione seu projeto e clique em **Arquivo** e escolha **Adicionar Arquivos a...**. Navegue até onde você baixou a biblioteca e selecione **ADALiOS.xcodeproj**.

3. Selecione o projeto novamente e abra a guia **Configurações da Compilação**. Navegue até a seção **Vinculação** e adicione `-ObjC` a **Outros Sinalizadores do Vinculador**.

4. Selecione a guia **Fases de Compilação**. Em **Dependências de Destino**, adicione `ADALiOS`.

5. Em **Vincular Binário com Bibliotecas**, adicione `libADALiOS.a`.

Agora você poderá referenciar a Biblioteca de Autenticação do Active Directory em seu projeto.

## <a name="add-authentication-code"></a>Adicionar o código de autenticação ao aplicativo do cliente

2. No QSTodoListViewController, inclua ADAL com o seguinte:

        #import "ADALiOS/ADAuthenticationContext.h"

3. Em seguida, adicione o seguinte método:

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

4. No código para o método `loginAndGetData` acima, substitua **INSERIR-AUTORIDADE-AQUI** pelo nome do locatário em que você provisionou seu aplicativo, o formato deve ser https://login.windows.net/tenant-name.onmicrosoft.com. Este valor pode ser copiado da guia Domínio no Azure Active Directory no [Portal de Gerenciamento do Azure].

5. No código para o método `loginAndGetData` acima, substitua **INSERIR-URI-DO-RECURSO-AQUI** pelo URI da **ID do Aplicativo** para o seu Aplicativo Móvel. Se você acompanhou o tópico [Como configurar seu aplicativo móvel com o Active Directory do Azure], o URI da ID do aplicativo deve ser similar a https://contosogateway.azurewebsites.net/login/aad.

6. No código, para o método `loginAndGetData` acima, substitua **￼INSERIR-ID-DO-CLIENTE-AQUI** pela ID do cliente copiada do aplicativo do cliente nativo.

7. No código para o método `loginAndGetData` acima, substitua **INSERIR-URI-DE-REDIRECIONAMENTO-AQUI** pelo ponto de extremidade de logon/feito para seu Gateway do Serviço de Aplicativo. Esse deve ser semelhante a https://contosogateway.azurewebsites.net/login/done.

8. No QSTodoListViewController, modifique `viewDidLoad` substituindo `[self refresh]` pelo seguinte:

        [self loginAndGetData];

## <a name="test-client"></a>Testar o cliente usando autenticação

1. No menu Produto, clique em Executar para iniciar o aplicativo
2. Você receberá uma solicitação para fazer logon no seu Active Directory do Azure.  
3. O aplicativo autentica e retorna os itens todo.

<!-- URLs. -->
[Como configurar seu aplicativo móvel com o Active Directory do Azure]: app-service-mobile-how-to-configure-active-directory-authentication-preview.md
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Biblioteca de Autenticação do Active Directory para iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
 [Introdução a Aplicativos Móveis]: app-service-mobile-dotnet-backend-ios-get-started-preview.md
 

<!---HONumber=62-->