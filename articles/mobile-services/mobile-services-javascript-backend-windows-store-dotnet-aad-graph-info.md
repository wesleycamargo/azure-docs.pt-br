<properties 
	pageTitle="Acessando informações do Azure Active Directory Graph (Windows Store) | Mobile Dev Center" 
	description="Saiba como acessar informações do Active Directory do Azure usando a Graph API em seu aplicativo da Windows Store." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="wesmc"/>

# Acessando informações do Azure Active Directory Graph


[AZURE.INCLUDE [mobile-services-selector-aad-graph](../../includes/mobile-services-selector-aad-graph.md)]
##Visão geral

Assim como outros provedores de identidade oferecidos com os Serviços Móveis, o provedor do Active Directory do Azure (AAD) também dá suporte a uma Graph API rica, que pode ser usada para acesso programático ao diretório. Neste tutorial você atualiza o aplicativo ToDoList para personalizar a experiência do aplicativo de usuário autenticado com base em informações de usuário adicionais que você recupera do diretório usando a [API REST Graph].

Para obter mais informações sobre a API do Azure AD Graph, consulte o [Blog da equipe do Azure Active Directory Graph].

>[AZURE.NOTE]O objetivo deste tutorial é ampliar o seu conhecimento de autenticação com o Active Directory do Azure. É esperado que você tenha concluído o tutorial [Adicionar Autenticação ao aplicativo] usando o provedor de autenticação do Active Directory do Azure. Este tutorial continua a atualizar o aplicativo TodoItem usado no tutorial [Adicionar Autenticação ao aplicativo].



##Pré-requisitos 

Antes de iniciar este tutorial, você já deve ter concluído estes tutoriais dos Serviços Móveis:

+ [Adicionar Autenticação ao seu aplicativo] <br/>Adiciona um requisito de logon ao aplicativo de amostra TodoList.

+ [Tutorial de API personalizada]<br/>Demonstra como chamar uma API personalizada.



##Gerar uma chave de acesso para registro de aplicativo no AAD


Durante o tutorial [Adicionar Autenticação ao aplicativo],você criou um registro para o aplicativo integrado quando concluiu a etapa [Registrar-se para usar um logon do Active Directory do Azure]. Nesta seção, você gera uma chave para ser usada ao ler as informações de diretório com essa ID integrada de cliente do aplicativo.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]



##Criar uma API personalizada do GetUserInfo

Nesta seção, você criará a API personalizada GetUserInfo que usará a [Graph API do REST] para recuperar informações adicionais sobre o usuário do AAD.

Se você nunca usou APIs personalizadas com Serviços Móveis, deve considerar examinar o [Tutorial de API personalizada] antes de concluir esta seção.

1. No [Portal de Gerenciamento do Azure], crie a nova API personalizada GetUserInfo para seu serviço móvel e defina as permissões para o método get para **Somente usuários autenticados**.

    ![][0]

2. Abra o editor de script da nova API GetUserInfo e aquele das seguintes variáveis para a parte superior do script.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;



3. Adicione a seguinte definição para a função `getAADToken`.

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

4. Adicione a seguinte função `getUser` que usa a API Graph para retornar informações do usuário.

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

5. Atualize o método `get` exportado como segue para retornar informações do usuário usando outras funções.

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


##Atualizar o aplicativo para usar GetUserInfo


Nesta seção, você atualizará o método `AuthenticateAsync` implementado no tutorial [Introdução à autenticação] para chamar a API personalizada e retornar informações adicionais sobre o usuário por meio do AAD.

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../../includes/mobile-services-aad-graph-info-update-app.md)]


 


##Testar o aplicativo

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../../includes/mobile-services-aad-graph-info-test-app.md)]




##Próximas etapas

No próximo tutorial, [Controle de acesso baseado em função com o AAD em Serviços Móveis], você usará o controle de acesso baseado em função com o Active Directory do Azure (AAD) para verificar a associação de grupo antes de permitir o acesso.



<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png


<!-- URLs. -->
[Adicionar Autenticação ao aplicativo]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Adicionar Autenticação ao seu aplicativo]: ../mobile-services-windows-store-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Tutorial de API personalizada]: mobile-services-windows-store-dotnet-call-custom-api.md
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[Registrar-se para usar um logon do Active Directory do Azure]: mobile-services-how-to-register-active-directory-authentication.md
[Graph API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[API REST Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Graph API do REST]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Obter usuário]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Controle de acesso baseado em função com o AAD em Serviços Móveis]: mobile-services-javascript-backend-windows-store-dotnet-aad-rbac.md
[Blog da equipe do Azure Active Directory Graph]: http://go.microsoft.com/fwlink/?LinkId=510536

<!---HONumber=August15_HO6-->