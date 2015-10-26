<properties 
	pageTitle="Criar um aplicativo de API ASP.NET no Serviço de Aplicativo do Azure usando o Visual Studio 2015" 
	description="Saiba como criar um aplicativo de API ASP.NET no Serviço de Aplicativo do Azure usando o Visual Studio 2015" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/14/2015" 
	ms.author="tdykstra"/>

# Criar um aplicativo de API ASP.NET no Serviço de Aplicativo do Azure usando o Visual Studio 2015

> [AZURE.SELECTOR]
- [Visual Studio 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio 2015 RC](app-service-dotnet-create-api-app-vs2015.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## Visão geral

Neste tutorial, você criará um projeto da API Web ASP.NET 2 usando o [Visual Studio 2015 RC](https://www.visualstudio.com/pt-BR/downloads/visual-studio-2015-downloads-vs.aspx) e o configurará para implantação na nuvem como um [aplicativo de API](app-service-api-apps-why-best-platform.md) no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md). Você também implantará o projeto no Azure. Ao fim do tutorial, você terá um aplicativo de API em execução na nuvem do Azure.

O tutorial pressupõe que você saiba como trabalhar com arquivos e pastas no **Gerenciador de Soluções** do Visual Studio.

O tutorial funciona com a versão lançada atualmente disponível da API Web ASP.NET. Para obter informações sobre como criar um aplicativo de API ASP.NET MVC 6, consulte esta postagem de blog: [https://alexanderzeitler.com/articles/Deploying-a-ASP-NET-MVC-6-API-as-Azure-API-App-in-Azure-App-Services/](https://alexanderzeitler.com/articles/Deploying-a-ASP-NET-MVC-6-API-as-Azure-API-App-in-Azure-App-Services/ "Implantando uma API do ASP.NET MVC 6 como um aplicativo de API do Azure nos Serviços de Aplicativo do Azure").

[AZURE.INCLUDE [install-sdk-2015-only](../../includes/install-sdk-2015-only.md)]

Este tutorial requer a versão 2.6 ou posterior do SDK do Azure para .NET.

## Criar um pacote de aplicativos de API 

O Visual Studio 2015 RC ainda não tem um modelo de projeto de aplicativo da API. Portanto, para criar um projeto de aplicativo da API, você começa com o modelo de projeto de API Web.

1. Abra o Visual Studio 2015 RC.

2. Clique em **Arquivo > Novo Projeto**.

3. Em **Modelos**, clique em **Web** e clique no modelo **Aplicativo Web ASP.NET**.

4. Nomeie o projeto *ContactsList*

5. Verifique se a caixa de seleção **Adicionar o Application Insights ao Projeto** está desmarcada.

5. Clique em **OK**.

	![](./media/app-service-dotnet-create-api-app-vs2015/newproj.png)

6. Na caixa de diálogo **Novo Projeto ASP.NET** em **Modelos ASP.NET 4.6**, selecione o modelo de projeto **Vazio**.

7. Marque a caixa de seleção **API Web**.

8. Desmarque a caixa de seleção **Host na nuvem**.

7. Clique em **OK**.

	![](./media/app-service-dotnet-create-api-app-vs2015/newaspnet.png)

## Adicionar pacotes NuGet

O tempo de execução do Serviço de Aplicativo para aplicativos de API é fornecido pelo pacote do NuGet [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/), e a geração de metadados da API [Swagger](http://swagger.io/ "Informações oficiais do Swagger") dinâmica é fornecida pelo pacote do NuGet [Swashbuckle](http://www.nuget.org/packages/Swashbuckle/).

> **Observação:** quando você instala o pacote Swashbuckle, uma página de teste de API é habilitada por padrão. Se você publicar o aplicativo de API e definir seu acesso como **Público (anônimo)**, qualquer pessoa que encontre a URL da página de teste poderá usá-la para chamar a API. Você usará a página de teste posteriormente neste tutorial.

1. Clique em **Ferramentas > Gerenciador de Pacotes do NuGet > Console do Gerenciador de Pacotes**.

2. No **Console do gerenciador de pacotes** (PMC), digite os comandos a seguir.

		install-package Microsoft.Azure.AppService.ApiApps.Service
		install-package Swashbuckle

	Talvez seja necessário aguardar alguns minutos após o PMC exibir a mensagem indicando que está verificando dependências.

## Adicionar arquivos de metadados de aplicativo de API

Os metadados que habilitam um projeto de API Web a ser implantado como um aplicativo de API estão contidos em um arquivo *apiapp.json* e uma pasta *Metadados* com suas subpastas e arquivos. Nas etapas a seguir, você adicionará esses arquivos com valores padrão.

A seção [Metadados de aplicativo de API](#api-app-metadata) posteriormente no tutorial explica como personalizar esses metadados.

1. Na pasta do projeto, crie um arquivo *.JSON* denominado apiapp.json e substitua o conteúdo do novo arquivo pelo texto JSON a seguir.

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "ContactsList",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "ContactsListTitle",
		    "summary": "Summary",
		    "author": "Author",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    }
		}

3. Na pasta do projeto, crie uma pasta chamada *Metadados* e, na pasta *Metadados*, crie uma pasta chamada *deploymentTemplates*.

5. Na pasta *deploymentTemplates*, crie um arquivo *.JSON* chamado *apiappconfig.azureresource.json* e substitua o conteúdo do novo arquivo pelo texto JSON a seguir.

		{
		  "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "$system": {
		      "type": "Object"
		    }
		  },
		  "resources": []
		}

## Adicionar código API Web

Nas etapas a seguir, você adiciona código para um método HTTP Get simples que retorna uma lista de contatos embutida em código.

1. Criar uma pasta *Modelos* na pasta do projeto, se ela ainda não existir.

2. Na pasta *Modelos*, adicione um arquivo de classe chamado *Contact.cs*, e substitua o conteúdo do arquivo pelo seguinte código: namespace ContactsList.Models { public class Contact { public int Id { get; set; } public string Name { get; set; } public string EmailAddress { get; set; } } }

5. Clique com o botão direito do mouse na pasta **Controladores** e selecione **Adicionar > Controlador**.

	![](./media/app-service-dotnet-create-api-app-vs2015/05-new-controller-v3.png)

6. Na caixa de diálogo **Adicionar Scaffold**, selecione a opção **Controlador de API Web 2 - Vazio** e clique em **Adicionar**.

	![](./media/app-service-dotnet-create-api-app-vs2015/06-new-controller-dialog-v3.png)

7. Nomeie o controlador **ContactsController** e clique em **Adicionar**.

	![](./media/app-service-dotnet-create-api-app-vs2015/07-new-controller-name-v2.png)

8. Depois que o arquivo *ContactsController.cs* for criado, substitua o conteúdo do arquivo pelo código a seguir.

		using ContactsList.Models;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace ContactsList.Controllers
		{
		    public class ContactsController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Contact> Get()
		        {
		            return new Contact[]{
						new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
						new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
	                	new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
		            };
		        }
		    }
		}

## Testar a API Web

Para exibir a página de teste da API, execute as etapas a seguir.

1. Execute o aplicativo localmente (CTRL-F5) e adicione `/swagger` ao final da URL na barra de endereços do navegador. 

	![](./media/app-service-dotnet-create-api-app-vs2015/14-swagger-ui.png)

2. Clique em **Contatos > Obter > Experimentar** e você verá que a API está funcionando e retorna o resultado esperado.

	![](./media/app-service-dotnet-create-api-app-vs2015/15-swagger-ui-post-test.png)

## Criar um aplicativo de API no Azure

1. Crie um aplicativo de API no [portal de visualização do Azure](https://portal.azure.com). 

	* Clique em **Novo > Web + Móvel > Aplicativo de API**.

		![](./media/app-service-dotnet-create-api-app-vs2015/createapiapp1.png)

	* Em **Nome**, digite ContactsList.

	* Em **Plano do Serviço de Aplicativo**, clique em **Criar Novo** e digite um nome, por exemplo: **ContactsList**.

		Para obter mais informações sobre os planos do Serviço de Aplicativo, consulte [Visão geral dos planos de Serviço de Aplicativo do Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md).

	* Clique em **Camada de preços > Exibir tudo > Gratuito > Selecionar** para selecionar a camada de preço gratuita.

		Você pode usar uma camada de preços paga, mas não é necessário para este tutorial.

	* Em **Grupo de recursos**, clique em **Criar novo** e digite um nome, por exemplo: ContactsList.

		Para obter mais informações sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](resource-group-overview.md).

	* Se você tiver mais de uma assinatura, selecione a assinatura que deseja usar.

	* Escolha um local perto de você.

	* Clique em **Criar**.

		![](./media/app-service-dotnet-create-api-app-vs2015/createapiapp2.png)

2. Quando o Azure concluir a criação do aplicativo de API, defina o nível de acesso do aplicativo de API como **Público (anônimo)**.

	* Clique em **Procurar > Grupos de recursos > [o grupo de recursos que você criou] > [o aplicativo de API que você criou]**.

	* Clique em **Configurações > Configurações do aplicativo**.

	* Altere o **Nível de Acesso** para **Público (anônimo)**.
	 
	* Clique em **Salvar**.

		![](./media/app-service-dotnet-create-api-app-vs2015/setpublicanon.png)
	
2. Anote o nome do aplicativo da Web subjacente que hospeda o aplicativo de API. Você usará isso ao implantar o projeto do Visual Studio.

	* Em **Host de aplicativo de API**, clique em **ContactsList**.

		![](./media/app-service-dotnet-create-api-app-vs2015/clickapiapphost.png)

	* O nome está no título da folha **Host de aplicativo de API**.

		![](./media/app-service-dotnet-create-api-app-vs2015/apiapphostname.png)

## Implantar o projeto de API Web para o novo aplicativo de API no Azure
 
Os aplicativos de API são, essencialmente, os aplicativos Web para os quais o Azure fornece recursos adicionais para a funcionalidade do serviço Web. No Visual Studio 2015 RC, você publica no aplicativo Web subjacente do aplicativo de API porque o assistente de Publicação na Web não tem uma seleção especificamente para aplicativos de API.

2. No **Gerenciador de Soluções** do Visual Studio, clique com o botão direito do mouse no projeto e, em seguida, no menu de contexto, clique em **Publicar**.

3. Na etapa **Perfil** do assistente de **Publicação na Web**, clique em **Aplicativos Web do Microsoft Azure**.

	![](./media/app-service-dotnet-create-api-app-vs2015/pubwebselwebapp.png)

4. Na lista suspensa **Aplicativos Web existentes**, selecione a entrada com o nome de aplicativo de API que você anotou anteriormente.

	![](./media/app-service-dotnet-create-api-app-vs2015/pubwebselapiapp.png)

5. Clique em **Publicar**.

	O navegador é aberto na URL do aplicativo Web e mostra uma página indicando que o "aplicativo API foi criado".

6. Na barra de endereços do navegador, adicione "swagger/" ao fim da URL , por exemplo:

		https://microsoft-apiappb001b62a9033493a33748332233fca2.azurewebsites.net/swagger/

	Você verá a mesma interface do usuário do Swagger que viu anteriormente em execução localmente, mas, agora, ela está em execução na nuvem.

2. Clique em **Contatos > Obter > Experimentar** e você verá que a API está funcionando e retorna o resultado esperado.

	![](./media/app-service-dotnet-create-api-app-vs2015/runninginazure.png)

## Exibir a definição de API no portal de visualização do Azure

Nesta seção, navegue para o portal para exibir a definição de API para o aplicativo de API que você acabou de criar.

1. No [Portal de visualização do Azure](https://portal.azure.com), navegue até a folha **Aplicativo de API** para seu aplicativo de API: clique em **Procurar > Grupos de recursos > [o grupo de recursos criado] > [o aplicativo de API criado]**.

4. Clique em **Definição de API**.

	A folha **Definição de API** do aplicativo mostra a lista de operações de API que você definiu quando criou o aplicativo. (Se você seguiu este tutorial, você verá apenas uma operação Get).

	![Definição da API](./media/app-service-dotnet-create-api-app-vs2015/29-api-definition-v3.png)

## Adicionar uma operação ao código de API da Web

5. Volte para o projeto no Visual Studio e adicione o seguinte código ao arquivo **ContactsController.cs**. Este código adiciona um método **Post** que pode ser usado para publicar novas `Contact` instâncias da API.  

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	![Adicionando o método Post ao controlador](./media/app-service-dotnet-create-api-app-vs2015/30-post-method-added-v3.png)

6. Publique o projeto como anteriormente. (No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto, clique em **Publicar**, e, em seguida, clique em **Publicar** no assistente **Publicar Web**.)

12. Quando o processo de publicação for concluído, vá para o portal e feche e reabra o gateway como feito anteriormente.

14. No portal, volte para a lâmina **Definição de API**.

	Você verá o novo ponto de extremidade da API que acabou de criar e implantar diretamente na sua assinatura do Azure.

	![Definição da API](./media/app-service-dotnet-create-api-app-vs2015/38-portal-with-post-method-v4.png)

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Próximas etapas

Você criou e implantou um aplicativo de API usando o Visual Studio 2015 RC. Para obter mais documentação sobre aplicativos de API, consulte as entradas no painel de navegação mostrado no lado esquerdo da página (para janelas de navegador amplas) ou na parte superior da página (para janelas de navegador estreitas). A maior parte da documentação de aplicativos de API atualmente mostra o Visual Studio 2013, mas grande parte dela pode ser usada com o VS 2015 porque a interface do usuário é semelhante, o código que você escreve é o mesmo e a interface do usuário do portal é a mesma.
 

<!---HONumber=Oct15_HO3-->