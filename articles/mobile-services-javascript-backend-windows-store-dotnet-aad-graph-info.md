<properties linkid="develop-mobile-tutorials-javascript-aad-graph-info" urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Accessing Azure Active Directory Graph Information (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to access Azure Active Directory information using the Graph API in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/20/2014" ms.author="wesmc" />

# Acessando informações do Azure Active Directory Graph

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-aad-graph-info/" title="C# da Windows Store" class="current">C# da Windows Store</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/" title="Back-end do .NET">Back-end do .NET</a> |
    <a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/" title="Back-end do JavaScript" class="current">Back-end do JavaScript</a>
</div>

Como outros provedores de identidade oferecidos com Serviços Móveis, o provedor do Active Directory do Azure (AAD) também suporta uma [Graph API][Graph API] rica que pode ser usada para acesso programático ao diretório. Neste tutorial você atualiza o aplicativo ToDoList para personalizar a experiência do aplicativo de usuário autenticado com base em informações de usuário adicionais que você recupera do diretório usando a [Graph API][Graph API].

> [WACOM.NOTE] O objetivo deste tutorial é ampliar o seu conhecimento de autenticação com o Active Directory do Azure. É esperado que você tenha concluído o tutorial [Introdução à autenticação][Introdução à autenticação] usando o provedor de autenticação do Active Directory do Azure. Este tutorial continua a atualizar o aplicativo TodoItem usado no tutorial [Introdução à autenticação][Introdução à autenticação].

Este tutorial apresenta as seguintes etapas:

1.  [Gerar uma chave de acesso para registro de aplicativo no AAD][Gerar uma chave de acesso para registro de aplicativo no AAD]
2.  [Criar uma API personalizada do GetUserInfo][Criar uma API personalizada do GetUserInfo]
3.  [Atualizar o aplicativo para usar a API personalizada][Atualizar o aplicativo para usar a API personalizada]
4.  [Testar o aplicativo][Testar o aplicativo]

## Pré-requisitos

Antes de iniciar este tutorial, você já deve ter concluído estes tutoriais dos Serviços Móveis:

-   [Introdução à autenticação][Introdução à autenticação]
    Adiciona um requisito de logon ao aplicativo de amostra TodoList.

-   [Tutorial de API personalizada][Tutorial de API personalizada]
    Demonstra como chamar uma API personalizada.

## <a name="generate-key"></a>Gerar uma chave de acesso para registro de aplicativo no AAD

Durante o tutorial [Introdução à autenticação][Introdução à autenticação], você criou um registro para o aplicativo integrado quando concluiu a etapa [Registrar-se para usar um logon do Active Directory do Azure][Registrar-se para usar um logon do Active Directory do Azure]. Nesta seção, você gera uma chave para ser usada ao ler as informações de diretório com essa ID integrada de cliente do aplicativo.

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]

## <a name="create-api"></a>Criar uma API personalizada do GetUserInfo

Nesta seção, você criará a API personalizada GetUserInfo que usará a [Graph API do REST][Graph API do REST] para recuperar informações adicionais sobre o usuário do AAD.

Se você nunca usou APIs personalizadas com Serviços Móveis, deve considerar examinar o [Tutorial de API personalizada][Tutorial de API personalizada] antes de concluir esta seção.

1.  No [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], crie a nova API personalizada GetUserInfo para seu serviço móvel e defina as permissões para o método get para **Somente usuários autenticados**.

    ![][0]

2.  Abra o editor de script da nova API GetUserInfo e aquele das seguintes variáveis para a parte superior do script.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;

3.  Adicione a seguinte definição para a função `getAADToken`.

        function getAADToken(callback) {
            var req = require("request");
            var options = {
                url: "https://login.windows.net/" + tenant_domain + "/oauth2/token?api-version=1.0",
                method: 'POST',
                form: {
                    grant_type: "client_credentials",
                    resource: "https://graph.windows.net",
                    client_id: clientID,
                    client_secret: key
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).access_token);
            });
        }

    Dado o *tenant\_domain*, *ID do cliente* do aplicativo integrado e *chave* do aplicativo, essa função fornece um token de acesso gráfico usado para ler informações de diretório.

4.  Adicione a seguinte função `getUser` que usa a Graph API para retornar informações do usuário.

        function getUser(access_token, objectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/users/" + objectId + "?api-version=1.0",
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + access_token
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body));
            });
        };

    Essa função é um thin wrapper em torno do ponto de extremidade [Obter usuário][Obter usuário] da Graph API do REST. Use o token de acesso gráfico para obter informações de usuário usando a ID do objeto do usuário.

5.  Atualize o método `get` exportado como segue para retornar informações do usuário usando outras funções.

        exports.get = function (request, response) {
            if (request.user.level == 'anonymous') {
                response.send(statusCodes.UNAUTHORIZED, null);
                return;
            }
            var errorHandler = function (err) {
                console.log(err);
                response.send(statusCodes.INTERNAL_SERVER_ERROR, err);
            };
            request.user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) errorHandler(err);
                        else getUser(access_token, objectId, function (err, user_info) {
                            if (err) errorHandler(err);
                            else {
                                console.log('GetUserInfo: ' + JSON.stringify(user_info, null, 4));
                                response.send(statusCodes.OK, user_info);
                              }
                            });
                    });
                },
                error: errorHandler
            });
        };

## <a name="update-app"></a>Atualizar o aplicativo para usar GetUserInfo

Nesta seção, você atualizará o método `AuthenticateAsync` que implementou no tutorial [Introdução à autenticação][Introdução à autenticação] para chamar a API personalizada e retornar informações adicionais sobre o usuário por meio do AAD.

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]

## <a name="test-app"></a>Testar o aplicativo

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]

## <a name="next-steps"></a>Próximas etapas

No próximo tutorial, [Controle de acesso baseado em função com o AAD em Serviços Móveis][Controle de acesso baseado em função com o AAD em Serviços Móveis], você usará o controle de acesso baseado em função com o Active Directory do Azure (AAD) para verificar a associação de grupo antes de permitir o acesso.


<!-- Images -->


  [Graph API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Gerar uma chave de acesso para registro de aplicativo no AAD]: #generate-key
  [Criar uma API personalizada do GetUserInfo]: #create-api
  [Atualizar o aplicativo para usar a API personalizada]: #update-app
  [Testar o aplicativo]: #test-app
  [Tutorial de API personalizada]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
  [Registrar-se para usar um logon do Active Directory do Azure]: /pt-br/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [Graph API do REST]: http://msdn.microsoft.com/pt-br/library/azure/hh974478.aspx
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png
  [Obter usuário]: http://msdn.microsoft.com/pt-br/library/azure/dn151678.aspx
  [Controle de acesso baseado em função com o AAD em Serviços Móveis]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/
