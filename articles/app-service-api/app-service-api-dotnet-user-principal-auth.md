<properties
	pageTitle="Autenticação de usuário para Aplicativos de API no Serviço de Aplicativo do Azure | Microsoft Azure"
	description="Saiba como proteger um aplicativo de API no Serviço de Aplicativo do Azure, permitindo acesso apenas a usuários autenticados."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/08/2016"
	ms.author="tdykstra"/>

# Autenticação de usuário para Aplicativos de API no Serviço de Aplicativo do Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Visão geral

Este tutorial mostra como usar os recursos de autenticação e autorização do Serviço de Aplicativo do Azure para proteger um aplicativo de API e como consumir o aplicativo de API em nome dos usuários finais. O provedor de autenticação exibido no tutorial é o Active Directory do Azure, a API é a API Web ASP.NET e o exemplo de cliente é um aplicativo de página única AngularJS em execução em um navegador.

![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)
 
## Autenticação e autorização no Serviço de Aplicativo

Para obter uma introdução aos recursos de autenticação usados neste tutorial, consulte o tutorial anterior desta série, [autenticação e autorização para Aplicativos de API no Serviço de Aplicativo do Azure](app-service-api-authentication.md).

## Como acompanhar este tutorial

Esse tutorial se baseia em um aplicativo de exemplo para o qual você baixa e cria um aplicativo de API no [primeiro tutorial da série de introdução dos Aplicativos de API e ASP.NET](app-service-api-dotnet-get-started.md).

## O exemplo de projeto ContactsList.Angular.AAD

No [aplicativo de exemplo ContactsList](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list), o projeto ContactsList.Angular.AAD é um cliente AngularJS que inclui um código para trabalhar com o Active Directory do Azure. O código se baseia em um exemplo do AAD que pode ser encontrado no repositório [Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi).

O código no projeto ContactsList.Angular.AAD é estruturado de forma diferente do projeto mais simples, ContactsLists.Angular. O código que chama a API está no arquivo *app/scripts/contactsSvc.js* no projeto ContactsList.Angular.AAD.

		angular.module('contactsListApp')
		.factory('contactsSvc', ['$http', function ($http) {
		    //var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		    var apiEndpoint = "https://localhost:44300";
		
		    $http.defaults.useXDomain = true;
		    delete $http.defaults.headers.common['X-Requested-With']; 
		
		    return {
		        getItems: function () {
		            return $http.get(apiEndpoint + '/api/contacts');
		        },
		        getItem : function(id){
		            return $http.get(apiEndpoint + '/api/contacts/' + id);
		        },
		        postItem : function(item){
		            return $http.post(apiEndpoint + '/api/contacts', item);
		        },
		        putItem : function(item){
		            return $http.put(apiEndpoint + '/api/contacts/', item);
		        },
		        deleteItem : function(id){
		            return $http({
		                method: 'DELETE',
		                url: apiEndpoint + '/api/contacts/' + id
		            });
		        }
		    };
		}]);

Aqui, o método `Get` é rotulado como `getItems`. No controlador (*app/scripts/contactsCtrl.js*), `getItems` está conectado a `$scope.populate`.

		$scope.populate = function () {
		    contactsSvc.getItems().then(function (results) {
		        $scope.contactsList = results.data;
		        $scope.loadingMessage = "";
		    }, function (err) {
		        $scope.loadingMessage = "";
		        $scope.error = "Error: " + err;
		    });
		};

No modo de exibição (*app/views/Contacts.html*), $scope.populate é chamado na inicialização.

		<div ng-init="populate()">

O código adicional para fazer logon e incluir um token de autorização com solicitações de API é fornecido pela [Biblioteca de Autenticação do Active Directory do Azure para JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js), nos arquivos *adal.js* e *adal-angular.js*.

No arquivo *app.js*, o código passa informações de configuração e o provedor `$http` para a função `adalProvider.init`. As informações de configuração incluem a ID de cliente do aplicativo do AAD referente a cada ponto de extremidade de API e a ID de cliente que pertence a esse aplicativo AngularJS. A função `init` adiciona interceptores para o provedor `$http`, que adicionam o token de autorização às solicitações.

		var endpoints = { 
		    //"https://{your api app name}.azurewebsites.net/": "{your client id}"
		    "https://localhost:44300/": "{your client id}"
		};

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: '{your tenant url}',
		        clientId: '{your client id}',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		        //cacheLocation: 'localStorage', // enable this for 
		    },
		    $httpProvider
		    );

## Configurar a autenticação e a autorização no Azure

1. No [portal do Azure](https://portal.azure.com/), navegue até a folha **Aplicativo de API** do aplicativo de API que você deseja proteger, de modo que apenas usuários autenticados possam chamá-lo. (Para este tutorial, escolha o aplicativo de API para o qual você implantou o projeto ContactsList.API).

2. Clique em **Configurações**

2. Localize a seção **Recursos** e clique em **Autenticação/Autorização**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Na folha **Autenticação/Autorização**, clique em **Ativada**.

4. Na lista suspensa **Ação a realizar quando a solicitação não está autenticada**, escolha **Fazer logon com o Active Directory do Azure**.

5. Em **Provedores de Autenticação**, clique em **Active Directory do Azure**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Na folha **Configurações do Active Directory do Azure**, clique em **Express**

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	Aqui, "Express" significa que o Azure criará automaticamente um aplicativo do AAD no locatário do AAD. Anote o nome do novo aplicativo AAD, pois você o selecionará posteriormente quando acessar o Portal clássico do Azure para obter a ID de cliente do novo aplicativo AAD.

7. Clique em **OK**.

10. Na folha **Autenticação/Autorização**, clique em **Salvar**.

8. Para verificar se o aplicativo de API está protegido agora, acesse a URL do aplicativo de API + `/swagger`, como você fez no primeiro tutorial para usar a interface do usuário do Swagger.

	Desta vez, você será redirecionado para uma página de logon.

	![](./media/app-service-api-dotnet-user-principal-auth/loginpage.png)

11. No [portal clássico do Azure](https://manage.windowsazure.com/), vá para **Active Directory do Azure**.

	Você precisa ir para o portal clássico porque determinadas configurações do Active Directory do Azure às quais você precisa ter acesso ainda não estão disponíveis no portal do Azure atual.

12. Na guia **Diretório**, clique no locatário do AAD.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Clique em **Aplicativos > Aplicativo que minha empresa possui** e clique na marca de seleção.

	Talvez também seja necessário atualizar a página para ver o novo aplicativo.

15. Na lista de aplicativos, clique no nome do aplicativo que o Azure criou quando você habilitou a autenticação para o aplicativo de API.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Clique em **Configurar**.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

15. Na parte inferior da página, clique em **Gerenciar manifesto > Baixar manifesto** e salve o arquivo em um local onde você possa editá-lo.

16. No arquivo de manifesto baixado, procure a propriedade `oauth2AllowImplicitFlow`. Altere o valor dessa propriedade de `false` para `true` e salve o arquivo.

	Essa configuração é necessária para o acesso de um aplicativo de página única de JavaScript. Ele habilita o token de portador Oauth 2.0 a ser retornado no fragmento de URL.

16. Clique em **Gerenciar manifesto > Carregar manifesto** e carregue o arquivo atualizado na etapa anterior.

17. Mantenha essa página aberta para que você possa copiar e colar valores dela e atualizar os valores na página em etapas posteriores do tutorial.

## Configurar o projeto ContactsList.Angular.AAD para chamar o aplicativo de API do Azure

As instruções a seguir explicam como implantar o aplicativo no Azure e executá-lo nele, mas, com algumas pequenas alterações, você pode executá-lo localmente. O código de exemplo contém pontos de extremidade de URL localhost. Se você quiser realizar a execução localmente, configure os projetos para SSL, use as URLs SSL localhost no código do projeto e use as URLs SSL localhost na configuração do aplicativo AAD. Durante a execução local, o código AngularJS só permitirá que usuários conectados chamem a API, mas chamadores não autenticados de outros clientes podem chamar a API.

1. No projeto ContactsList.Angular.AAD, abra o arquivo *app/scripts/app.js*.

8. No código que define a variável `endpoints`, marque como comentário o ponto de extremidade de localhost e remova a marca de comentário do ponto de extremidade do Azure.

10. Substitua "iddeseucliente" pelo valor real da ID de cliente do aplicativo do AAD da guia **Configurar** do portal clássico para o aplicativo do AAD.

2. Substitua "{nome do aplicativo de api}" pelo nome do aplicativo de API que você implantou no projeto ContactsList.API.

	Agora, o código é semelhante ao exemplo a seguir.

		var endpoints = {
		    "https://contactslistapi.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    //"https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. Além disso, em *app.js*, na chamada a `adalProvider.init`, substitua "{url do locatário}" e "{id do cliente}" pelos valores reais.

	O código será semelhante ao exemplo a seguir:

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: 'contoso.onmicrosoft.com',
		        clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		    },
		    $httpProvider
		    );

1. No arquivo *app/scripts/contactsSvc.js*, faça com que a mesma URL de ponto de extremidade mude de localhost para a URL do aplicativo de API, como você fez anteriormente em *App.js*.

	Agora, o código é semelhante ao exemplo a seguir.

		var apiEndpoint = "https://contactslistapi.azurewebsites.net";
		//var apiEndpoint = "https://localhost:44300";

## Configurar o aplicativo AAD para o aplicativo Web do Azure

1. Na guia **Configurar** do aplicativo do AAD no portal clássico, no campo **URL de Entrada**, exclua a URL que já está presente e substitua-a pela URL de base do aplicativo Web, incluindo a barra à direita. (Observe que essa é a URL do aplicativo Web que executará o código AngularJS, não a URL do aplicativo de API.)

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

3. No campo **URL de Resposta**, substitua a URL já existente pela URL de base do aplicativo Web.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

4. Clique em **Salvar**.

## Implantar o projeto ContactsList.Angular.AAD no Azure

8. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ContactsList.Angular.AAD e clique em **Publicar**.

9. Clique em **Serviço de Aplicativo do Microsoft Azure**

10. Na caixa de diálogo **Serviço de Aplicativo**, na lista suspensa **Assinatura**, escolha sua assinatura.

11. Expanda o grupo de recursos que você criou neste tutorial e selecione o aplicativo Web que você criou no segundo tutorial.

	![](./media/app-service-api-dotnet-user-principal-auth/deploytowebapp.png)

12. Clique em **OK**.

12. No assistente **Publicar Web**, clique na guia **Conexão** e, na caixa **URL de Destino**, mude `http://` para `https://`

	![](./media/app-service-api-dotnet-user-principal-auth/httpsinconntab.png)

	Essa configuração determina em qual URL o navegador padrão será aberto depois de uma implantação bem-sucedida.

12. No assistente **Publicar Web**, clique na guia **Configurações**, expanda **Opções de Publicação do Arquivo** e marque a caixa de seleção **Remover arquivos adicionais no destino**.

7. Clique em **Publicar**.

	O Visual Studio implanta o projeto e abre a página inicial do aplicativo em um navegador.

## Testar o aplicativo Web AngularJS no Azure

8. Clique na guia **Contatos**.

	Você receberá uma solicitação para fazer logon.

9. Faça logon com as credenciais de um usuário em seu locatário do AAD.

10. A página **Contatos** é exibida.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)

Agora, o front-end pode chamar a API em nome de um usuário autenticado, mas usuários não autenticados não podem chamar a API.

## Próximas etapas

Neste tutorial, você usou a Autenticação/Autorização do Serviço de Aplicativo para restringir o acesso a um aplicativo de API de modo que somente usuários autenticados possam chamá-lo. No próximo tutorial da série, você aprenderá a [restringir o acesso ao aplicativo de API para cenários entre serviços](app-service-api-dotnet-service-principal-auth.md).

<!---HONumber=AcomDC_0114_2016-->