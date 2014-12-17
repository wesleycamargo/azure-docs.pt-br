<properties urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Acessando informações do Azure Active Directory Graph (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to access Azure Active Directory information using the Graph API in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/29/2014" ms.author="wesmc" />

# Acessando informações do Azure Active Directory Graph

[WACOM.INCLUDE [mobile-services-selector-aad-graph](../includes/mobile-services-selector-aad-graph.md)]


Assim como outros provedores de identidade oferecidos com os Serviços Móveis, o provedor do Active Directory do Azure (AAD) também dá suporte a uma [Graph API] rica, que pode ser usada para acesso programático ao diretório. Neste tutorial, você atualiza o aplicativo ToDoList para personalizar a experiência de aplicativo do usuário autenticado com base em informações adicionais de usuário que você recupera do diretório usando a [Graph API].

>[WACOM.NOTE] O objetivo deste tutorial é ampliar o seu conhecimento de autenticação com o Active Directory do Azure. É esperado que você tenha concluído o tutorial [Introdução à autenticação] usando o provedor de autenticação do Active Directory do Azure. Este tutorial continua a atualizar o aplicativo TodoItem usado no tutorial [Introdução à autenticação].



Este tutorial apresenta as seguintes etapas:


1. [Gerar uma chave de acesso para registro de aplicativo no AAD] 
2. [Criar uma API personalizada do GetUserInfo] 
3. [Atualizar o aplicativo para usar a API personalizada]
4. [Testar o aplicativo]

##Pré-requisitos 

Antes de iniciar este tutorial, você já deve ter concluído estes tutoriais dos Serviços Móveis:

+ [Introdução à autenticação]<br/>Adiciona um requisito de logon ao aplicativo de exemplo TodoList.

+ [Tutorial de API personalizada]<br/>Demonstra como chamar uma API personalizada. 



## <a name="generate-key"></a>Gerar uma chave de acesso para registro de aplicativo no AAD


Durante o tutorial [Introdução à autenticação], você criou um registro para o aplicativo integrado quando concluiu a etapa [Registrar-se para usar um logon do Active Directory do Azure]. Nesta seção, você gera uma chave para ser usada ao ler as informações de diretório com essa ID integrada de cliente do aplicativo. 

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]



## <a name="create-api"></a>Criar uma API personalizada do GetUserInfo

Nesta seção, você criará a API personalizada do GetUserInfo que usará a [Graph API do REST] para recuperar informações adicionais sobre o usuário por meio do AAD.

Se você nunca usou APIs personalizadas com Serviços Móveis, é conveniente consultar o [Tutorial de API Personalizada] antes de concluir esta seção.

1. No [Portal de Gerenciamento do Azure], crie a nova API personalizada GetUserInfo para seu serviço móvel e defina as permissões para o método get como **Somente usuários autenticados**.

    ![][0]

2. Abra o editor de script da nova API GetUserInfo e aquele das seguintes variáveis para a parte superior do script.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;



3. Adicione a seguinte definição à função `getAADToken`.

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

    Dados o *tenant_domain*, a *ID do cliente* do aplicativo integrado e a *chave* do aplicativo, essa função fornece um token de acesso gráfico usado para ler informações de diretório.

4. Adicione a seguinte função `getUser` que usa a Graph API para retornar as informações do usuário.

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

    Essa função é um thin wrapper em torno do ponto de extremidade [Obter usuário] da Graph API do REST. Use o token de acesso gráfico para obter informações de usuário usando a ID do objeto do usuário.

5. Para retornar informações do usuário usando outras funções, atualize o método `get` exportado conforme demonstrado a seguir.

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


Nesta seção, você atualizará o método `AuthenticateAsync` que implementou no tutorial [Introdução à Autenticação], para chamar a API personalizada e retornar informações adicionais sobre o usuário por meio do AAD. 

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]


 


## <a name="test-app"></a>Testar o aplicativo

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]




##<a name="next-steps"></a>Próximas etapas

No próximo tutorial, [Controle de acesso baseado em função com o AAD em Serviços Móveis], você usará o controle de acesso baseado em função com o Active Directory do Azure (AAD) para verificar a associação a um grupo antes de permitir o acesso. 


<!-- Anchors. -->
[Gerar uma chave de acesso para registro de aplicativo no AAD]: #generate-key
[Criar uma API personalizada do GetUserInfo]: #create-api
[Atualizar o aplicativo para usar a API personalizada]: #update-app
[Testar o aplicativo]: #test-app
[Próximas etapas]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png


<!-- URLs. -->
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Como se registrar com o Active Directory do Azure]: /pt-br/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Tutorial de API Personalizada]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
[Armazenar Scripts de Servidor]: /pt-br/documentation/articles/mobile-services-store-scripts-source-control/
[Registrar-se para usar um logon do Active Directory do Azure]: /pt-br/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Graph API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Graph API do REST]: http://msdn.microsoft.com/pt-br/library/azure/hh974478.aspx
[Obter usuário]: http://msdn.microsoft.com/pt-br/library/azure/dn151678.aspx
[Controle de acesso baseado em função com o AAD em Serviços Móveis]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/
