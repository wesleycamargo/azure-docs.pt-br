<properties
	pageTitle="Introdução aos aplicativos de API e ao ASP.NET no Serviço de Aplicativo do Azure | Microsoft Azure"
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
	ms.date="02/05/2016"
	ms.author="tdykstra"/>

# Introdução aos aplicativos de API e ao ASP.NET no Serviço de Aplicativo do Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Visão geral

Este é o primeiro de uma série de tutoriais que mostram como usar recursos do Serviço de Aplicativo do Azure que são úteis para desenvolver e hospedar APIs:

* Suporte integrado para metadados de API
* Suporte a CORS
* Suporte a autenticação e autorização

Você implantará um aplicativo de exemplo para dois [aplicativos de API](app-service-api-apps-why-best-platform.md) e um aplicativo Web no Serviço de Aplicativo do Azure. O aplicativo de exemplo é uma lista de tarefas pendentes que tem um front-end SPA (aplicativo de página única) do AngularJS, uma camada intermediária de API Web do ASP.NET e uma camada de dados de API Web do ASP.NET, conforme mostrado no diagrama.

![](./media/app-service-api-dotnet-get-started/noauthdiagram.png)

Aqui está uma captura de tela do front-end SPA.

![](./media/app-service-api-dotnet-get-started/todospa.png)

Ao concluir este tutorial, você terá as duas APIs Web em execução em aplicativos de API do Serviço de Aplicativo. Depois de concluir o tutorial a seguir, você terá o aplicativo inteiro em execução na nuvem, com o SPA em um aplicativo Web do Serviço de Aplicativo. Nos tutoriais subsequentes, você adicionará a autenticação e a autorização.

## O que você aprenderá

Neste tutorial, você aprenderá:

* Como preparar seu computador para desenvolvimento do Azure instalando o SDK do Azure para .NET.
* Como trabalhar com aplicativos de API e aplicativos Web no Serviço de Aplicativo do Azure usando ferramentas incorporadas ao Visual Studio 2015.
* Como automatizar a descoberta de API usando o pacote NuGet do Swashbuckle para gerar dinamicamente a definição de API do Swagger JSON.
* Como usar código cliente gerado automaticamente para consumir um aplicativo de API de um cliente .NET.
* Como usar o portal do Azure para configurar o ponto de extremidade de metadados do aplicativo de API.

## Pré-requisitos

[AZURE.INCLUDE [prerequisites](../../includes/app-service-api-dotnet-get-started-prereqs.md)]

[AZURE.INCLUDE [set-up-dev-environment](../../includes/install-sdk-2015-2013.md)]

Este tutorial requer a versão 2.8.2 ou posterior do SDK do Azure para .NET.

## Baixar o aplicativo de exemplo 

1. Baixe o repositório [Azure-Samples/app-service-api-dotnet-to-do-list](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list).

	Você pode clicar no botão **Baixar ZIP** ou clonar o repositório para seu computador local.

2. Abra a solução ToDoList no Visual Studio 2015 ou 2013.

	A solução do Visual Studio é um aplicativo de exemplo que funciona com itens pendentes simples que consistem em uma descrição e um proprietário.

		public class ToDoItem 
		{ 
		    public int ID { get; set; } 
		    public string Description { get; set; } 
		    public string Owner { get; set; } 
		} 
 
	A solução inclui três projetos:

	![](./media/app-service-api-dotnet-get-started/projectsinse.png)

	* **ToDoListAngular** ‒ O front-end: um SPA do AngularJS que chama a camada intermediária. 

	* **ToDoListAPI** ‒ A camada intermediária: um projeto de API Web do ASP.NET que chama a camada de dados para executar operações CRUD em itens pendentes.

	* **ToDoListDataAPI** ‒ a camada de dados: um projeto de API Web do ASP.NET que executa operações CRUD nos itens pendentes. Os itens pendentes são armazenados na memória, o que significa que, sempre que o aplicativo é reiniciado, todas as alterações são perdidas.

	A camada intermediária fornece a ID de usuário no campo `Owner` quando chama a camada de dados. No código que você baixa, a ID de usuário é sempre "*". Quando você adicionar a autenticação nos tutoriais posteriores, a camada intermediária fornecerá a ID de usuário real para a camada de dados.

2. Compile o projeto para restaurar os pacotes NuGet.

### Opcional: executar o aplicativo localmente

Nesta seção, você verificará se pode executar o cliente localmente e chamar a API enquanto ela também estiver sendo executada localmente.

**Observação:** essas instruções funcionam para o Internet Explorer e o Edge porque esses navegadores permitem chamadas JavaScript entre origens de e para URLs `http://localhost`. Se você estiver usando o Chrome, inicie o navegador com a opção `--disable-web-security`. Se você estiver usando o Firefox, ignore esta seção.

1. Defina todos os três projetos como projetos de inicialização, primeiro iniciando ToDoListDataAPI e, em seguida, ToDoListAPI e ToDoListAngular. (Clique com o botão direito do mouse na solução, clique em **Propriedades**, selecione **Vários projetos de inicialização**, coloque os projetos na ordem correta e defina **Ação** como **Iniciar** para cada um deles.)  

2. Pressione F5 para iniciar os projetos.

	Duas janelas de navegador são abertas com páginas de erro HTTP 403, o que é normal para projetos de API Web, e a terceira janela do navegador mostra a interface do usuário do AngularJS.

3. Na janela do navegador que mostra a interface do usuário do AngularJS, clique na guia **Lista de Tarefas Pendentes**.

	A interface do usuário mostra dois itens pendentes padrão.

	![](./media/app-service-api-dotnet-get-started/todospa.png)

4. Adicione, edite e exclua itens pendentes para ver como o aplicativo funciona.

	As alterações feitas são armazenadas na memória e são perdidas quando você reinicia o aplicativo.

3. Feche as janelas do navegador.

## Usar a interface do usuário e metadados Swagger

O suporte a metadados da API 2.0 do [Swagger](http://swagger.io/) é integrado ao Serviço de Aplicativo do Azure. Cada aplicativo de API especifica um ponto de extremidade de URL que retorna metadados para a API no formato JSON Swagger. Os metadados retornados do ponto de extremidade podem ser usados para gerar código de cliente.

O pacote [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) do NuGet fornece metadados Swagger 2.0 para um projeto de API Web do ASP.NET. O Swashbuckle usa a Reflexão para gerar os metadados dinamicamente.

O pacote NuGet Swashbuckle já está instalado nos projetos ToDoListDataAPI e ToDoListAPI que você baixou e já estará instalado quando você criar um novo projeto usando o modelo de projeto **Aplicativo de API do Azure**. (No Visual Studio: **Arquivo > Novo > Projeto > Aplicativo Web ASP.NET > Aplicativo de API do Azure**.)

Nesta seção do tutorial, você verá os metadados do Swagger 2.0 gerados e experimentará uma interface do usuário de teste baseada nos metadados do Swagger.

2. Defina o projeto ToDoListDataAPI como projeto de inicialização. 
 
4. Pressione F5 para executar o projeto no modo de depuração.

	O navegador é aberto e mostra a página de erro HTTP 403.

12. Na barra de endereços do navegador, adicione `swagger/docs/v1` no fim da linha e pressione Return. (A URL será `http://localhost:45914/swagger/docs/v1`.)

	Essa é a URL padrão usada pelo Swashbuckle para retornar metadados JSON do Swagger 2.0 para a API. Se você estiver usando o Internet Explorer, o navegador solicitará que baixe um arquivo *v1.json*.

	![](./media/app-service-api-dotnet-get-started/iev1json.png)

	Se você estiver usando o Chrome, o Firefox ou o Edge, o navegador exibirá o JSON em sua janela.

	![](./media/app-service-api-dotnet-get-started/chromev1json.png)

	O exemplo a seguir mostra a primeira seção dos metadados do Swagger para a API, com a definição para o método Get. Esses metadados geram a interface do usuário do Swagger que você usará nas etapas a seguir e em uma seção posterior do tutorial para gerar o código cliente automaticamente.

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

1. Feche o navegador.

3. No projeto ToDoListDataAPI no **Gerenciador de Soluções**, abra o arquivo *App\_Start\\SwaggerConfig.cs*, role para baixo até o código a seguir e remova as marcas de comentário dele.

		/*
		    })
		.EnableSwaggerUi(c =>
		    {
		*/

	O arquivo *SwaggerConfig.cs* é criado quando você instala o pacote do Swashbuckle em um projeto. O arquivo fornece várias maneiras de se configurar o Swashbuckle.

	O código que você removeu habilita a interface do usuário do Swagger que será usada nas etapas a seguir. Quando você cria um projeto de API Web usando o modelo de projeto de aplicativo de API, o código é comentado por padrão como medida de segurança.

5. Execute o projeto novamente.

3. Na barra de endereços do navegador, adicione `swagger` no fim da linha e pressione Return. (A URL será `http://localhost:45914/swagger`.)

4. Quando for exibida a página de interface do usuário do Swagger, clique em **ToDoList** para ver os métodos disponíveis.

	![](./media/app-service-api-dotnet-get-started/methods.png)

5. Clique em **Obter**.

6. Digite um asterisco como o valor do parâmetro `owner` e clique em **Experimentar**.

	![](./media/app-service-api-dotnet-get-started/gettryitout1.png)

	A interface do usuário do Swagger chama o método Get ToDoList e exibe os resultados JSON.

	![](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. Clique em **Postar** e marque a caixa em **Esquema de Modelo**.

	Clicar no esquema de modelo preencherá previamente a caixa de entrada onde é possível especificar o valor do parâmetro para o método Post. (Se isso não funcionar no Internet Explorer, use um navegador diferente ou insira o valor do parâmetro manualmente na próxima etapa.

	![](./media/app-service-api-dotnet-get-started/post.png)

7. Altere o JSON na caixa de entrada do parâmetro `contact` para que ele seja semelhante ao exemplo a seguir ou substitua-o por seu próprio texto de descrição:

		{
		  "ID": 2,
		  "Description": "buy the dog a toy",
		  "Owner": "*"
		}

10. Clique em **Experimentar**.

	A API de ToDoList retorna um código de resposta HTTP 204 que indica êxito.

11. Clique em **Obter > Experimentar**.

	A resposta do método Get agora inclui o novo item pendente.

12. Experimente também os métodos Put, Delete e Get by ID e feche o navegador.

O Swashbuckle funciona com qualquer projeto de API Web ASP.NET. Se você deseja adicionar a geração de metadados de Swagger a um projeto existente, instale o pacote Swashbuckle. Para criar um novo projeto, use o modelo de projeto ASP.NET para o **Aplicativo de API do Azure**, mostrado na ilustração a seguir. Esse modelo cria um projeto de API Web com o Swashbuckle instalado.

![](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

**Observação:** por padrão, o Swashbuckle pode gerar IDs de operação do Swagger duplicadas para os métodos do controlador. Isso acontecerá se o controlador tiver métodos HTTP sobrecarregados, por exemplo: `Get()` e `Get(id)`. Para obter informações sobre como lidar com sobrecargas, consulte [Personalizar definições de API geradas pelo Swashbuckle](app-service-api-dotnet-swashbuckle-customize.md). Se você criar um projeto de API Web no Visual Studio usando o modelo de Aplicativo de API do Azure, o código que gera IDs de operação exclusivas será adicionado automaticamente ao arquivo *SwaggerConfig.cs*.

## Criar um aplicativo de API no Azure e implantar o projeto ToDoListAPI nele

Nesta seção, use as ferramentas do Azure integradas ao assistente **Publicar Web** do Visual Studio para criar um novo aplicativo de API no Azure. Em seguida, implante o projeto ToDoListDataAPI no novo aplicativo de API e chame a API executando a interface do usuário do Swagger novamente, desta vez enquanto ele estiver sendo executado na nuvem.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ToDoListDataAPI e clique em **Publicar**.

3.  Na etapa **Perfil** do assistente **Publicar Web**, clique em **Serviço de Aplicativo do Microsoft Azure**.

	![](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. Entre em sua conta do Azure, se ainda não tiver feito isso, ou atualize suas credenciais se elas tiverem expirado.

4. Na caixa de diálogo Serviço de Aplicativo, escolha a **Assinatura** do Azure que você deseja usar e clique em **Novo**.

	![](./media/app-service-api-dotnet-get-started/clicknew.png)

3. Na guia **Hospedagem** da caixa de diálogo **Criar Serviço de Aplicativo**, clique em **Alterar Tipo** e em **Aplicativo de API**.

	![](./media/app-service-api-dotnet-get-started/apptype.png)

	A definição do tipo como **Aplicativo de API** não determina os recursos que estarão disponíveis para o novo aplicativo. A URL da definição de API (que você verá posteriormente neste tutorial), o suporte a CORS (que você verá no próximo tutorial) e a autenticação (que você verá nos três últimos tutoriais desta série) estão disponíveis para aplicativos Web e aplicativos móveis, bem como aplicativos de API. A criação de um aplicativo como um aplicativo de API tem apenas estes efeitos:

	a. No portal do Azure, o ícone do tipo de aplicativo ou o texto aparecerá em títulos de folha e listas de aplicativos. Além disso, na folha **Configurações**, a seção API aparece anteriormente na lista para um aplicativo de API, em comparação com outros tipos de aplicativos.

	b. No Visual Studio com o SDK do Azure para .NET 2.8.1, o Visual Studio define a URL de definição de API durante a criação de um novo aplicativo de API ASP.NET, mas não para outros tipos de aplicativos.

4. Insira um **Nome do aplicativo de API** que seja exclusivo no domínio *azurewebsites.net*, como ToDoListDataAPI mais um número para torná-lo exclusivo.

	O Visual Studio sugere um nome exclusivo acrescentando uma cadeia de caracteres de data e hora ao nome do projeto. Se preferir, você poderá aceitar esse nome.

	Se você inserir um nome usado por outra pessoa, verá um ponto de exclamação vermelho à direita em vez de uma marca de verificação verde e precisará inserir outro nome de site.

	O Azure usará esse nome como o prefixo para a URL do aplicativo. A URL completa consistirá nesse nome mais *.azurewebsites.net*. Por exemplo, se o nome for `ToDoListDataAPI`, a URL será `todolistdataapi.azurewebsites.net`.

6. Na lista suspensa **Grupo de Recursos**, insira "ToDoListGroup" ou outro nome, se preferir.

	Essa caixa permite que você selecione um [grupo de recursos](../azure-preview-portal-using-resource-groups.md) existente ou crie um novo digitando um nome diferente de qualquer grupo de recursos existente na assinatura.

	Para este tutorial, é melhor criar um novo porque facilita a exclusão de todos os recursos do Azure criados para o tutorial em uma única etapa.

4. Clique no botão **Novo** ao lado da lista suspensa **Plano do Serviço de Aplicativo**.

	![](./media/app-service-api-dotnet-get-started/createas.png)

	Para saber mais sobre os planos do Serviço de Aplicativo, consulte a [Visão geral dos planos do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Na caixa de diálogo **Configurar o Plano de Serviço de Aplicativo**, insira "ToDoListPlan" ou outro nome, se preferir.

5. Na lista suspensa **Local**, escolha o local mais próximo de você.

	Essa configuração especifica em qual datacenter do Azure o aplicativo será executado. Para este tutorial, você pode selecionar qualquer região e isto não fará uma diferença notável. Porém, para um aplicativo de produção, o ideal é que seu servidor esteja o mais próximo possível dos clientes que o acessam, de modo a minimizar a [latência](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Na lista suspensa **Tamanho**, clique em **Gratuito**.

	Neste tutorial, o tipo de preço gratuito permitirá o desempenho suficiente.

6. Na caixa de diálogo **Configurar o Plano do Serviço de Aplicativo**, clique em **OK**.

	![](./media/app-service-api-dotnet-get-started/configasp.png)

7. Na caixa de diálogo **Criar Serviço de Aplicativo**, clique em **Criar**.

	O Visual Studio cria o aplicativo de API e um perfil de publicação que tem todas as configurações necessárias para o novo aplicativo de API. Nas etapas a seguir, você usará o novo perfil de publicação para implantar o projeto.
 
	**Observação:** existem outras maneiras de criar aplicativos de API no Serviço de Aplicativo do Azure. No Visual Studio, os mesmos diálogos estão disponíveis durante a criação de um novo projeto. Você também pode criar aplicativos de API usando o portal do Azure, [cmdlets do Azure para o Windows PowerShell](../powershell-install-configure.md) ou a [interface de linha de comando de plataforma cruzada](../xplat-cli.md).

8. Na guia **Conexão** do assistente **Publicar Web**, clique em **Publicar**.

	![](./media/app-service-api-dotnet-get-started/clickpublish.png)

	O Visual Studio implanta o projeto ToDoListDataAPI no novo aplicativo de API e abre um navegador para a URL do aplicativo de API. Uma página "criado com êxito" será exibida no navegador.

	![](./media/app-service-api-dotnet-get-started/appcreated.png)

11. Adicione "swagger" à URL na barra de endereço do navegador e pressione Enter. (A URL será `http://{apiappname}.azurewebsites.net/swagger`).

	O navegador exibe a mesma interface do usuário do Swagger que você viu anteriormente, mas agora ela está em execução na nuvem. Experimente o método Get e você verá que retornou aos dois itens pendentes padrão, pois as alterações feitas anteriormente foram salvas na memória no computador local.

12. Abra o [Portal do Azure](https://portal.azure.com/).
 
14. Clique em **Procurar > Aplicativos de API > {seu novo aplicativo de API}**.

	![](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

16. Clique em **Configurações** e, na folha **Configurações**, localize a seção de API e clique em **Definição de API**.

	![](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

	A folha Definição de API permite que você especifique a URL para retornar metadados do Swagger 2.0 no formato JSON. Quando o Visual Studio cria o aplicativo de API, ele define a URL de definição de API com o valor padrão que vimos anteriormente, que é a URL base do aplicativo de API mais `/swagger/docs/v1`.

	![](./media/app-service-api-dotnet-get-started/apidefurl.png)

	Quando você seleciona um aplicativo de API para gerar código para ele, o Visual Studio recupera os metadados dessa URL.

### URL da definição de API nas ferramentas do Gerenciador de Recursos do Azure

Você também pode configurar a URL de definição de API para um aplicativo de API usando ferramentas do Gerenciador de Recursos do Azure, como o Azure PowerShell, a CLI ou o [Gerenciador de Recursos](https://resources.azure.com/).

Para fazer isso, defina a propriedade `apiDefinition` no tipo de recurso `Microsoft.Web/sites/config` para o recurso `<site name>/web`. Por exemplo, no **Gerenciador de Recursos**, vá para **assinaturas > {sua assinatura} > resourceGroups > {seu grupo de recursos} > provedores > Microsoft.Web > sites > {seu site} > configuração > Web** e você verá a propriedade `apiDefinition`:

		"apiDefinition": {
		  "url": "https://todolistdataapi.azurewebsites.net/swagger/docs/v1"
		}

Para ver um exemplo de um modelo do Gerenciador de Recursos do Azure que inclui o JSON para definir a propriedade de definição de API, abra o [arquivo azuredeploy.json no repositório de aplicativos de exemplo](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json).

## <a id="codegen"></a> Consumir de um cliente .NET usando código de cliente gerado

Uma das vantagens da integração do Swagger a aplicativos de API do Azure é a geração automática de código. As classes de cliente geradas tornam mais fácil escrever código para chamar um aplicativo de API.

Nesta seção, você verá como consumir um aplicativo de API do código de API Web do ASP.NET.

### Gerar código de cliente

Você pode gerar o código cliente para um aplicativo de API usando o Visual Studio ou a linha de comando. Para este tutorial, você usará o Visual Studio. Para saber mais sobre como fazer isso na linha de comando, confira o arquivo Leiame do repositório [Azure/autorest](https://github.com/azure/autorest) no GitHub.com.

O projeto ToDoListAPI já tem o código cliente gerado, mas você o excluirá e o regenerará, o que redefinirá a URL de destino padrão para seu próprio aplicativo de API.

1. No **Gerenciador de Soluções** do Visual Studio, no projeto ToDoListAPI, exclua a pasta *ToDoListDataAPI*.

	Essa pasta foi criada usando o processo de geração de código que você está prestes a seguir.

	![](./media/app-service-api-dotnet-get-started/deletecodegen.png)

2. Clique com o botão direito do mouse no projeto ToDoListAPI e clique em **Adicionar > Cliente da API REST**.

	![](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. Na caixa de diálogo **Adicionar Cliente da API REST**, clique em **URL do Swagger** e em **Selecionar Ativo do Azure**.

	![](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. Na caixa de diálogo **Serviço de Aplicativo**, expanda o grupo de recursos que você está usando neste tutorial, selecione o aplicativo de API e clique em **OK**.

	Essa caixa de diálogo permite mais de uma forma de organização de aplicativos de API na lista, caso você tenha muito muitos para percorrer. Ele também permite inserir uma cadeia de caracteres de pesquisa para filtrar os aplicativos de API pelo nome.

	![](./media/app-service-api-dotnet-get-started/codegenselect.png)

	Se você não encontrar o aplicativo de API na lista, é possível que, ao criar o aplicativo de API, você tenha omitido acidentalmente a etapa que alterava o tipo de aplicativo Web para aplicativo de API. Nesse caso, você pode criar um novo aplicativo de API repetindo as etapas que realizou anteriormente. Você precisará escolher um nome diferente para o aplicativo de API.

	Observe que, quando você retornar à caixa de diálogo **Adicionar Cliente da API REST**, a caixa de texto terá sido preenchida com o valor de URL de definição de API visto anteriormente no portal.

	![](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

	Uma maneira alternativa de obter metadados para a geração de código é inserir a URL diretamente em vez de passar pelo diálogo Procurar. Por exemplo, se você tiver implantado sua API em um aplicativo Web e ele não apareceu na caixa de diálogo Procurar, poderá inserir manualmente a URL que retorna metadados Swagger aqui.

	Outra maneira alternativa de obter metadados é usar a opção **Selecionar um arquivo de metadados do Swagger existente**. Por exemplo, se você quiser gerar código cliente antes de implantar no Azure, poderá executar localmente, baixar o arquivo JSON do Swagger e selecioná-lo aqui.

9. Na caixa de diálogo **Adicionar Cliente da API REST**, clique em **OK**.

	O Visual Studio cria uma pasta com o nome do aplicativo de API e gera classes de cliente.

	![](./media/app-service-api-dotnet-get-started/codegenfiles.png)

5. No projeto ToDoListAPI, abra *Controllers\\ToDoListController.cs* para ver o código que chama a API usando o cliente gerado.

	O trecho a seguir mostra como o código instancia o objeto de cliente e chama o método Get.

		private ToDoListDataAPI db = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

	O parâmetro de construtor obtém a URL de ponto de extremidade da configuração do aplicativo `toDoListDataAPIURL`. No arquivo Web.config, esse valor é definido como a URL do IIS Express local do projeto de API da configuração `toDoListDataAPIURL` para que você possa executar o aplicativo localmente. Se você omitir o parâmetro do construtor, o ponto de extremidade padrão será a URL da qual você gerou o código.

6. A classe de cliente será gerada com um nome diferente baseado no nome de seu aplicativo de API. Altere o código para que o nome do tipo corresponda ao que foi gerado em seu projeto. Por exemplo, se você desse ao Aplicativo de API o nome ToDoListDataAPI0121, o código seria semelhante ao seguinte exemplo:

		private ToDoListDataAPI0121 db = new ToDoListDataAPI0121(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

#### Criar um aplicativo de API para hospedar a camada intermediária

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ToDoListAPI e clique em **Publicar**.

3.  Na guia **Perfil** do assistente **Publicar Web**, clique em **Serviço de Aplicativo do Microsoft Azure**.

5. Na caixa de diálogo **Serviço de Aplicativo**, clique em **Novo**.

3. Na guia **Hospedagem** da caixa de diálogo **Criar Serviço de Aplicativo**, clique em **Alterar Tipo** e altere o tipo para **Aplicativo de API**.

4. Insira um **Nome de Aplicativo de API** que seja exclusivo no domínio *azurewebsites.net*.

5. Escolha a **Assinatura** do Azure com a qual você deseja trabalhar.

6. Na lista suspensa **Grupo de Recursos**, escolha o mesmo grupo de recursos que você criou anteriormente.

4. Na lista suspensa **Plano do Serviço de Aplicativo**, escolha o mesmo plano criado anteriormente.

7. Clique em **Criar**.

	O Visual Studio cria o aplicativo de API, cria um perfil de publicação para ele e exibe a etapa **Conexão** do assistente **Publicar Web**.

### Definir a URL da camada de dados nas configurações do aplicativo de camada intermediária

1. Vá para o [portal do Azure](https://portal.azure.com/) e navegue até a folha **Aplicativo de API** do aplicativo de API que você criou para hospedar o projeto TodoListAPI (camada intermediária).

2. Clique em **Configurações > Configurações do aplicativo**.

3. Na seção **Configurações do aplicativo**, adicione a seguinte chave e valor:

	|Chave|Valor|Exemplo
	|---|---|---|
	|toDoListDataAPIURL|nome de aplicativo de API de tipo de preço https://{your}.azurewebsites.net|https://todolistdataapi0121.azurewebsites.net|

4. Clique em **Salvar**.

	Quando o código for executado no Azure, esse valor substituirá a URL de localhost no arquivo Web.config.

### Implantar o projeto ToDoListAPI para o novo aplicativo de API

3.  Na etapa **Conexão** do assistente **Publicar Web**, clique em **Publicar**.

	O Visual Studio implanta o projeto ToDoListAPI no novo aplicativo de API e abre um navegador para a URL do aplicativo de API. A página "Criado com êxito" é exibida.

### Teste para verificar se ToDoListAPI chama ToDoListDataAPI

11. Adicione "swagger" à URL na barra de endereço do navegador e pressione Enter. (A URL será `http://{apiappname}.azurewebsites.net/swagger`).

	O navegador exibe a mesma interface do usuário do Swagger que você viu anteriormente para ToDoListDataAPI, mas agora `owner` não é um campo obrigatório, pois o aplicativo de API de camada intermediária está enviando esse valor ao aplicativo de API de camada de dados para você.

12. Experimente o método Get e os outros métodos para validar se o aplicativo de API de camada intermediária está chamando o aplicativo de API de camada de dados com êxito.

	![](./media/app-service-api-dotnet-get-started/midtierget.png)

## Próximas etapas

Neste tutorial, você viu como criar aplicativos de API, implantar código neles, gerar código cliente para eles e consumi-los usando clientes .NET. O próximo tutorial na série de introdução aos Aplicativos de API mostra como [consumir aplicativos de API de clientes JavaScript usando CORS](app-service-api-cors-consume-javascript.md).

<!---HONumber=AcomDC_0211_2016-->