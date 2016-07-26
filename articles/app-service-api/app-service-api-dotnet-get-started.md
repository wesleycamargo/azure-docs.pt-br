<properties
	pageTitle="Introdução aos aplicativos de API e ao ASP.NET no Serviço de Aplicativos | Microsoft Azure"
	description="Saiba como criar, implantar e consumir um aplicativo de API do ASP.NET no Serviço de Aplicativo do Azure usando o Visual Studio 2015."
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
	ms.date="05/27/2016"
	ms.author="rachelap"/>

# Introdução aos aplicativos de API, ASP.NET e Swagger no Serviço de Aplicativo do Azure

[AZURE.INCLUDE [seletor](../../includes/app-service-api-get-started-selector.md)]

Este é o primeiro de uma série de tutoriais que mostram como usar recursos do Serviço de Aplicativo do Azure que são úteis para desenvolver e hospedar APIs RESTful: Este tutorial aborda o suporte para metadados de API no formato Swagger.

O que você aprenderá:

* Como criar e implantar [aplicativos de API](app-service-api-apps-why-best-platform.md) no Serviço de Aplicativo do Azure usando ferramentas internas do Visual Studio 2015.
* Como automatizar a detecção de API usando o pacote NuGet do Swashbuckle para gerar dinamicamente os metadados de API do Swagger.
* Como usar metadados de API do Swagger para gerar automaticamente o código do cliente para um aplicativo de API.

## Visão geral do aplicativo de exemplo

Neste tutorial, você trabalha com um aplicativo de exemplo de lista de tarefas simples. O aplicativo tem um front-end SPA (aplicativo de página única), uma camada intermediária de API Web do ASP.NET e uma camada de dados de API Web do ASP.NET.

![Diagrama do aplicativo de exemplo de Aplicativos de API](./media/app-service-api-dotnet-get-started/noauthdiagram.png)

Aqui está uma captura de tela do front-end [AngularJS](https://angularjs.org/).

![Lista de tarefas pendentes do aplicativo de exemplo de Aplicativos de API](./media/app-service-api-dotnet-get-started/todospa.png)

A solução do Visual Studio inclui três projetos:

![](./media/app-service-api-dotnet-get-started/projectsinse.png)

* **ToDoListAngular** ‒ o front-end: um SPA do AngularJS que chama a camada intermediária.

* **ToDoListAPI** ‒ a camada intermediária: um projeto de API Web ASP.NET que chama a camada de dados para executar operações CRUD em itens pendentes.

* **ToDoListDataAPI** ‒ a camada de dados: um projeto de API Web ASP.NET que executa operações CRUD nos itens pendentes.

A arquitetura de três camadas é uma das várias arquiteturas que você pode implementar usando aplicativos de API e é usada aqui apenas para fins de demonstração. O código em cada camada é tão simples quanto possível para demonstrar os recursos de aplicativos de API. Por exemplo, a camada de dados usa memória de servidor em vez de um banco de dados como seu mecanismo de persistência.

Ao concluir este tutorial, você terá dois projetos de API Web em execução na nuvem em aplicativos de API do Serviço de Aplicativo.

O próximo tutorial na série implanta o front-end do SPA na nuvem.

## Pré-requisitos

* API Web do ASP.NET ‒ as instruções do tutorial pressupõem que você tenha um conhecimento básico de como trabalhar com a [API Web 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) do ASP.NET no Visual Studio.

* Conta do Azure ‒ você pode [Abrir uma conta do Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F) ou [Ativar benefícios de assinante do Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

	Se você quiser ter uma introdução ao Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751). Lá, você poderá criar imediatamente um aplicativo de curta duração inicial no Serviço de Aplicativo — sem exigência de cartão de crédito e sem compromissos.

* Visual Studio 2015 com o [SDK do Azure para .NET](http://go.microsoft.com/fwlink/?linkid=518003) ‒ o SDK instalará automaticamente o Visual Studio 2015 se você ainda não o tiver.

	>[AZURE.NOTE] Dependendo de quantas dependências de SDK você já tiver no seu computador, a instalação do SDK pode demorar bastante, de vários minutos a meia hora ou mais.

## Baixar o aplicativo de exemplo 

1. Baixe o repositório [Azure-Samples/app-service-api-dotnet-to-do-list](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list).

	Você pode clicar no botão **Baixar ZIP** ou clonar o repositório no computador local.

2. Abra a solução ToDoList no Visual Studio 2015 ou 2013.

2. Compile o projeto para restaurar os pacotes NuGet.

	Se quiser ver o aplicativo em operação antes de implantá-lo, você poderá executá-lo localmente. Apenas verifique se todos os três projetos são projetos de inicialização. Você precisará usar o Internet Explorer ou o Edge, pois esses navegadores permitem chamadas de JavaScript entre origens para URLs `http://localhost`.

## Usar a interface do usuário e metadados de API do Swagger

O suporte a metadados da API 2.0 do [Swagger](http://swagger.io/) é integrado ao Serviço de Aplicativo do Azure. Cada aplicativo de API pode especificar um ponto de extremidade de URL que retorna metadados para a API no formato JSON Swagger. Os metadados retornados do ponto de extremidade podem ser usados para gerar código de cliente.

Um projeto de API Web ASP.NET pode gerar dinamicamente metadados do Swagger usando o pacote NuGet [Swashbuckle](https://www.nuget.org/packages/Swashbuckle). O pacote NuGet Swashbuckle já está instalado nos projetos ToDoListDataAPI e ToDoListAPI que você baixou.

Nesta seção do tutorial, você verá os metadados do Swagger 2.0 gerados e experimentará uma interface do usuário de teste baseada nos metadados do Swagger.

2. Defina o projeto ToDoListDataAPI (**não** o projeto ToDoListAPI) como o projeto de inicialização.
 
4. Pressione F5 ou clique em **Depurar > Iniciar Depuração** para executar o projeto no modo de depuração.

	O navegador é aberto e mostra a página de erro HTTP 403.

12. Na barra de endereços do navegador, adicione `swagger/docs/v1` no fim da linha e pressione Return. (A URL é `http://localhost:45914/swagger/docs/v1`.)

	Essa é a URL padrão usada pelo Swashbuckle para retornar metadados JSON do Swagger 2.0 para a API.

	Se você estiver usando o Internet Explorer, o navegador solicitará que baixe um arquivo *v1.json*.

	![Baixar metadados JSON no IE](./media/app-service-api-dotnet-get-started/iev1json.png)

	Se você estiver usando o Chrome, o Firefox ou o Edge, o navegador exibirá o JSON em sua janela. Navegadores diferentes lidam com o JSON de maneira diferente, e a janela do navegador pode não ser exatamente como o exemplo.

	![Metadados JSON no Chrome](./media/app-service-api-dotnet-get-started/chromev1json.png)

	O exemplo a seguir mostra a primeira seção dos metadados do Swagger para a API, com a definição para o método Get. Esses metadados geram a interface do usuário do Swagger, que você usará nas etapas a seguir e em uma seção posterior do tutorial para gerar o código cliente automaticamente.

		{
		  "swagger": "2.0",
		  "info": {
		    "version": "v1",
		    "title": "ToDoListDataAPI"
		  },
		  "host": "localhost:45914",
		  "schemes": [ "http" ],
		  "paths": {
		    "/api/ToDoList": {
		      "get": {
		        "tags": [ "ToDoList" ],
		        "operationId": "ToDoList_GetByOwner",
		        "consumes": [ ],
		        "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
		        "parameters": [
		          {
		            "name": "owner",
		            "in": "query",
		            "required": true,
		            "type": "string"
		          }
		        ],
		        "responses": {
		          "200": {
		            "description": "OK",
		            "schema": {
		              "type": "array",
		              "items": { "$ref": "#/definitions/ToDoItem" }
		            }
		          }
		        },
		        "deprecated": false
		      },

1. Feche o navegador e interrompa a depuração do Visual Studio.

3. No projeto ToDoListDataAPI no **Gerenciador de Soluções**, abra o arquivo *App\_Start\\SwaggerConfig.cs*, role para baixo até o código a seguir e remova as marcas de comentário dele.

		/*
		    })
		.EnableSwaggerUi(c =>
		    {
		*/

	O arquivo *SwaggerConfig.cs* é criado quando você instala o pacote do Swashbuckle em um projeto. O arquivo fornece várias maneiras de se configurar o Swashbuckle.

	O código que você removeu habilita a interface do usuário do Swagger que será usada nas etapas a seguir. Quando você cria um projeto de API Web usando o modelo de projeto de aplicativo de API, o código é comentado por padrão como medida de segurança.

5. Execute o projeto novamente.

3. Na barra de endereços do navegador, adicione `swagger` no fim da linha e pressione Return. (A URL é `http://localhost:45914/swagger`.)

4. Quando for exibida a página de interface do usuário do Swagger, clique em **ToDoList** para ver os métodos disponíveis.

	![Métodos disponíveis na interface do usuário do Swagger](./media/app-service-api-dotnet-get-started/methods.png)

5. Clique no primeiro botão **Obter** na lista.

6. Na seção **Parâmetros**, digite um asterisco como o valor do parâmetro `owner` e clique em **Experimentar**.

	Quando você adicionar a autenticação nos tutoriais posteriores, a camada intermediária fornecerá a ID de usuário real para a camada de dados. Por enquanto, todas as tarefas terão um asterisco como sua ID de proprietário, enquanto o aplicativo é executado sem autenticação habilitada.

	![Teste da interface do usuário do Swagger](./media/app-service-api-dotnet-get-started/gettryitout1.png)

	A interface do usuário do Swagger chama o método Get ToDoList e exibe o código de resposta e os resultados JSON.

	![Resultados do teste da interface do usuário do Swagger](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. Clique em **Postar** e marque a caixa em **Esquema de Modelo**.

	Clicar no esquema de modelo preencherá previamente a caixa de entrada onde é possível especificar o valor do parâmetro para o método Post. (Se isso não funcionar no Internet Explorer, use um navegador diferente ou insira o valor do parâmetro manualmente na próxima etapa).

	![Postagem do teste da interface do usuário do Swagger](./media/app-service-api-dotnet-get-started/post.png)

7. Altere o JSON na caixa de entrada do parâmetro `todo` para que ele seja semelhante ao seguinte exemplo ou substitua-o por seu próprio texto de descrição:

		{
		  "ID": 2,
		  "Description": "buy the dog a toy",
		  "Owner": "*"
		}

10. Clique em **Experimentar**.

	A API de ToDoList retorna um código de resposta HTTP 204 que indica êxito.

11. Clique no primeiro botão **Obter** e, nessa seção da página, clique no botão **Experimentar**.

	A resposta do método Get agora inclui o novo item pendente.

12. Opcional: Também experimente os métodos Put, Delete e Get by ID.

14. Feche o navegador e interrompa a depuração do Visual Studio.

O Swashbuckle funciona com qualquer projeto de API Web ASP.NET. Se você deseja adicionar a geração de metadados de Swagger a um projeto existente, instale o pacote Swashbuckle.

**Observação:** os metadados do Swagger incluem uma ID exclusiva para cada operação da API. Por padrão, o Swashbuckle pode gerar IDs de operação do Swagger duplicadas para os métodos do controlador da API Web. Isso acontecerá se o controlador tiver métodos HTTP sobrecarregados, como `Get()` e `Get(id)`. Para obter informações sobre como lidar com sobrecargas, consulte [Personalizar definições de API geradas pelo Swashbuckle](app-service-api-dotnet-swashbuckle-customize.md). Se você criar um projeto de API Web no Visual Studio usando o modelo de Aplicativo de API do Azure, o código que gera IDs de operação exclusivas será adicionado automaticamente ao arquivo *SwaggerConfig.cs*.

## <a id="createapiapp"></a> Criar um aplicativo de API no Azure e implantar código nele

Nesta seção, você usará as ferramentas do Azure integradas no assistente **Publicar na Web** do Visual Studio para criar um novo aplicativo de API no Azure. Em seguida, implante o projeto ToDoListDataAPI no novo aplicativo de API e chame a API executando a interface do usuário do Swagger.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ToDoListDataAPI e clique em **Publicar**.

	![Clicar em Publicar no Visual Studio](./media/app-service-api-dotnet-get-started/pubinmenu.png)

3.  Na etapa **Perfil** do assistente **Publicar Web**, clique em **Serviço de Aplicativo do Microsoft Azure**.

	![Clicar no Serviço de Aplicativo do Azure em Publicar Web](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. Entre em sua conta do Azure, se ainda não tiver feito isso, ou atualize suas credenciais se elas tiverem expirado.

4. Na caixa de diálogo Serviço de Aplicativo, escolha a **Assinatura** do Azure que você deseja usar e clique em **Novo**.

	![Clicar em Novo no diálogo Serviço de Aplicativo](./media/app-service-api-dotnet-get-started/clicknew.png)

	A guia **Hospedagem** da caixa de diálogo **Criar Serviço de Aplicativo** será exibida.

	Como você está implantando um projeto de API Web com o Swashbuckle instalado, o Visual Studio pressupõe que você queira criar Aplicativo de API. Isso é indicado pelo título **Nome do Aplicativo de API** e pelo fato de a lista suspensa **Alterar Tipo** estar definida para **Aplicativo de API**.

	![Tipo de aplicativo no diálogo Serviço de Aplicativos](./media/app-service-api-dotnet-get-started/apptype.png)

4. Insira um **Nome de Aplicativo de API** que seja exclusivo no domínio *azurewebsites.net*. Você pode aceitar o nome padrão que o Visual Studio propõe.

	Se inserir um nome que outra pessoa já tenha usado, você verá um ponto de exclamação vermelho à direita.

	A URL do aplicativo de API será `{API app name}.azurewebsites.net`.

6. Na lista suspensa **Grupo de Recursos**, clique em **Novo** e insira "ToDoListGroup" ou outro nome, se preferir.

	Um grupo de recursos é uma coleção de recursos do Azure, como aplicativos de API, bancos de dados, VMs e assim por diante. Para este tutorial, é melhor criar um novo grupo de recursos porque isso facilitará a exclusão de todos os recursos do Azure criados para o tutorial em uma única etapa.

	Essa caixa permite que você selecione um [grupo de recursos](../resource-group-overview.md) existente ou crie um novo digitando um nome diferente de qualquer grupo de recursos existente na assinatura.

4. Clique no botão **Novo** ao lado da lista suspensa **Plano do Serviço de Aplicativo**.

	A captura de tela mostra valores de exemplo para o **Nome do Aplicativo de API**, **Assinatura** e **Grupo de Recursos**. Seus valores serão diferentes.

	![Criar caixa de diálogo do Serviço de Aplicativo](./media/app-service-api-dotnet-get-started/createas.png)

	Nas etapas a seguir, você criará um plano de Serviço de Aplicativo para o novo grupo de recursos. Um plano de Serviço de Aplicativo especifica os recursos de computação em que seu aplicativo de API é executado. Por exemplo, se você escolher a camada gratuita, seu aplicativo de API será executado em VMs compartilhadas, enquanto que para algumas camadas pagas, ele é executado em VMs dedicadas. Para saber mais sobre os planos do Serviço de Aplicativo, consulte a [Visão geral dos planos do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Na caixa de diálogo **Configurar Plano do Serviço de Aplicativo**, insira "ToDoListPlan" ou outro nome, se preferir.

5. Na lista suspensa **Local**, escolha o local mais próximo de você.

	Essa configuração especifica em qual datacenter do Azure o aplicativo será executado. Escolha um local perto de você para minimizar a [latência](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Na lista suspensa **Tamanho**, clique em **Gratuito**.

	Neste tutorial, o tipo de preço gratuito permitirá um desempenho suficiente.

6. Na caixa de diálogo **Configurar o Plano do Serviço de Aplicativo**, clique em **OK**.

	![Clicar em OK em Configurar Plano do Serviço de Aplicativos](./media/app-service-api-dotnet-get-started/configasp.png)

7. Na caixa de diálogo **Criar Serviço de Aplicativo**, clique em **Criar**.

	![Clicar em Criar no diálogo Criar Serviço de Aplicativos](./media/app-service-api-dotnet-get-started/clickcreate.png)

	O Visual Studio cria o aplicativo de API e um perfil de publicação que tem todas as configurações necessárias para o aplicativo de API. Em seguida, ele abre o assistente **Publicar na Web**, que você usará para implantar o projeto.

	O assistente **Publicar na Web** será aberto na guia **Conexão** (mostrada abaixo).

	Na guia **Conexão**, as configurações **Servidor** e **Nome do site** apontam para seu aplicativo de API. O **Nome de usuário** e a **Senha** são credenciais de implantação que o Azure cria para você. Após a implantação, o Visual Studio abre um navegador na **URL de Destino** (essa é a única finalidade da **URL de Destino**).

8. Clique em **Próximo**.

	![Clique em Avançar na guia Conexão de Publicar Web](./media/app-service-api-dotnet-get-started/connnext.png)

	A próxima guia é **Configurações** (mostrada abaixo). Aqui, você pode alterar a guia de configuração da compilação para implantar uma compilação de depuração para a [depuração remota](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). A guia também oferece várias **Opções de Publicação do Arquivo**:

	* Remover os arquivos adicionais no destino
	* Pré-compilar durante a publicação
	* Excluir arquivos da pasta App\_Data

	Para este tutorial, você não precisará de qualquer uma delas. Para obter explicações detalhadas sobre o que fazem, confira [Como implantar um projeto Web usando a publicação de um clique no Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx).

14. Clique em **Próximo**.

	![Clique em Avançar na guia Configurações de Publicar Web](./media/app-service-api-dotnet-get-started/settingsnext.png)

	A próxima guia é **Visualização** (mostrada abaixo), que oferece a oportunidade de ver quais arquivos serão copiados do projeto para o aplicativo de API. Quando você estiver implantando um projeto para um aplicativo de API no qual já tenha implantado antes, somente os arquivos alterados serão copiados. Se quiser ver uma lista do que será copiado, você poderá clicar no botão **Iniciar Visualização**.

15. Clique em **Publicar**.

	![Clicar em Publicar na guia Visualização de Publicar Web](./media/app-service-api-dotnet-get-started/clickpublish.png)

	O Visual Studio implanta o projeto ToDoListDataAPI para o novo aplicativo de API. A janela **Saída** registrará uma implantação bem-sucedida, e uma página "criada com êxito" será exibida em uma janela do navegador aberta para a URL do aplicativo de API.

	![Implantação bem-sucedida da janela de saída](./media/app-service-api-dotnet-get-started/deploymentoutput.png)

	![Página Novo aplicativo de API criado com êxito](./media/app-service-api-dotnet-get-started/appcreated.png)

11. Adicione "swagger" à URL na barra de endereço do navegador e pressione Enter. (A URL é `http://{apiappname}.azurewebsites.net/swagger`.)

	O navegador exibe a mesma interface do usuário do Swagger que você viu anteriormente, mas agora ela está em execução na nuvem. Experimente o método Get e você verá que voltou aos itens de tarefas pendentes 2 padrão. As alterações feitas anteriormente foram salvas na memória do computador local.

12. Abra o [Portal do Azure](https://portal.azure.com/).

	O portal do Azure é uma interface da Web para gerenciar recursos do Azure, como aplicativos de API.
 
14. Clique em **Procurar > Serviços de Aplicativos**.

	![Procurar Serviços de Aplicativos](./media/app-service-api-dotnet-get-started/browseas.png)

15. Na folha **Serviços de Aplicativos**, localize e clique no novo aplicativo de API. (No portal do Azure, as janelas abertas à direita são chamadas de *folhas*.)

	![Folha Serviços de Aplicativos](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

	Duas folhas são abertas. Uma folha tem uma visão geral do aplicativo de API e outra tem uma longa lista de configurações que você pode exibir e alterar.

16. Na folha **Configurações**, localize a seção **API** e clique em **Definição da API**.

	![Definição de API na folha Configurações](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

	A folha **Definição da API** permite que você especifique a URL para retornar os metadados do Swagger 2.0 no formato JSON. Quando o Visual Studio cria o aplicativo de API, define a URL de definição da API com o valor padrão para os metadados gerados pelo Swashbuckle vistos anteriormente, que é a URL base do aplicativo de API mais `/swagger/docs/v1`.

	![URL de definição da API](./media/app-service-api-dotnet-get-started/apidefurl.png)

	Quando você seleciona um aplicativo de API para gerar código para ele, o Visual Studio recupera os metadados dessa URL.

## <a id="codegen"></a> Gerar o código de cliente para a camada de dados

Uma das vantagens da integração do Swagger a aplicativos de API do Azure é a geração automática de código. As classes de cliente geradas tornam mais fácil escrever código para chamar um aplicativo de API.

O projeto ToDoListAPI já tem o código de cliente gerado, mas, nas etapas a seguir, você o excluirá e recriará para ver como fazer a geração de código.

1. No **Gerenciador de Soluções** do Visual Studio, no projeto ToDoListAPI, exclua a pasta *ToDoListDataAPI*. **Cuidado: exclua apenas a pasta, não o projeto ToDoListDataAPI.**

	![Excluir código de cliente gerado](./media/app-service-api-dotnet-get-started/deletecodegen.png)

	Essa pasta foi criada usando o processo de geração de código que você está prestes a seguir.

2. Clique com o botão direito do mouse no projeto ToDoListAPI e clique em **Adicionar > Cliente da API REST**.

	![Adicionar o cliente da API REST ao Visual Studio](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. Na caixa de diálogo **Adicionar Cliente da API REST**, clique em **URL do Swagger** e **Selecionar Ativo do Azure**.

	![Selecionar Ativo do Azure](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. Na caixa de diálogo **Serviço de Aplicativo**, expanda o grupo de recursos que você está usando neste tutorial, selecione o aplicativo de API e clique em **OK**.

	![Selecionar aplicativo de API para geração de código](./media/app-service-api-dotnet-get-started/codegenselect.png)

	Observe que, quando você retornar para o diálogo **Adicionar Cliente da API REST**, a caixa de texto terá sido preenchida com o valor de URL de definição de API vista anteriormente no portal.

	![URL de Definição da API](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

	>[AZURE.TIP] Uma maneira alternativa de obter metadados para a geração de código é inserir a URL diretamente em vez de passar pelo diálogo Procurar. Ou então, se quiser gerar o código de cliente antes de implantar o Azure, você poderá executar o projeto de API Web localmente, ir para a URL que fornece o arquivo JSON Swagger, salvar o arquivo e usar a opção **Selecionar um arquivo de metadados do Swagger**.

9. Na caixa de diálogo **Adicionar Cliente da API REST**, clique em **OK**.

	O Visual Studio cria uma pasta com o nome do aplicativo de API e gera classes de cliente.

	![Arquivos de código para cliente gerado](./media/app-service-api-dotnet-get-started/codegenfiles.png)

5. No projeto ToDoListAPI, abra *Controllers\\ToDoListController.cs* para ver o código que chama a API usando o cliente gerado.

	O trecho a seguir mostra como o código instancia o objeto de cliente e chama o método Get.

		private static ToDoListDataAPI NewDataAPIClient()
		{
		    var client = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
		    return client;
		}
		
		public async Task<IEnumerable<ToDoItem>> Get()
		{
		    using (var client = NewDataAPIClient())
		    {
		        var results = await client.ToDoList.GetByOwnerAsync(owner);
		        return results.Select(m => new ToDoItem
		        {
		            Description = m.Description,
		            ID = (int)m.ID,
		            Owner = m.Owner
		        });
		    }
		}

	O parâmetro do construtor obtém a URL do ponto de extremidade na configuração do aplicativo `toDoListDataAPIURL`. No arquivo Web.config, esse valor é definido como a URL do IIS Express local do projeto de API para que você possa executar o aplicativo localmente. Se você omitir o parâmetro do construtor, o ponto de extremidade padrão será a URL da qual você gerou o código.

6. A classe de cliente será gerada com um nome diferente baseado no nome do aplicativo de API. Altere o código em *Controllers\\ToDoListController.cs* para que o nome do tipo corresponda ao que foi gerado em seu projeto. Por exemplo, se chamar o aplicativo de API de ToDoListDataAPI071316, você alterará este código:

		private static ToDoListDataAPI NewDataAPIClient()
		{
		    var client = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));

para isto:

		private static ToDoListDataAPI071316 NewDataAPIClient()
		{
		    var client = new ToDoListDataAPI071316(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));


## Criar um aplicativo de API para hospedar a camada intermediária

Anteriormente, você [criou o aplicativo de API de camada de dados e implantou código nele](#createapiapp). Agora, você segue o mesmo procedimento para o aplicativo de API de camada intermediária.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ToDoListAPI de camada intermediária (não no ToDoListDataAPI de camada de dados) e clique em **Publicar**.

	![Clicar em Publicar no Visual Studio](./media/app-service-api-dotnet-get-started/pubinmenu2.png)

3.  Na guia **Perfil** do assistente **Publicar Web**, clique em **Serviço de Aplicativo do Microsoft Azure**.

5. Na caixa de diálogo **Serviço de Aplicativo**, clique em **Novo**.

3. Na guia **Hospedagem** da caixa de diálogo **Criar Serviço de Aplicativo**, aceite o **Nome de Aplicativo de API** padrão ou digite um nome que seja exclusivo no domínio *azurewebsites.net*.

5. Escolha a **assinatura** do Azure que você usa.

6. Na lista suspensa **Grupo de Recursos**, escolha o mesmo grupo de recursos que você criou anteriormente.

4. Na lista suspensa **Plano do Serviço de Aplicativo**, escolha o mesmo plano criado anteriormente. O padrão será a esse valor.

7. Clique em **Criar**.

	O Visual Studio cria o aplicativo de API, cria um perfil de publicação para ele e exibe a etapa **Conexão** do assistente **Publicar na Web**.

3.  Na etapa **Conexão** do assistente **Publicar Web**, clique em **Publicar**.

	O Visual Studio implanta o projeto ToDoListAPI no novo aplicativo de API e abre um navegador para a URL do aplicativo de API. A página "Criado com êxito" é exibida.

## Configurar a camada intermediária para chamar a camada de dados

Se você chamasse o aplicativo de API da camada intermediária agora, ele tentaria chamar a camada de dados usando a URL do localhost que ainda está no arquivo Web.config. Nesta seção, você insere a URL do aplicativo de API da camada de dados em uma configuração de ambiente no aplicativo de API da camada intermediária. Quando o código no aplicativo de API da camada intermediária recupera a configuração da URL da camada de dados, a configuração do ambiente substitui o que está no arquivo Web.config.
 
1. Vá para o [portal do Azure](https://portal.azure.com/) e navegue até a folha **Aplicativo de API** do aplicativo de API que você criou para hospedar o projeto TodoListAPI (camada intermediária).

2. Na folha **Configurações** do Aplicativo de API, clique em **Configurações do aplicativo**.
 
4. Na folha **Configurações do Aplicativo** do Aplicativo de API, role para baixo até a seção **Configurações do aplicativo** e adicione a chave e o valor a seguir:

	| **Chave** | toDoListDataAPIURL |
	|---|---|
	| **Valor** | https://{your nome do aplicativo de API da camada de dados}.azurewebsites.net |
	| **Exemplo** | https://todolistdataapi.azurewebsites.net |

4. Clique em **Salvar**.

	![Clicar em Salvar para Configurações de Aplicativo](./media/app-service-api-dotnet-get-started/asinportal.png)

	Quando o código for executado no Azure, esse valor substituirá a URL de localhost no arquivo Web.config.

## Teste

11. Em uma janela de navegador, navegue até a URL do novo aplicativo de API da camada intermediária que você acabou de criar para ToDoListAPI. Você pode acessá-lo clicando na URL na folha principal do aplicativo de API no portal.

13. Adicione "swagger" à URL na barra de endereço do navegador e pressione Enter. (A URL é `http://{apiappname}.azurewebsites.net/swagger`.)

	O navegador exibe a mesma IU do Swagger vista anteriormente para ToDoListDataAPI, mas agora `owner` não é um campo obrigatório para a operação Get, pois o aplicativo de API da camada intermediária está enviando esse valor para o aplicativo de API da camada de dados. (Quando você executar os tutoriais de autenticação, a camada intermediária enviará as IDs de usuário reais para o parâmetro `owner`; no momento, ela está codificando um asterisco.)

12. Experimente o método Get e os outros métodos para validar se o aplicativo de API de camada intermediária está chamando o aplicativo de API de camada de dados com êxito.

	![Método Get da interface do usuário do Swagger](./media/app-service-api-dotnet-get-started/midtierget.png)

## Solucionar problemas

Caso você encontre um problema ao percorrer este tutorial, aqui estão algumas ideias para solução de problemas:

* Verifique se está usando a última versão do [SDK do Azure para .NET](http://go.microsoft.com/fwlink/?linkid=518003).

* Dois nomes do projeto são semelhantes (ToDoListAPI, ToDoListDataAPI). Se as coisas não ficarem conforme descrito nas instruções quando você estiver trabalhando com um projeto, verifique se que você abriu o projeto correto.

* Se você estiver em uma rede corporativa e estiver tentando implantar no Serviço de Aplicativo do Azure por meio de um firewall, verifique se as portas 443 e 8172 estão abertas para implantação na Web. Se não puder abrir essas portas, você poderá usar outros métodos de implantação. Confira [Implantar seu aplicativo no Serviço de Aplicativo do Azure](../app-service-web/web-sites-deploy.md).

* Erros do tipo "Nomes de rota devem ser exclusivos": você poderá recebê-los se implantar acidentalmente o projeto errado em um aplicativo de API e, mais tarde, implantar o projeto correto nele. Para corrigir isso, reimplante o projeto correto para o aplicativo de API e, na guia **Configurações** do assistente **Publicar na Web**, selecione **Remover arquivos adicionais no destino**.

Quando seu aplicativo de API ASP.NET estiver em execução no Serviço de Aplicativo do Azure, procure saber mais sobre os recursos do Visual Studio que simplificam a solução de problemas. Para saber mais sobre o registro em log, a depuração remota e muito mais, confira [Solução de problemas dos aplicativos do Serviço de Aplicativo do Azure no Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## Próximas etapas

Neste tutorial, você viu como criar aplicativos de API, implantar código neles, gerar código cliente para eles e consumi-los usando clientes .NET. O próximo tutorial desta série mostra como [usar CORS para consumir aplicativos de API de clientes JavaScript](app-service-api-cors-consume-javascript.md).
 
Para obter mais informações sobre geração de código de cliente, confira o repositório [Azure/AutoRest](https://github.com/azure/autorest) em GitHub.com. Para obter ajuda com os problemas para usar o cliente gerado, abra um [problema no repositório AutoRest](https://github.com/azure/autorest/issues).

Se você quiser criar novos projetos de aplicativo de API do zero, use o modelo de **aplicativo de API do Azure**.

![Modelo de Aplicativo de API no Visual Studio](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

O modelo de projeto **Aplicativo de API do Azure** é equivalente a escolher o modelo **Vazio** do ASP.NET 4.5.2, clicar na caixa de seleção para adicionar o suporte da API Web e instalar o pacote NuGet do Swashbuckle. Além disso, o modelo adiciona alguns códigos de configuração do Swashbuckle projetados para evitar a criação de IDs de operação do Swagger duplicadas. Depois de criar um projeto de aplicativo de API, você pode implantá-lo em um aplicativo de API da mesma maneira como viu neste tutorial.

<!---HONumber=AcomDC_0720_2016-->