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
	ms.date="01/05/2016"
	ms.author="tdykstra"/>

# Consumir um aplicativo de API do JavaScript usando CORS

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Visão geral

Este tutorial mostra como consumir um aplicativo de API usando código JavaScript em um site que é servido por um domínio diferente do aplicativo de API. O cliente de exemplo usa AngularJS.

![](./media/app-service-api-cors-consume-javascript/homepageazure.png)
 
Este é o segundo de uma série de tutoriais sobre como trabalhar com aplicativos de API no Serviço de Aplicativo do Azure. Para ir para o primeiro da série, escolha o primeiro tópico da lista suspensa **Tópico** na parte superior da página.

## Suporte a CORS no Serviço de Aplicativo do Azure

Por motivos de segurança, o comportamento padrão dos navegadores impede que o JavaScript chame APIs para um domínio diferente daquele de onde ele vem. Por exemplo, você pode fazer uma chamada de uma página Web para um ponto de extremidade de API contoso.com, mas não para um ponto de extremidade fabrikam.com. O CORS (Compartilhamento de Recursos Entre Origens) é um protocolo de internet que foi projetado para permitir cenários em que é necessário fazer chamadas de API entre domínios. No Serviço de Aplicativo do Azure, um exemplo desse cenário é o cliente JavaScript estar em execução em um aplicativo Web enquanto a API está em execução em um aplicativo de API.

O Serviço de Aplicativo do Azure oferece uma maneira fácil de configurar os domínios que têm permissão para chamar um aplicativo de API e o recurso CORS funciona da mesma forma para todas as linguagens às quais o serviço de Aplicativos de API oferece suporte, como Java e Node.js.

## Como acompanhar este tutorial

Este tutorial trabalha com um aplicativo de exemplo que você baixa e cria um aplicativo de API para ele no [primeiro tutorial da versão ASP.NET desta série](app-service-api-dotnet-get-started.md).

Se você estiver seguindo os tutoriais de introdução ao Java ou ao Node.js, vá diretamente para a [seção de configuração do CORS](#corsconfig) para obter instruções gerais que se apliquem a todos os aplicativos de API.

## O projeto de exemplo ContactsList.Angular

No [aplicativo de exemplo ContactsList](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list), o projeto ContactsList.Angular é um cliente simples do AngularJS para o projeto de API Web ContactsList.API.

O código JavaScript do AngularJS que chama a API está no arquivo *index.html* no projeto ContactsList.Angular. O código define as funções e adiciona-as ao objeto `$scope`, como mostrado aqui, em que o método Get da API é definido como `$scope.refresh()`.

		angular.module('myApp', []).controller('contactListCtrl', function ($scope, $http) {
		    $scope.baseurl = 'http://localhost:51864';
		
		    $scope.refresh = function () {
		        $scope.status = "Refreshing Contacts...";
		        $http({
		            method: 'GET',
		            url: $scope.baseUrl + '/api/contacts',
		            headers: {
		                'Content-Type': 'application/json'
		            }
		        }).then(function (results) {
		            $scope.contacts = results.data;
		            $scope.status = "Contacts loaded";
		        }, function (err) {
		            $scope.status = "Error loading contacts";
		        });
		    };
		
		    // POST and DELETE not shown
		
		    $scope.refresh();
		});

O código chama o método $scope.refresh() quando a página é carregada (no fim do trecho de código mostrado acima) e é conectado ao botão **Atualizar** na interface do usuário.

		<th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>

## Executar o projeto AngularJS localmente

Nesta seção, você verificará se pode executar o cliente localmente e chamar a API enquanto ela também estiver sendo executada localmente.

**Observação:** essas instruções funcionarão para o Internet Explorer e o Edge porque esses navegadores permitem chamadas JavaScript entre origens de e para URLs `http://localhost`. Se você estiver usando o Chrome, inicie o navegador com a opção `--disable-web-security`. Se você estiver usando o Firefox, ignore esta seção.

1. Defina os projetos ContactsList.API e ContactsList.Angular como projetos de inicialização, com ContactsList.API iniciando antes de ContactsList.Angular. 

2. Pressione F5 para iniciar os projetos.

	A interface do usuário do AngularJS exibe os contatos armazenados localmente; você pode usá-la para adicionar e excluir contatos.

	![](./media/app-service-api-cors-consume-javascript/homepagelocal.png)

3. Feche as janelas do navegador.

## Altere o projeto AngularJS para apontar para o aplicativo de API do Azure 

Em seguida, execute o front-end AngularJS na nuvem e chame o back-end da API que está em execução na nuvem. Antes de implantar o front-end do Azure, você precisa alterar o ponto de extremidade de API no projeto AngularJS para que o código chame o aplicativo de API do Azure que você criou anteriormente.

1. No projeto ContactsList.Angular, abra *index.html*.

2. Marque como comentário a linha que define `baseUrl` como URL localhost, remova a marca de comentário da linha que define `baseUrl` como uma URL de azurewebsites.net e substitua o espaço reservado pelo nome real do aplicativo de API criado anteriormente. Se você nomeou o aplicativo de API ContactsListAPI, o código agora se parece com o trecho a seguir.

		$scope.baseUrl = 'https://ContactsListAPI.azurewebsites.net';
		//$scope.baseUrl = 'http://localhost:51864';

### Implantar o projeto ContactsList.Angular no aplicativo Web

Você pode criar um novo aplicativo Web no qual implantar o projeto AngularJS, mas, para este tutorial, ele será implantado no mesmo aplicativo Web criado no tutorial anterior. O nome do aplicativo Web pode indicar que você implantou um projeto MVC ASP.NET nele anteriormente, mas, após essa implantação, ele executará o código AngularJS.

8. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ContactsList.Angular e em **Publicar**.

9. Clique na guia **Perfil**.

3.  Na etapa **Perfil** do assistente **Publicar Web**, clique em **Serviço de Aplicativo do Microsoft Azure**.

4. Na caixa de diálogo **Serviço de Aplicativo**, escolha sua assinatura.

5. Depois de definir **Exibição** com o valor **Grupo de Recursos** padrão, expanda o grupo de recursos que você criou para esta série de tutoriais.

7. Escolha o aplicativo Web que você criou no primeiro tutorial (não selecione o aplicativo de API) e clique em **OK**.

8. Clique na guia **Configurações**.

9. Expanda **Opções de Publicação do Arquivo** e escolha **Remover arquivos adicionais no destino**.

	![](./media/app-service-api-cors-consume-javascript/removeadditionalfiles.png)

	Normalmente, ao implantar um projeto Web em um aplicativo Web do Serviço de Aplicativo existente, a opção "remover arquivos adicionais" não é interessante porque as alterações são normalmente atualizações ou novos arquivos. Neste caso, você está implantando um projeto diferente no mesmo aplicativo Web; portanto, é possível que muitos arquivos da implantação anterior não sejam necessários na nova.

10. Clique em **Publicar**.

	O Visual Studio implanta o projeto ContactsList.Angular no aplicativo Web e abre um navegador para a URL do aplicativo Web. O navegador mostra a mesma interface do usuário AngularJS UI que você viu em execução localmente, mas agora ela falha porque o front-end está em execução em um domínio diferente (a URL do aplicativo Web) do domínio do back-end (a URL do aplicativo de API).

	![](./media/app-service-api-cors-consume-javascript/corserror.png)

## <a id="corsconfig"></a> Configurar o CORS para o aplicativo de API de destino no Azure

8. Em outra janela do navegador, vá para o [portal do Azure](https://portal.azure.com/).

9. Clique em **Procurar > Aplicativos de API** e escolha o aplicativo de API de destino. Para este tutorial, esse é o aplicativo de API que você criou no primeiro tutorial para o projeto ContactsList.API.

10. Na folha **Aplicativo de API**, clique em **Configurações**.

11. Localize a seção **API** e clique em **CORS**.

12. Na caixa de texto, digite a URL da qual você deseja permitir chamadas. Por exemplo, se você implantou o aplicativo JavaScript para um aplicativo Web chamado ContactsListMVC, digite "http://contactslistmvc.azurewebsites.net".

	Observe que, como alternativa a digitar uma URL, você pode inserir um asterisco (*) para especificar que todos os domínios de origem são aceitos.

13. Clique em **Salvar**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

14. Vá para a janela do navegador que exibe o cliente AngularJS e atualize a página ou clique no botão **Atualizar**.

	A página agora mostra os contatos armazenados no sistema de arquivos do aplicativo de API do Azure.

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

### CORS no Gerenciador de Recursos do Azure

Você também pode configurar o CORS para um aplicativo de API usando ferramentas do Gerenciador de Recursos do Azure, como o Azure PowerShell, a CLI ou o [Gerenciador de Recursos](https://resources.azure.com/).

Defina a propriedade `cors` no tipo de recurso Microsoft.Web/sites/config para o recurso <site name>/web. Por exemplo, no **Gerenciador de Recursos**, vá para **assinaturas > {sua assinatura} > resourceGroups > {seu grupo de recursos} > provedores > Microsoft.Web > sites > {seu site} > configuração > web** e você verá a propriedade cors:

		"cors": {
		    "allowedOrigins": [
		        "contactslistmvc.azurewebsites.net"
		    ]
		}

## Suporte a CORS no código da API Web

Em um projeto de API Web, você pode instalar o pacote NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) que o habilita a especificar no código de quais domínios a API aceitará chamadas JavaScript.
 
O suporte a CORS da API Web é mais flexível do que o suporte a CORS do Serviço de Aplicativo. Por exemplo, você pode especificar no código diferentes origens aceitas para diferentes métodos de ação, enquanto para o CORS do Serviço de Aplicativo, você especifica um conjunto de origens aceitas para todos os métodos do aplicativo de API.

### O CORS do Serviço de Aplicativo tem precedência sobre o CORS da API Web

Não tente usar o CORS da API Web e o CORS do Serviço de Aplicativo em um aplicativo de API. O CORS do Serviço de Aplicativo terá a precedência e o CORS da API Web não funcionará. Por exemplo, se você habilitar um domínio de origem no Serviço de Aplicativo e habilitar todos os domínios de origem no código de API Web, o aplicativo de API do Azure aceitará somente chamadas de domínio especificadas no Azure.

### Como habilitar o CORS no código da API Web

As etapas a seguir resumem o processo para habilitar o suporte ao CORS da API Web. Para saber mais, confira [Permitindo solicitações entre origens na API Web 2 ASP.NET](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

1. Em um projeto de API Web, inclua uma linha de código `config.EnableCors()` no método **Register** de **WebApiConfig**, como no exemplo a seguir. 

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

1. No controlador de API Web, adicione o atributo `EnableCors` à classe `ContactsController` ou métodos de ação individuais. No exemplo a seguir, o suporte a CORS aplica-se ao controlador inteiro.

		namespace ContactList.Controllers
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ContactsController : ApiController
 
	> **Observação**: o uso de curingas para todos os parâmetros com o atributo `EnableCors` serve somente para demonstração e abrirá a API para todas as origens e solicitações HTTP. Use esse atributo com cuidado.

## Próximas etapas 

Neste tutorial, você viu como habilitar o suporte a CORS do Serviço de Aplicativo para que o código cliente JavaScript possa chamar uma API em um domínio diferente. No próximo artigo da série de introdução a Aplicativos de API, você saberá mais sobre [Autenticação para aplicativos de API do Serviço de Aplicativo](app-service-api-authentication.md).

<!---HONumber=AcomDC_0114_2016-->