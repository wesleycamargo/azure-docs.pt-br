---
title: Autenticar o seu aplicativo com Logon Único da biblioteca de autenticação do Active Directory (iOS) | Microsoft Docs
description: Saiba como autenticar usuários para logon único com ADAL no seu aplicativo iOS.
documentationcenter: ios
author: mattchenderson
manager: dwrede
editor: ''
services: mobile-services

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/21/2016
ms.author: mahender

---
# Autenticar o seu aplicativo com Logon Único da biblioteca de autenticação do diretório ativo
[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[!INCLUDE [mobile-services-selector-adal-sso](../../includes/mobile-services-selector-adal-sso.md)]

## Visão geral
Neste tutorial, adicione a autenticação ao projeto de início rápido usando uma Biblioteca de autenticação do diretório ativo.

Para poder autenticar os usuários, deve-se registrar o seu aplicativo com o Azure Active Directory (AAD). Isso é feito em duas etapas. Primeiro, deve-se registrar o seu serviço móvel e expor nele as permissões. Em segundo lugar, você deve registrar seu aplicativo iOS e conceder acesso com essas permissões

> [!NOTE]
> O objetivo deste tutorial é ajudar você a compreender melhor como os Serviços Móveis permitem efetuar autenticação do Active Directory do Azure com logon único para os aplicativos iOS. Se esta for sua primeira experiência com os Serviços Móveis, conclua o tutorial [Introdução aos serviços móveis].
> 
> 

## Pré-requisitos
Este tutorial exige o seguinte:

* XCode 4.5 e iOS 6.0 (ou versões posteriores)
* Conclusão do tutorial [Introdução aos Serviços Móveis].
* Conclusão de [Registrar seus aplicativos para usar um logon de conta do Active Directory do Azure]
* SDK de Serviços Móveis do Microsoft Azure
* A [Biblioteca de Autenticação do Active Directory para iOS]

[!INCLUDE [mobile-services-dotnet-adal-register-client](../../includes/mobile-services-dotnet-adal-register-client.md)]

## Configurar o serviço móvel para requerer autenticação
[!INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## Adicionar o código de autenticação ao aplicativo do cliente
1. Baixar a [Biblioteca de Autenticação do Active Directory para iOS] e incluí-la em seu projeto. Certifique-se de adicionar também storyboards da fonte ADAL.
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
4. No código para o método `loginAndGetData` acima, substitua **INSERIR-AUTORIDADE-AQUI** pelo nome do locatário em que você provisionou seu aplicativo, o formato deve ser https://login.windows.net/tenant-name.onmicrosoft.com. Este valor pode ser copiado da guia Domínio no Active Directory do Azure no [Portal clássico do Azure].
5. No código para o método `loginAndGetData` acima, substitua **INSIRA-RECURSO-URI-AQUI** com o **URI do ID do aplicativo** para o seu serviço móvel. Se seguiu o tópico [Como se registrar com o Active Directory do Azure], sua URI da ID do aplicativo deverá ser semelhante a https://todolist.azure-mobile.net/login/aad.
6. No código, para o método `loginAndGetData` acima, substitua **INSIRA-ID-DO-CLIENTE-AQUI** com o ID do cliente copiado do aplicativo do cliente nativo.
7. No código para o método `loginAndGetData` acima, substitua **INSIRA-REDIRECIONAR-URI-AQUI** pelo ponto de extremidade/logon/conclusão de seu serviço móvel. Ele deve ser semelhante a https://todolist.azure-mobile.net/login/done.
8. No QSTodoListViewController, modifique `ViewDidLoad` substituindo `[self refresh]` pelo seguinte:
   
        [self loginAndGetData];

## Testar o cliente usando autenticação
1. No menu Produto, clique em Executar para iniciar o aplicativo
2. Você receberá uma solicitação para fazer logon no seu Active Directory do Azure.
3. O aplicativo autentica e retorna os itens todo.
   
   ![](./media/mobile-services-dotnet-backend-ios-adal-sso-authentication/mobile-services-app-run.png)

<!-- URLs. -->
[Introdução aos serviços móveis]: mobile-services-dotnet-backend-ios-get-started.md
[Registrar seus aplicativos para usar um logon de conta do Active Directory do Azure]: mobile-services-how-to-register-active-directory-authentication.md
[Como se registrar com o Active Directory do Azure]: mobile-services-how-to-register-active-directory-authentication.md
[Portal clássico do Azure]: https://manage.windowsazure.com/
[Biblioteca de Autenticação do Active Directory para iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios

<!---HONumber=AcomDC_0727_2016-->