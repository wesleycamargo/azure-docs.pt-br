<properties 
	pageTitle="Controle de acesso baseado em função nos Serviços Móveis e Active Directory do Azure (Windows Store) | Centro de desenvolvimento de Serviços Móveis" 
	description="Saiba como controlar o acesso com base em funções do Active Directory do Azure em seu aplicativo da Windows Store." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="wesmc"/>

# Controle de acesso baseado em função nos Serviços Móveis e Active Directory do Azure

[AZURE.INCLUDE [mobile-services-selector-rbac](../includes/mobile-services-selector-rbac.md)]

#Visão geral

O controle de acesso baseado em funções (RBAC) é a prática de atribuir permissões a funções que seus usuários podem ter, definindo claramente os limites sobre o que certas classes de usuários podem e não podem fazer. Este tutorial explica como adicionar RBAC básico aos Serviços Móveis do Azure.

Este tutorial demonstrará o controle de acesso baseado em função, verificando cada associação de usuário ao grupo de Vendas definido no Active Directory do Azure (AAD). A verificação de acesso será feita com JavaScript no back-end do serviço móvel usando a [API gráfica do Active Directory do Azure]. Somente usuários que pertencem à função Vendas podem consultar os dados.


>[AZURE.NOTE] O objetivo deste tutorial é ampliar o seu conhecimento de autenticação para incluir práticas de autorização. É esperado que você conclua primeiro o tutorial [Adicionar Autenticação a seu aplicativo de serviço móvel] usando o provedor de autenticação do Active Directory do Azure. Este tutorial continua a atualizar o aplicativo TodoItem usado no tutorial [Adicionar autenticação ao aplicativo].

##Pré-requisitos

Este tutorial exige o seguinte:

* Visual Studio 2013 em execução no Windows 8.1.
* Conclusão do tutorial [Adicionar autenticação a seu aplicativo de serviço móvel] usando o provedor de autenticação do Active Directory do Azure.
* Conclusão do tutorial [Armazenar scripts do servidor] para familiarização com o uso de um repositório Git para armazenamento de scripts de servidor.
 


##Criar um grupo de Vendas com associação

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../includes/mobile-services-aad-rbac-create-sales-group.md)]


##Gerar uma chave para o Aplicativo Integrado


Durante o tutorial [Adicionar autenticação a seu aplicativo de serviço móvel], você criou um registro para o aplicativo integrado quando concluiu a etapa [Registrar-se para usar um logon do Active Directory do Azure]. Nesta seção, você gera uma chave para ser usada ao ler as informações de diretório com essa ID integrada de cliente do aplicativo. 

Se seguiu o tutorial [Acessar informações de gráfico do Active Directory do Azure], você já concluiu essa etapa e poderá ignorar esta seção.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]





##Adicionar um script compartilhado ao serviço móvel que verifica a associação

Nesta seção, você usará o Git para implantar um arquivo de script compartilhado denominado *rbac.js* a seu serviço móvel. O arquivo de script compartilhado conterá as funções que usam a [API gráfica] para verificar a associação a um grupo do usuário. 

Se não estiver familiarizado com a implantação de scripts para seu serviço móvel com o Git, examine o tutorial [Armazenar scripts do servidor] antes de concluir esta seção.

1. Crie um novo arquivo de script denominado *rbac.js* no diretório *./service/shared/* do repositório local de seu serviço móvel.
2. Adicione o seguinte script à parte superior do arquivo que define a função `getAADToken`. Considerando o *tenant_domain*, o  *client id* de aplicativo integrado e a *key* de aplicativo, essa função fornece um token de acesso gráfico usado para ler as informações de diretório.

    >[AZURE.NOTE] Você deve armazenar o token em cache, em vez de criar um novo com cada verificação de acesso. Em seguida, atualize o cache ao tentar usar o resultado do token em uma resposta 401 Authentication_ExpiredToken como observado na [Referência de erro de API Gráfica]. Para simplificar, isso não está demonstrado no código abaixo, mas aliviará o tráfego extra na rede em seu Active Directory. 

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;
        exports.SalesGroup = appSettings.AAD_GROUP_ID;
         
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


3. Adicione o seguinte código a *rbac.js* para definir a função `getGroupId`. Essa função usa o token de acesso para obter a ID de grupo com base no nome do grupo usado em um filtro.
 
    >[AZURE.NOTE] Esse código pesquisa o grupo do Active Directory por nome. Em muitos casos, a melhor prática é armazenar a ID do grupo como uma configuração do aplicativo do serviço móvel e somente usar esse ID de grupo. Isso porque o nome do grupo pode mudar, mas a ID permanece a mesma. Entretanto, com a alteração de um nome do grupo, geralmente ocorre no mínimo uma alteração no escopo da função, o que também pode exigir uma atualização no código do serviço móvel.   

        function getGroupId(groupname, accessToken, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/groups" + 
                      "?$filter=displayName%20eq%20'" + groupname + "'" + 
	              "&api-version=2013-04-05" ,
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + accessToken,
                    "Content-Type": "application/json",
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value[0].objectId);
            });
        }


4. Adicione o seguinte código a *rbac.js* para definir a função `isMemberOf` que chama o ponto de extremidade [IsMemberOf] da API Gráfica do REST.

    Esta função é um thin wrapper em torno do ponto de extremidade [IsMemberOf] da API gráfica do REST. Ele utiliza o token de acesso gráfico para verificar se a ID do objeto do diretório do usuário tem associação no grupo com base na ID de grupo.

        function isMemberOf(access_token, userObjectId, groupObjectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/isMemberOf" + "?api-version=2013-04-05",
                method: 'POST',
                headers: {
                    "Authorization": "Bearer " + access_token,
                    "Content-Type": "application/json",
                },
                body: JSON.stringify({
                "groupId": groupObjectId,
                "memberId": userObjectId
                })
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value);
            });
        };

    

7. Adicione a seguinte função `checkGroupMembership` exportada a *rbac.js*.  

    Essa função encapsula o uso de outras funções de script e é exportada do script compartilhado para ser chamada por outros scripts para executar as verificações de acesso reais. Dado o objeto de usuário do serviço móvel, e a ID de grupo, o script recuperará a ID do objeto do Active Directory do Azure para a identidade do usuário e verificará a associação ao grupo.

        exports.checkGroupMembership = function (user, groupName, callback) {
            user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) callback(err);
		        else getGroupId(groupName, access_token, function (err, groupId) { 
                            if (err) callback(err);
                            else isMemberOf(access_token, objectId, groupId, function (err, isInGroup) {
       	                        if (err) errorHandler(err);
               	                else callback(null, isInGroup);
                            });
                        });
                    });
                },
                error: callback
            });
        }

8. Salve suas alterações em *rbac.js*.

##Adicionar verificação de acesso baseada em função às operações de banco de dados


Quando você concluiu a [adicionar autenticação ao seu aplicativo de serviço móvel] tutorial, você deve já tiver definido as operações de tabela para exigir autenticação, conforme mostrado abaixo.

![][3]

Com toda operação do banco de dados exigindo autenticação, podemos adicionar scripts que usam o objeto do usuário para verificações de acesso.

As etapas a seguir demonstram como implantar função com base no controle de acesso usando scripts para cada operação de tabela em seu serviço móvel. Um script, que usa o script *rbac.js* compartilhado, é executado para cada operação de tabela.

1. Adicione um novo arquivo de script denominado *todoitem.insert.js* no diretório ./service/table/ do repositório Git local para seu serviço móvel. Cole o seguinte script nesse arquivo.

        function insert(item, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

2. Adicione um novo arquivo de script denominado *todoitem.read.js* no diretório *./service/table/* do repositório Git local para seu serviço móvel. Cole o seguinte script nesse arquivo.

        function read(query, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

3. Adicione um novo arquivo de script denominado *todoitem.update.js* no diretório ./service/table/ do repositório Git local para seu serviço móvel. Cole o seguinte script nesse arquivo.

        function update(item, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

4. Adicione um novo arquivo de script denominado *todoitem.delete.js* no diretório ./service/table/ do repositório Git local para seu serviço móvel. Cole o seguinte script nesse arquivo.

        function del(id, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

5. Na interface de linha de comando de seu repositório Git, adicione acompanhamento para os arquivos de script que você adicionou executando o seguinte comando.

        git add .

6. Na interface de linha de comando para seu repositório Git, confirme as atualizações executando o seguinte comando.

        git commit -m "Added role based access control to table operations"
  
7. Na interface de linha de comando de seu repositório Git, implante as atualizações em seu diretório Git local para o serviço móvel executando o seguinte comando. Esse comando presume que você tenha feito as atualizações na ramificação *master*.

        git push origin master

8. No [Portal de Gerenciamento do Azure], é possível navegar pelas operações de tabela do serviço móvel e ver as atualizações implementadas, conforme mostrado abaixo. 

    ![][4]

##Testar o acesso do cliente

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../includes/mobile-services-aad-rbac-test-app.md)]







<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/users.png
[1]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/group-membership.png
[2]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/sales-group.png
[3]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/table-perms.png
[4]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/insert-table-op-view.png
[5]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/sales-group-id.png
[6]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/client-id-and-key.png

<!-- URLs. -->
[Adicionar autenticação ao seu aplicativo de serviço móvel]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md
[Como se registrar com o Active Directory do Azure]: mobile-services-how-to-register-active-directory-authentication.md
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Cenários de sincronização de diretório]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[Armazenar scripts de servidor]: mobile-services-store-scripts-source-control.md
[Registrar-se para usar um logon do Active Directory do Azure]: mobile-services-how-to-register-active-directory-authentication.md
[API gráfica]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Referência de erros de API gráfica]: http://msdn.microsoft.com/library/azure/hh974480.aspx
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx
[Acessando informações do Azure Active Directory Graph]: mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info.md

<!--HONumber=49-->