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
	ms.date="11/27/2015"
	ms.author="tdykstra"/>

# Consumir um aplicativo de API do JavaScript usando CORS

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Visão geral

Este tutorial mostra como consumir um aplicativo de API usando código JavaScript em um site que é servido por um domínio diferente do aplicativo de API. O cliente de exemplo usa AngularJS.

![](./media/app-service-api-cors-consume-javascript/homepageazure.png)
 
Este é o segundo de uma série de tutoriais que mostram como trabalhar com aplicativos de API no Serviço de Aplicativo do Azure. Para saber mais sobre a série, confira [Introdução aos Aplicativos de API e ao ASP.NET no Serviço de Aplicativo do Azure](app-service-api-dotnet-get-started.md).

## Introdução ao CORS

Por motivos de segurança, o comportamento padrão dos navegadores impede que o JavaScript chame APIs para um domínio diferente daquele de onde ele vem. Por exemplo, você pode fazer uma chamada de uma página Web para um ponto de extremidade de API contoso.com, mas não para um ponto de extremidade fabrikam.com. O CORS (Compartilhamento de Recursos Entre Origens) é um protocolo de internet que foi projetado para permitir cenários em que é necessário fazer chamadas de API entre domínios. No Serviço de Aplicativo do Azure, um exemplo desse cenário é o cliente JavaScript estar em execução em um aplicativo Web enquanto a API está em execução em um aplicativo de API.

Em um projeto de API Web, você pode instalar pacotes NuGet CORS que permitem a especificação, no código, dos domínios dos quais a API aceitará chamadas JavaScript. Como alternativa, você pode usar o recurso CORS incorporado no Serviço de Aplicativo do Azure para especificar de quais domínios o aplicativo de API aceitará chamadas. A primeira metade deste tutorial mostra como usar o recurso do Azure, que funciona em todos os idiomas com suporte do serviço dos Aplicativos de API. A segunda metade é opcional e mostra o método do pacote NuGet que funciona com a API Web ASP.NET.

## O projeto de exemplo ContactsList.Angular

Neste tutorial, você usa os projetos de exemplo que baixou no primeiro tutorial desta série e os recursos do Azure (aplicativo de API e aplicativo Web) que criou no primeiro tutorial.

O projeto ContactsList.Angular é um cliente simples do AngularJS para o projeto de API Web ContactsList.API. O código JavaScript do AngularJS que chama a API está no arquivo *index.html* no projeto ContactsList.Angular. O código define as funções e adiciona-as ao objeto `$scope`, como mostrado aqui, em que o método Get da API é definido como `$scope.refresh()`.

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

O código chama o método $scope.refresh() quando a página carrega (no final do trecho de código mostrado acima) e é conectado ao botão **Atualizar** na interface do usuário.

		<th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>

## Executar o projeto AngularJS localmente

Nesta seção, você verificará se pode executar o cliente localmente e chamar a API enquanto ela também estiver sendo executada localmente.

1. Defina os projetos ContactsList.API e ContactsList.Angular como projetos de inicialização, com ContactsList.API iniciando antes de ContactsList.Angular. 

2. Pressione F5 para iniciar os projetos.

	A interface do usuário do AngularJS exibe os contatos armazenados localmente; você pode usá-la para adicionar e excluir contatos.

	![](./media/app-service-api-cors-consume-javascript/homepagelocal.png)

3. Feche as janelas do navegador.

## Altere o projeto AngularJS para apontar para o aplicativo de API do Azure 

Em seguida, execute o front-end AngularJS na nuvem e chame o back-end da API que está em execução na nuvem. Antes de implantar o front-end do Azure, você precisa alterar o ponto de extremidade de API no projeto AngularJS para que o código chame o aplicativo de API do Azure que você criou anteriormente.

1. No projeto ContactsList.Angular, abra *index.html*.

2. Comente a linha que define `baseUrl` como URL localhost, remova a linha que define `baseUrl` como uma URL de azurewebsites.net e substitua o espaço reservado pelo nome real do aplicativo de API criado anteriormente. Se você nomeou o aplicativo de API ContactsListAPI, o código agora se parece com o trecho a seguir.

		$scope.baseUrl = 'https://ContactsListAPI.azurewebsites.net';
		//$scope.baseUrl = 'http://localhost:51864';

### Implantar o projeto ContactsList.Angular no aplicativo Web

Você pode criar um novo aplicativo Web no qual implantar o projeto AngularJS, mas, para este tutorial, ele será implantado no mesmo aplicativo Web criado anteriormente. O nome do aplicativo Web pode indicar que você implantou um projeto MVC ASP.NET nele anteriormente, mas, após essa implantação, ele executará o código AngularJS.

8. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ContactsList.Angular e clique em **Publicar**.

9. Clique na guia **Perfil**.

3.  Na etapa **Perfil** do assistente **Publicar Web**, clique em **Serviço de Aplicativo do Microsoft Azure**.

4. Na caixa de diálogo **Serviço de Aplicativo**, escolha sua assinatura.

5. Depois de definir **Exibição** com o valor **Grupo de Recursos** padrão, expanda o grupo de recursos que você criou para esta série de tutoriais.

7. Selecione o aplicativo Web que você criou no primeiro tutorial (não selecione o aplicativo de API) e clique em **OK**.

8. Clique na guia **Configurações**.

9. Expanda **Opções de Publicação do Arquivo** e selecione **Remover arquivos adicionais no destino**.

	![](./media/app-service-api-cors-consume-javascript/removeadditionalfiles.png)

	Normalmente, ao implantar um projeto Web em um aplicativo Web do Serviço de Aplicativo existente, a opção "remover arquivos adicionais" não é interessante porque as alterações são normalmente atualizações ou novos arquivos. Neste caso, você está implantando um projeto diferente no mesmo aplicativo Web; portanto, é possível que muitos arquivos da implantação anterior não sejam necessários na nova.

10. Clique em **Publicar**.

	O Visual Studio implanta o projeto ContactsList.Angular no aplicativo Web e abre um navegador para a URL do aplicativo Web. O navegador mostra a mesma interface do usuário AngularJS UI que você viu em execução localmente, mas agora ela falha porque o front-end está em execução em um domínio diferente (a URL do aplicativo Web) do domínio do back-end (a URL do aplicativo de API).

	![](./media/app-service-api-cors-consume-javascript/corserror.png)

## Configurar o CORS para o aplicativo de API de destino no Azure

8. Em outra janela do navegador, vá para o [portal do Azure](https://portal.azure.com/).

9. Navegue até a folha de aplicativo de API do aplicativo de API que você criou no primeiro tutorial.

10. Clique em **Configurações**.

11. Encontre a seção **API** e clique em **CORS**.

12. Na caixa de texto, insira a URL do aplicativo Web que você criou no primeiro tutorial para o front-end MVC ASP.NET. Por exemplo, se você nomeou o aplicativo Web ContactsListMVC, digite "http://contactslistmvc.azurewebsites.net".

	Observe que, como alternativa a digitar uma URL, você pode inserir um asterisco (*) para especificar que todos os domínios de origem são aceitos.

13. Clique em **Salvar**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

14. Volte para a janela do navegador que exibe o cliente AngularJS em execução no aplicativo Web do Azure e atualize a página ou clique no botão **Atualizar**.

	A página agora mostra os contatos armazenados no sistema de arquivos do aplicativo de API do Azure.

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

## Configurar o CORS para o aplicativo de API de destino no código API Web

Se você não quiser usar o suporte de CORS do Serviço de Aplicativo do Azure, uma alternativa é habilitar CORS em seu código de API Web ASP.NET. Esse processo está documentado com profundidade em [Habilitando solicitações entre origens na API Web ASP.NET 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api). Para aplicativos de API construídos usando a API Web do ASP.NET, o processo é exatamente o mesmo, mas será resumido aqui.

Nesta seção, você irá desabilitar o suporte a CORS do Serviço de Aplicativo do Azure e habilitar o suporte a CORS da API Web. Observe que desabilitar o suporte a CORS do Azure não é uma etapa opcional antes de ativar o suporte da API Web. Se você usar ambos os métodos ao mesmo tempo, o CORS do Azure terá precedência e CORS da API Web não funcionará. Por exemplo, se você habilitar um domínio de origem no Azure e habilitar todos os domínios de origem no código de API Web, o aplicativo de API do Azure aceitará somente chamadas de domínio especificado no Azure.

### Desabilitar o suporte a CORS do Azure

1. Para desabilitar o suporte a CORS do Azure, volte ao portal do Azure, limpe a URL que você inseriu na folha CORS e clique em **Salvar**.
 
3.  Volte para a URL do aplicativo Web em que você implantou o aplicativo AngularJS e atualize a página ou clique no botão **Atualizar**.

	Você verá "Erro ao carregar contatos" novamente.

	![](./media/app-service-api-cors-consume-javascript/corserror.png)

### Habilitar suporte a CORS da API Web

A funcionalidade CORS para API Web é fornecida pelo pacote do NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/). O pacote já está instalado no aplicativo de exemplo baixado e tudo o que você precisa fazer para habilitar o CORS é remover os comentários de alguns códigos.

1. No projeto ContactsList.API, abra o arquivo *App\_Start/WebApiConfig.cs*. Remova a linha de código config.EnableCors do método **Register** em **WebApiConfig**. 

	Depois que o arquivo é atualizado, o código deve se parecer com o seguinte:

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
		        // Uncomment the following line to control CORS by using Web API code
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

1. Abra o arquivo *Controllers/ContactsController.cs* e remova a linha que adiciona o atributo `EnableCors` à classe `ContactsController`.

		namespace ContactList.Controllers
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ContactsController : ApiController
 
	Você pode aplicar o atributo a métodos de ação individuais em vez de a todo o controlador, se preferir.

	> **Observação**: o uso de curingas para todos os parâmetros com o atributo `EnableCors` serve somente para demonstração e abrirá a sua API para todas as origens e solicitações HTTP. Use esse atributo com cuidado.

### Implantar a API do Azure e testar o front-end novamente

8. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ContactsList.API e clique em **Publicar**.

	O assistente **Publicar Web** abre automaticamente na etapa **Visualização** do perfil de publicação para o aplicativo de API que você criou anteriormente, já que é o último destino para o qual você implantou esse projeto.

3.  Clique em **Publicar**.

	![](./media/app-service-api-cors-consume-javascript/pubapi.png)

	Depois que a implantação for concluída, o navegador abrirá uma janela para a URL de aplicativo de API que exibirá uma página de "aplicativo Web criado com êxito".

3.  Volte para a URL do aplicativo Web em que você implantou o aplicativo AngularJS e atualize a página ou clique no botão **Atualizar**.

	A página é carregada com êxito novamente.

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

## Habilitar novamente o suporte a CORS do Azure

Para usar os serviços internos de autenticação do Azure nos tutoriais a seguir, você precisará usar o CORS do Azure em vez do CORS da API Web.
 
1. Para habilitar o suporte a CORS do Azure, volte à folha CORS no portal do Azure para seu aplicativo de API e insira novamente a URL do aplicativo Web.

13. Clique em **Salvar**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

## Próximas etapas 

Neste tutorial, você viu duas maneiras de habilitar o suporte a CORS para que o código cliente JavaScript possa chamar uma API em um domínio diferente. No próximo tutorial, você aprenderá como [restringir o acesso ao aplicativo de API para que somente usuários autenticados possam acessá-lo](app-service-api-dotnet-user-principal-auth.md).

<!---HONumber=AcomDC_1203_2015-->