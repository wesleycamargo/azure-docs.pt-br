<properties
	pageTitle="Consumir um aplicativo de API do JavaScript usando CORS | Microsoft Azure"
	description="Saiba como consumir um aplicativo de API no Serviço de Aplicativo do Azure, pelo cliente JavaScript e usando CORS."
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
	ms.topic="get-started-article"
	ms.date="03/04/2016"
	ms.author="tdykstra"/>

# Consumir um aplicativo de API do JavaScript usando CORS

## Visão geral

O Serviço de Aplicativo oferece suporte interno para Compartilhamento de Recursos Entre Origens (CORS), que permite que os clientes JavaScript façam chamadas entre domínios para aplicativos de API do Serviço de Aplicativo.

Por motivos de segurança, os navegadores impedem que o JavaScript faça chamadas de APIs para um domínio diferente daquele do qual o código JavaScript é proveniente. Por exemplo, você pode fazer uma chamada de uma página Web para um ponto de extremidade de API contoso.com, mas não para um ponto de extremidade fabrikam.com. O CORS é um protocolo de internet que foi projetado para permitir cenários em que é necessário fazer chamadas à API entre domínios. No Serviço de Aplicativo do Azure, um exemplo desse cenário é o cliente JavaScript estar em execução em um aplicativo Web enquanto a API está em execução em um aplicativo de API.

Este artigo contém duas seções:

* A seção [Como configurar CORS](#corsconfig) explica em termos gerais como configurar CORS para qualquer aplicativo de API e aplica-se igualmente a todas as estruturas às quais o Serviço de Aplicativo dá suporte, incluindo .NET, Node.js e Java. 

* Começando com a seção [Continuar com os tutoriais de Introdução do .NET](#tutorialstart), o tutorial orienta você na implantação de um aplicativo de exemplo .NET e na configuração de CORS para que o front-end JavaScript possa chamar o back-end de API da Web.

## <a id="corsconfig"></a> Como configurar CORS no Serviço de Aplicativo do Azure

Você pode configurar CORS no portal do Azure ou usando as ferramentas [Azure Resource Manager](../resource-group-overview.md).

#### Configurar CORS no portal do Azure

8. Em um navegador, acesse o [Portal do Azure](https://portal.azure.com/).

2. Clique em **Serviços de Aplicativos** e, em seguida, clique no nome do seu aplicativo de API.

	![](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. Na folha **Configurações** que aparece à direita da folha **Aplicativo de API**, encontre a seção **API** e, em seguida, clique em **CORS**.

	![](./media/app-service-api-cors-consume-javascript/clicksettings.png)

11. Na caixa de texto, digite a(s) URL(s) da(s) qual(is) você deseja permitir chamadas JavaScript.


	Por exemplo, se você implantou o aplicativo JavaScript para um aplicativo Web chamado todolistangular, digite "https://todolistangular.azurewebsites.net". Como alternativa, você pode inserir um asterisco (*) para especificar que todos os domínios de origem são aceitos.


13. Clique em **Salvar**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

	Depois que você clicar em **Salvar**, o aplicativo de API aceitará chamadas JavaScript da(s) URL(s) especificada(s).

### Configurar CORS usando as ferramentas do Gerenciador de Recursos do Azure

Você também pode configurar o CORS para um aplicativo de API usando os [modelos do Azure Resource Manager](../resource-group-authoring-templates.md) nas ferramentas de linha de comando, como o [Azure PowerShell](../powershell-install-configure.md) e a [CLI do Azure](../xplat-cli-install.md).

Para ver um exemplo de um modelo do Azure Resource Manager que defina a propriedade do CORS, abra o [arquivo azuredeploy.json no repositório do aplicativo de exemplo deste tutorial](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Localize a seção do modelo que se parece com o exemplo a seguir:

		"cors": {
		    "allowedOrigins": [
		        "todolistangular.azurewebsites.net"
		    ]
		}

## <a id="tutorialstart"></a> Continuando o tutorial de introdução do .NET

Se você estiver seguindo a série de introdução do Node.js ou do Java para aplicativos de API, vá para o próximo artigo, [Autenticação para aplicativos de API do Serviço de Aplicativo](app-service-api-authentication.md).

O restante deste artigo é uma continuação da série de introdução do .NET e supõe que você tenha concluído com êxito [o primeiro tutorial](app-service-api-dotnet-get-started.md).

## Implantar o projeto ToDoListAngular para um novo aplicativo Web

No [primeiro tutorial](app-service-api-dotnet-get-started.md), você criou um aplicativo de API de camada intermediária e um aplicativo de API da camada de dados. Neste tutorial, você criará um aplicativo Web SPA (aplicativo de página única) que chama o aplicativo de API de camada intermediária. Para que o SPA funcione, você terá que habilitar CORS no aplicativo de API de camada intermediária.

No [aplicativo de exemplo ToDoList](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list), o projeto ToDoListAngular é um cliente do AngularJS simples que chama o projeto de API Web ToDoListAPI de camada intermediária. O código JavaScript no arquivo *app/scripts/todoListSvc.js* chama a API usando o provedor HTTP do AngularJS.

		angular.module('todoApp')
		.factory('todoListSvc', ['$http', function ($http) {
		    var apiEndpoint = "http://localhost:46439";
		
		    $http.defaults.useXDomain = true;
		    delete $http.defaults.headers.common['X-Requested-With']; 
		
		    return {
		        getItems : function(){
		            return $http.get(apiEndpoint + '/api/TodoList');
		        },

		        /* Get by ID, Put, and Delete methods not shown */

		        postItem : function(item){
		            return $http.post(apiEndpoint + '/api/TodoList', item);
		        }
		    };
		}]);

### Criar um novo aplicativo Web para o projeto ToDoListAngular

O procedimento para criar um novo aplicativo web e implantá-lo em um projeto é o mesmo que você viu no primeiro tutorial nesta série, exceto que o tipo de aplicativo é **aplicativo Web** em vez de **Aplicativo de API**.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ToDoListAngular e em **Publicar**.

3.  Na guia **Perfil** do assistente **Publicar Web**, clique em **Serviço de Aplicativo do Microsoft Azure**.

5. Na caixa de diálogo **Serviço de Aplicativo**, clique em **Novo**.

3. Na guia **Hospedagem** da caixa de diálogo **Criar Serviço de Aplicativo**, insira um **Nome de Aplicativo Web** que seja exclusivo no domínio *azurewebsites.net*.

5. Escolha a **Assinatura** do Azure com a qual você deseja trabalhar.

6. Na lista suspensa **Grupo de Recursos**, escolha o mesmo grupo de recursos que você criou anteriormente.

4. Na lista suspensa **Plano do Serviço de Aplicativo**, escolha o mesmo plano criado anteriormente.

7. Clique em **Criar**.

	O Visual Studio cria o aplicativo Web, cria um perfil de publicação para ele e exibe a etapa **Conexão** do assistente **Publicar Web**.

	Antes de clicar em **Publicar** no assistente para **Publicar na Web**, você configurará o novo aplicativo Web para chamar o aplicativo de API de camada intermediária que está em execução no Serviço de Aplicativo.

### Definir a URL de camada intermediária nas configurações do aplicativo Web

1. Vá para o [Portal do Azure](https://portal.azure.com/) e navegue até a folha **Aplicativo Web** do aplicativo Web que você criou para hospedar o projeto TodoListAngular (front-end).

2. Clique em **Configurações > Configurações do aplicativo**.

3. Na seção **Configurações do aplicativo**, adicione a seguinte chave e valor:

	|Chave|Valor|Exemplo
	|---|---|---|
	|toDoListAPIURL|https://{your nome de aplicativo de API de tipo médio }.azurewebsites.net|https://todolistapi0121.azurewebsites.net|

4. Clique em **Salvar**.

	Quando o código for executado no Azure, esse valor substituirá a URL de localhost no arquivo Web.config.

	O código que obtém o valor de configuração está em *index.cshtml*:

		<script type="text/javascript">
		    var apiEndpoint = "@System.Configuration.ConfigurationManager.AppSettings["toDoListAPIURL"]";
		</script>
		<script src="app/scripts/todoListSvc.js"></script>

	O código em *todoListSvc.js* usa a configuração:

		return {
		    getItems : function(){
		        return $http.get(apiEndpoint + '/api/TodoList');
		    },
		    getItem : function(id){
		        return $http.get(apiEndpoint + '/api/TodoList/' + id);
		    },
		    postItem : function(item){
		        return $http.post(apiEndpoint + '/api/TodoList', item);
		    },
		    putItem : function(item){
		        return $http.put(apiEndpoint + '/api/TodoList/', item);
		    },
		    deleteItem : function(id){
		        return $http({
		            method: 'DELETE',
		            url: apiEndpoint + '/api/TodoList/' + id
		        });
		    }
		};

### Implantar o projeto da Web ToDoListAngular no novo aplicativo Web

*  No Visual Studio, na etapa **Conexão** do assistente **Publicar na Web**, clique em **Publicar**.

	O Visual Studio implanta o projeto ToDoListAngular no novo aplicativo Web e abre um navegador para a URL do aplicativo Web.

### Testar o aplicativo sem CORS habilitado 

2. Nas Ferramentas de Desenvolvedor do navegador, abra a janela Console.

3. Na janela do navegador que exibe a interface do usuário AngularJS, clique no link **Lista de Tarefas Pendentes**.

	O código JavaScript tenta chamar o aplicativo de API de camada intermediária, mas a chamada falhará porque o front-end está em execução em um domínio diferente (a URL do aplicativo Web) daquele do back-end (a URL do aplicativo de API). A janela Console de Ferramentas de Desenvolvedor do navegador mostra uma mensagem de erro entre origens.

	![](./media/app-service-api-cors-consume-javascript/consoleaccessdenied.png)

## Configure o CORS para o aplicativo de API de camada intermediária

Nesta seção, você configurará o aplicativo de API ToDoListAPI para permitir chamadas de JavaScript do aplicativo Web que você criou para o projeto ToDoListAngular.
 
8. Em um navegador, acesse o [Portal do Azure](https://portal.azure.com/).

2. Clique em **Serviços de Aplicativos** e, em seguida, clique no aplicativo de API ToDoListAPI (camada intermediária).

	![](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. Na folha **Configurações** que aparece à direita da folha **Aplicativo de API**, encontre a seção **API** e, em seguida, clique em **CORS**.

	![](./media/app-service-api-cors-consume-javascript/clicksettings.png)

12. Na caixa de texto, insira a URL do aplicativo Web ToDoListAngular (front-end). Por exemplo, se você tiver implantado o projeto ToDoListAngular para um aplicativo Web chamado todolistangular0121, isso permitirá chamadas da URL `https://todolistangular0121.azurewebsites.net`.

	Como alternativa, você pode inserir um asterisco (*) para especificar que todos os domínios de origem são aceitos.

13. Clique em **Salvar**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

	Depois que você clicar em **Salvar**, o aplicativo de API aceitará chamadas JavaScript da(s) URL(s) especificada(s). Nessa captura de tela, o aplicativo de API ToDoListAPI0223 aceitará chamadas de cliente JavaScript do aplicativo Web ToDoListAngular.

### Testar o aplicativo com CORS habilitado

* Abra um navegador para a URL HTTPS do aplicativo Web. 

	Desta vez, o aplicativo lhe permite exibir, adicionar, editar e excluir itens pendentes.

	![](./media/app-service-api-cors-consume-javascript/corssuccess.png)

## CORS do Serviço de Aplicativo versus CORS da API Web

Em um projeto de API Web, você pode instalar o pacote NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) para especificar no código de quais domínios a API aceitará chamadas JavaScript.
 
O suporte a CORS da API Web é mais flexível do que o suporte a CORS do Serviço de Aplicativo. Por exemplo, você pode especificar no código diferentes origens aceitas para diferentes métodos de ação, enquanto para o CORS do Serviço de Aplicativo, você especifica um conjunto de origens aceitas para todos os métodos do aplicativo de API.

> [AZURE.NOTE] Não tente usar o CORS da API Web e o CORS do Serviço de Aplicativo em um aplicativo de API. O CORS do Serviço de Aplicativo terá a precedência e o CORS da API Web não funcionará. Por exemplo, se você habilitar um domínio de origem no Serviço de Aplicativo e habilitar todos os domínios de origem no código de API Web, o aplicativo de API do Azure aceitará somente chamadas de domínio especificadas no Azure.

### Como habilitar o CORS no código da API Web

As etapas a seguir resumem o processo para habilitar o suporte ao CORS da API Web. Para saber mais, confira [Permitindo solicitações entre origens na API Web ASP.NET 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

1. Em um projeto de API Web, instale o pacote NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/).

1. Inclua uma linha de código `config.EnableCors()` no método **Register** da classe **WebApiConfig**, como no exemplo a seguir.

		public static class WebApiConfig
		{
		    public static void Register(HttpConfiguration config)
		    {
		        // Web API configuration and services
	            
		        // The following line enables you to control CORS by using Web API code
		        config.EnableCors();
	
		        // Web API routes
		        config.MapHttpAttributeRoutes();
	
		        config.Routes.MapHttpRoute(
		            name: "DefaultApi",
		            routeTemplate: "api/{controller}/{id}",
		            defaults: new { id = RouteParameter.Optional }
		        );
		    }
		}

1. No controlador de API Web, adicione uma instrução `using` ao namespace `System.Web.Http.Cors` e adicione o atributo `EnableCors` à classe de controlador ou a métodos de ação individuais. No exemplo a seguir, o suporte a CORS aplica-se ao controlador inteiro.

		namespace ToDoListAPI.Controllers 
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ToDoListController : ApiController
 
	> **Observação**: o uso de curingas para todos os parâmetros com o atributo `EnableCors` serve somente para demonstração e abrirá a API para todas as origens e solicitações HTTP. Use esse atributo com cuidado.

## Próximas etapas 

Neste tutorial, você viu como habilitar o suporte a CORS do Serviço de Aplicativo para que o código JavaScript de cliente possa chamar uma API em um domínio diferente. No próximo artigo da série de introdução a Aplicativos de API, você saberá mais sobre [Autenticação para aplicativos de API do Serviço de Aplicativo](app-service-api-authentication.md).

<!---HONumber=AcomDC_0309_2016-->