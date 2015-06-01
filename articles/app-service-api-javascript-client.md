<properties 
	pageTitle="Acessar um aplicativo de API do Azure usando HTML e JavaScript" 
	description="Saiba como acessar o back-end de seu aplicativo de API usando HTML e JavaScript." 
	services="app-service\api" 
	documentationCenter=".net"
	authors="bradygaster"
	manager="mohisri" 
	editor="tdykstra"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="bradygaster"/>

# Consumir um aplicativo de API do Azure usando HTML e JavaScript

## Visão geral

Este artigo mostra como criar um cliente HTML e JavaScript para um [aplicativo de API](app-service-api-apps-why-best-platform.md) no [Serviço De Aplicativo do Azure](/documentation/services/app-service/). Este artigo pressupõe conhecimento prático de HTML e JavaScript e terá foco no uso da estrutura JavaScript [AngularJS](https://angularjs.org/) para fazer chamadas REST para o aplicativo de API.

Alguns artigos para ler antes disso, para lhe ajudar a começar.

1. Em [Criar um aplicativo de API](app-service-dotnet-create-api-app.md), você criou um projeto de aplicativo de API.
2. Em [Implantar um aplicativo de API](app-service-dotnet-deploy-api-app.md), você implanta o aplicativo de API para sua assinatura do Azure.
3. Em [Depurar um aplicativo de API](app-service-dotnet-remotely-debug-api-app.md), você usa o Visual Studio para depurar remotamente o código enquanto ele é executado no Azure.

Este artigo se baseará nesses artigos anteriores demonstrando como seus aplicativos HTML podem usar JavaScript para acessar seus aplicativos de API de back-end.

## Habilitando CORS

Normalmente, o CORS (compartilhamento de recursos entre origens) é necessário em aplicativos HTML que serão atendidos a partir de hosts diferentes da própria API. Com aplicativos de API, há pelo menos duas opções para habilitar o CORS. Esta seção descreverá essas opções.

### Habilitando CORS para Gateways de aplicativo de API

Gateways de aplicativo de API podem ser configurados para habilitar CORS usando o Portal de Visualização do Azure. Adicionando a *appSetting* **MS_CrossDomainOrigins**, você pode especificar as URLs que têm permissão para chamar seu aplicativo de API. Esta seção explicará como usar este *appSetting* para habilitar o CORS no nível do Gateway de API.

1. Navegue até a folha Portal de visualização do Azure para o aplicativo de API para o qual você deseja habilitar o CORS. Tendo chegado lá, clique no ícone *Gateway* para seu aplicativo de API. 

	![Clicando no botão Gateway de aplicativo de API](./media/app-service-api-javascript-client/19-api-app-blade.png)

1. Clique no link **Host do Gateway** na folha do portal.

	![Clicando no link de host de Gateway de aplicativo de API](./media/app-service-api-javascript-client/20-gateway-host-blade.png)

1. Clique no link **Todas as configurações** na folha do portal.

	![Link de todas as configurações do gateway](./media/app-service-api-javascript-client/21-gateway-blade-all-settings.png)

1. Clique no botão **Configurações do aplicativo** na folha do portal.

	![Configurações do aplicativo de gateway](./media/app-service-api-javascript-client/22-gateway-app-settings-blade.png)

1. Adicione a configuração de aplicativo **MS_CrossDomainOrigins**. Defina como o valor da configuração a lista separada por vírgulas de hosts HTTP para os quais você deseja fornecer acesso ao seu aplicativo de API. Se você deseja fornecer acesso a vários hosts, o valor de *appSetting* pode ser definido como algo parecido com o código a seguir.

		http://foo.azurewebsites.net, https://foo.azurewebsites.net, http://contactlistwebapp.azurewebsites.net

	A seção a seguir orientará você durante o processo de criação de um aplicativo web separado que contenha uma página HTML simples, que chama o aplicativo de API a partir de um aplicativo Web em execução no mesmo grupo de recursos do Azure que o aplicativo de API. Como esse é o único host que terá permissão de acesso ao aplicativo de API, a configuração será definida para conter um host.

		http://contactlistwebapp.azurewebsites.net

	A captura de tela abaixo demonstra como essa configuração deve parecer depois de você tê-la salvo no Portal de Visualização do Azure.

	![](./media/app-service-api-javascript-client/23-app-settings-set.png)

A configuração de aplicativo **MS_CrossDomainOrigins** é descrita detalhadamente na postagem de blog [Atualizações do .NET do Serviço Móvel do Azure](http://azure.microsoft.com/blog/2014/07/28/azure-mobile-services-net-updates/), por isso verifique esta postagem para obter mais detalhes sobre os detalhes da configuração.

### Habilitando CORS no código de API da Web

O processo de habilitar CORS na API Web está documentado no artigo ASP.NET [Permitindo solicitações entre origens na API Web 2 ASP.NET](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api) detalhadamente. Para aplicativos API construídos usando o API Web de ASP.NET o processo é exatamente o mesmo, mas será resumido abaixo. Ignore esta seção se você já tiver habilitado o CORS, já que seu aplicativo de API já deve estar configurado corretamente.

1. A funcionalidade CORS é fornecida pelo pacote do NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/). Instale-o abrindo o **Console do Gerenciador de Pacotes** e execute o script do PowerShell a seguir. 

		Install-Package Microsoft.AspNet.WebApi.Cors

1. Depois que o comando for executado, o Console do Gerenciador de Pacotes e **packages.config** refletirão a adição do novo pacote do NuGet.

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/01-cors-installed.png)

1. Abra o arquivo *App_Start/WebApiConfig.cs*. Adicione a linha de código abaixo ao método **Register** da classe **WebApiConfig** no arquivo.

		config.EnableCors();

	Depois que o arquivo é atualizado, o código deve ser semelhante ao seguinte:

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
				config.EnableCors(); /* -- NEW CODE -- */
	
	            // Web API routes
	            config.MapHttpAttributeRoutes();
	
	            config.Routes.MapHttpRoute(
	                name: "DefaultApi",
	                routeTemplate: "api/{controller}/{id}",
	                defaults: new { id = RouteParameter.Optional }
	            );
	        }
	    }

1. A etapa final para habilitar o CORS é demarcar os métodos de ação individuais que você deseja habilitar. Adicione o atributo **EnableCors** em cada um dos métodos ou no controlador inteiro, como demonstrado no código a seguir.

	> **Observação**: o uso de curingas para todos os parâmetros com o atributo EnableCors destina-se apenas a fins de demonstração e abrirá sua API para todas as origens e solicitações HTTP. Use esse atributo com cuidado e entenda as implicações.

		using ContactList.Models;
		using System.Collections.Generic;
		using System.Web.Http;
		using System.Web.Http.Cors;
	
		namespace ContactList.Controllers
		{
		    [EnableCors(origins:"*", headers:"*", methods: "*")] /* -- NEW CODE -- */
		    public class ContactsController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Contact> Get()
		        {
		            return new Contact[]
		            {
		                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
		                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
		                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
		            };
		        }
		
		        [HttpPost]
		        public Contact Post([FromBody] Contact contact)
		        {
		            return contact;
		        }
		    }
		}
1. Se você já tiver implantado seu Aplicativo de API no Azure antes de adicionar suporte para CORS, implante novamente o código para que o CORS seja habilitado em sua API hospedada do Azure. 

## Criando um aplicativo Web para consumir o aplicativo de API

Nesta seção, você vai criar um novo aplicativo Web vazio, instalar e usar nele o AngularJS e associar um front-end de HTML simples para o aplicativo de API. Você implantará o aplicativo Web que realiza o consumo no serviço de aplicativo do Azure. O aplicativo da Web HTML associará e exibirá os dados recuperados do aplicativo de API e fornecerá aos usuários uma interface de usuário simples para a API de contatos.

1. Clique com o botão direito na solução e selecione **Adicionar -> Novo projeto**

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/02-add-project.png)

1. Selecione o modelo **Aplicativo Web ASP.NET**.

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/03-new-web-project.png)

1. Selecione o modelo **Vazio** na caixa de diálogo One ASP.NET.

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/04-empty-web.png)

1. Usando o **Gerenciador de pacotes** ou usando o item de contexto de menu **Gerenciar pacotes NuGet**, instale o pacote do NuGet [AngularJS](https://www.nuget.org/packages/angularjs).

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/05-install-angular.png)

1. Usando o **Gerenciador de pacotes** ou usando o item de menu de contexto **Gerenciar pacotes NuGet**, instale o pacote do NuGet [AngularJS](https://www.nuget.org/packages/bootstrap).

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/05-install-bootstrap.png)

1. Adicione um novo arquivo HTML ao projeto de aplicativo Web.

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/06-new-html-file.png)

1. Nomeie o arquivo *index.html*.

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/07-index-html.png)

1. Adicione os arquivos de inicialização CSS e AngularJS JavaScript à página HTML; use também um modelo simples de inicialização ([como este](http://getbootstrap.com/examples/starter-template/)) e crie uma marca de script vazia para preparar a página.
	
	> Observação: os comentários no código HTML e JavaScript abaixo são prelúdios de etapas subsequentes nesta seção.

		<!DOCTYPE html>
		<html xmlns="http://www.w3.org/1999/xhtml">
		<head>
		    <title>Contacts HTML Client</title>
		    <link href="Content/bootstrap.css" rel="stylesheet" />
		    <style type="text/css">
		        body {
		            margin-top: 60px;
		        }
		    </style>
		</head>
		<body>
		
		    <nav class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
		                    <span class="sr-only">Toggle navigation</span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		                <a class="navbar-brand" href="#">Contacts</a>
		            </div>
		            <div id="navbar" class="collapse navbar-collapse">
		                <ul class="nav navbar-nav"></ul>
		            </div>
		        </div>
		    </nav>
		
		    <div class="container">
		        <!-- contacts ui here -->
		    </div>
		
		    <script src="Scripts/angular.js" type="text/javascript"></script>
		    <script type="text/javascript">
		        /* client javascript code here */
		    </script>
		
		</body>
		</html>

1. Adicione o código HTML à tabela abaixo para o elemento *div* do **contêiner** no HTML.

		<!-- contacts ui here -->
        <table class="table table-striped" ng-app="myApp" ng-controller="contactListCtrl">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Email</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr ng-repeat="con in contacts">
                    <td>{{con.Id}}</td>
                    <td>{{con.Name}}</td>
                    <td>{{con.EmailAddress}}</td>
                    <td></td>
                </tr>
            </tbody>
            <tfoot>
                <tr>
                    <th>Create a new Contact</th>
                    <th colspan="2">API Status: {{status}}</th>
                    <th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>
                </tr>
                <tr>
                    <td><input type="text" placeholder="ID" ng-model="newId" /></td>
                    <td><input type="text" placeholder="Name" ng-model="newName" /></td>
                    <td><input type="text" placeholder="Email Address" ng-model="newEmail" /></td>
                    <td><button class="btn btn-sm btn-success" ng-click="create()">Create</button></td>
                </tr>
            </tfoot>
        </table>

1. Clique com o botão direito no arquivo *index.html* e selecione o item de menu **Definir como página inicial**. Em seguida, depure o projeto da Web para que ela seja aberta no seu navegador padrão.

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/08-run-the-web-app.png)

1. Anote os guidões modelo na saída HTML. Você vinculará esses elementos HTML por dados, usando o AngularJS, na próxima etapa.

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/09-template-ui.png)

1. Adicione o código JavaScript a seguir ao arquivo *index.html* para chamar a API e vincule a interface do usuário HTML por dados à saída de API.

		/* client javascript code here */
        angular.module('myApp', []).controller('contactListCtrl', function ($scope, $http) {
            $scope.baseUrl = 'http://localhost:1578';

            $scope.refresh = function () {
                $scope.status = "Refreshing Contacts...";
                $http({
                    method: 'GET',
                    url: $scope.baseUrl + '/api/contacts',
                    headers: {
                        'Content-Type': 'application/json'
                    }
                }).success(function (data) {
                    $scope.contacts = data;
                    $scope.status = "Contacts loaded";
                }).error(function (data, status) {
                    $scope.status = "Error loading contacts";
                });
            };

            $scope.create = function () {
                $scope.status = "Creating a new contact";

                $http({
                    method: 'POST',
                    url: $scope.baseUrl + '/api/contacts',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    data: {
                        'Id': $scope.newId,
                        'Name': $scope.newName,
                        'EmailAddress': $scope.newEmail
                    }
                }).success(function (data) {
                    $scope.status = "Contact created";
                    $scope.refresh();
                    $scope.newId = 0;
                    $scope.newName = '';
                    $scope.newEmail = '';
                });
            };

            $scope.refresh();
        });

	> **Observação**: o número de porta pode variar, portanto, se seu projeto de API estiver sendo executado em uma porta diferente, simplesmente altere o JavaScript acima para refletir seu próprio ambiente.

1. Certifique-se de que o projeto de aplicativo de API também está em execução; caso contrário, o HTML JavaScript não funcionará corretamente. Clique com o botão direito na solução e selecione **Propriedades**. Em seguida, defina ambos os projetos da Web como **Iniciar sem depuração**, e especifique que o projeto de API seja executado primeiro.

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/10-run-both-web-projects.png)

1. Depure a solução e você verá que o cliente HTML/JavaScript pode conectar e exibir dados do projeto de aplicativo de API.

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/11-web-client-running.png)

## Implantando o aplicativo Web

Nesta seção você implantará o cliente HTML/JavaScript como um aplicativo Web do serviço de aplicativo. Depois que a implantação for concluída, você vai alterar a URL que o JavaScript está usando para consumir o aplicativo de API implantado.

> Observação: esta seção presume que você leu e concluiu o artigo [Implantar um aplicativo de API](app-service-dotnet-deploy-api-app.md) ou anteriormente implantou seu próprio aplicativo de API.

1. Abra a folha do aplicativo de API no Portal de Visualização do Azure. Clique na URL na folha para abri-la em seu navegador. Depois que ela se abrir, copie a URL do aplicativo de API da barra de endereços do navegador. 

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/12-open-api-app-from-blade.png)

1. Cole a URL do aplicativo de API para substituir o valor anterior para a propriedade **$scope.baseUrl** no código JavaScript.

		$scope.baseUrl = 'https://microsoft-apiappf7e042ba8e5233ab4312021d2aae5d86.azurewebsites.net';

1. Clique com o botão direito no projeto da Web HTML/JavaScript e selecione o item de menu de contexto **Publicar**.

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/13-publish-web-app.png)

1. Selecione a opção **Aplicativos Web do Microsoft Azure** na caixa de diálogo Publicar na Web.

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/14-publish-web-dialog.png)

1. Clique no botão **Novo** para criar um novo aplicativo Web.

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/15-new-web-app.png)

1. Selecione os mesmos plano de hospedagem de aplicativo e grupo de recursos nos quais seu aplicativo de API já está em execução.

	> **Observação**: isso não é um requisito, mas para fins de demonstração facilita a limpeza posterior dos recursos do Azure, se tudo estiver contido em um grupo de recursos.

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/16-new-web-app-creation-dialog.png)

1. Conclua as etapas de publicação na Web para implantar o cliente HTML/JavaScript para aplicativos Web do serviço de aplicativo.
1. Depois que o aplicativo Web é implantado automaticamente ele deve abrir no navegador Web e exibir os dados do aplicativo de API. 

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/17-web-app-running-in-ie.png)

1. Neste ponto, se você navegar para o grupo de recursos, verá o novo aplicativo Web em execução juntamente com o aplicativo de API.

	![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-javascript-client/18-web-app-visible-in-resource-group.png)

## Resumo 
Este exemplo demonstrou como você pode usar AngularJS como sua plataforma de JavaScript para acessar os back-ends de aplicativos de API. Você pode alterar a funcionalidade de acesso do REST para usar qualquer outra estrutura JavaScript. <!--HONumber=52-->
