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
	ms.date="03/09/2016"
	ms.author="tdykstra"/>

# Introdução aos aplicativos de API e ao ASP.NET no Serviço de Aplicativo do Azure

[AZURE.INCLUDE [seletor](../../includes/app-service-api-get-started-selector.md)]

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

* Como trabalhar com aplicativos de API e aplicativos Web no Serviço de Aplicativo do Azure usando ferramentas incorporadas ao Visual Studio 2015.
* Como automatizar a descoberta de API usando o pacote NuGet do Swashbuckle para gerar dinamicamente a definição de API do Swagger JSON.
* Como usar código cliente gerado automaticamente para consumir um aplicativo de API de um cliente .NET.
* Como usar o portal do Azure para configurar o ponto de extremidade de metadados do aplicativo de API.

## Pré-requisitos

[AZURE.INCLUDE [prerequisites](../../includes/app-service-api-dotnet-get-started-prereqs.md)]

## Baixar o aplicativo de exemplo 

1. Baixe o repositório [Azure-Samples/app-service-api-dotnet-to-do-list](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list).

	Você pode clicar no botão **Baixar ZIP** ou clonar o repositório no seu computador local.

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

	* **ToDoListAngular** ‒ o front-end: um SPA do AngularJS que chama a camada intermediária. 

	* **ToDoListAPI** ‒ a camada intermediária: um projeto de API Web ASP.NET que chama a camada de dados para executar operações CRUD em itens pendentes.

	* **ToDoListDataAPI** ‒ a camada de dados: um projeto de API Web ASP.NET que executa operações CRUD nos itens pendentes. Os itens pendentes são armazenados na memória, o que significa que, sempre que o aplicativo é reiniciado, todas as alterações são perdidas.

	A camada intermediária fornece a ID de usuário no campo `Owner` quando chama a camada de dados. No código que você baixa, a ID de usuário é sempre "*". Quando você adicionar a autenticação nos tutoriais posteriores, a camada intermediária fornecerá a ID de usuário real para a camada de dados.

2. Compile o projeto para restaurar os pacotes NuGet.

### Opcional: executar o aplicativo localmente

Nesta seção, você verificará se pode executar o cliente localmente e chamar a API enquanto ela também estiver sendo executada localmente.

**Observação:** essas instruções funcionam para o Internet Explorer e o Edge porque esses navegadores permitem chamadas JavaScript entre origens de e para URLs `http://localhost`. Se você estiver usando o Chrome, inicie o navegador com a opção `--disable-web-security`. Se você estiver usando o Firefox, ignore esta seção.

1. Defina todos os três projetos como projetos de inicialização, primeiro iniciando ToDoListDataAPI e, em seguida, ToDoListAPI e ToDoListAngular. (No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução, clique em **Propriedades**, selecione **Vários projetos de inicialização**, coloque os projetos na ordem correta e defina **Ação** como **Iniciar** para cada um deles).  

2. Pressione F5 para iniciar os projetos.

	Três janelas do navegador são abertas. Duas janelas de navegador mostram as páginas de erro de HTTP 403 (pesquisa no diretório não permitida), o que é normal para projetos de API Web. A terceira janela do navegador mostra a interface do usuário do AngularJS.

3. Na janela do navegador que mostra a interface do usuário do AngularJS, clique na guia **Lista de Tarefas Pendentes**.

	A interface do usuário mostra dois itens pendentes padrão.

	![](./media/app-service-api-dotnet-get-started/todospa.png)

4. Adicione, edite e exclua itens pendentes para ver como o aplicativo funciona.

	As alterações feitas são armazenadas na memória e são perdidas quando você reinicia o aplicativo.

3. Feche as janelas do navegador.

## Usar a interface do usuário e metadados Swagger

O suporte a metadados da API 2.0 do [Swagger](http://swagger.io/) é integrado ao Serviço de Aplicativo do Azure. Cada aplicativo de API especifica um ponto de extremidade de URL que retorna metadados para a API no formato JSON Swagger. Os metadados retornados do ponto de extremidade podem ser usados para gerar código de cliente.

Um projeto de API Web ASP.NET pode gerar dinamicamente metadados do Swagger usando o pacote NuGet [Swashbuckle](https://www.nuget.org/packages/Swashbuckle). O pacote NuGet Swashbuckle já está instalado nos projetos ToDoListDataAPI e ToDoListAPI que você baixou.

Nesta seção do tutorial, você verá os metadados do Swagger 2.0 gerados e experimentará uma interface do usuário de teste baseada nos metadados do Swagger.

2. Defina o projeto ToDoListDataAPI como projeto de inicialização. 
 
4. Pressione F5 para executar o projeto no modo de depuração.

	O navegador é aberto e mostra a página de erro HTTP 403.

12. Na barra de endereços do navegador, adicione `swagger/docs/v1` no fim da linha e pressione Return. (A URL será `http://localhost:45914/swagger/docs/v1`.)

	Essa é a URL padrão usada pelo Swashbuckle para retornar metadados JSON do Swagger 2.0 para a API.

	Se você estiver usando o Internet Explorer, o navegador solicitará que baixe um arquivo *v1.json*.

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

	O arquivo *SwaggerConfig.cs* é criado quando você instala o pacote Swashbuckle em um projeto. O arquivo fornece várias maneiras de se configurar o Swashbuckle.

	O código que você removeu habilita a interface do usuário do Swagger que será usada nas etapas a seguir. Quando você cria um projeto de API Web usando o modelo de projeto de aplicativo de API, o código é comentado por padrão como medida de segurança.

5. Execute o projeto novamente.

3. Na barra de endereços do navegador, adicione `swagger` no fim da linha e pressione Return. (A URL será `http://localhost:45914/swagger`.)

4. Quando for exibida a página da interface do usuário do Swagger, clique em **ToDoList** para ver os métodos disponíveis.

	![](./media/app-service-api-dotnet-get-started/methods.png)

5. Clique em **Obter**.

6. Insira um asterisco como o valor do parâmetro `owner` e clique em **Experimentar**.

	![](./media/app-service-api-dotnet-get-started/gettryitout1.png)

	A interface do usuário do Swagger chama o método Get ToDoList e exibe o código de resposta e os resultados JSON.

	![](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. Clique em **Postar** e marque a caixa em **Esquema de Modelo**.

	Clicar no esquema de modelo preencherá previamente a caixa de entrada onde é possível especificar o valor do parâmetro para o método Post. (Se isso não funcionar no Internet Explorer, use um navegador diferente ou insira o valor do parâmetro manualmente na próxima etapa).

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

12. Experimente também os métodos Put, Delete e Get by ID.

14. Feche o navegador.

O Swashbuckle funciona com qualquer projeto de API Web ASP.NET. Se você deseja adicionar a geração de metadados de Swagger a um projeto existente, instale o pacote Swashbuckle.

**Observação:** os metadados do Swagger incluem uma ID exclusiva para cada operação de API. Por padrão, o Swashbuckle pode gerar IDs de operação do Swagger duplicadas para os métodos do controlador da API Web. Isso acontecerá se o controlador tiver métodos HTTP sobrecarregados, como `Get()` e `Get(id)`. Para obter informações sobre como lidar com sobrecargas, consulte [Personalizar definições de API geradas pelo Swashbuckle](app-service-api-dotnet-swashbuckle-customize.md). Se você criar um projeto de API Web no Visual Studio usando o modelo de Aplicativo de API do Azure, o código que gera IDs de operação exclusivas será adicionado automaticamente ao arquivo *SwaggerConfig.cs*.

## Criar um aplicativo de API no Azure e implantar o projeto ToDoListAPI nele

Nesta seção, use as ferramentas do Azure integradas ao assistente **Publicar Web** do Visual Studio para criar um novo aplicativo de API no Azure. Em seguida, implante o projeto ToDoListDataAPI no novo aplicativo de API e chame a API executando a interface do usuário do Swagger novamente, desta vez enquanto ele estiver sendo executado na nuvem.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ToDoListDataAPI e clique em **Publicar**.

	![](./media/app-service-api-dotnet-get-started/pubinmenu.png)

3.  Na etapa **Perfil** do assistente **Publicar Web**, clique em **Serviço de Aplicativo do Microsoft Azure**.

	![](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. Entre em sua conta do Azure, se ainda não tiver feito isso, ou atualize suas credenciais se elas tiverem expirado.

4. Na caixa de diálogo Serviço de Aplicativo, escolha a **Assinatura** do Azure que você deseja usar e clique em **Novo**.

	![](./media/app-service-api-dotnet-get-started/clicknew.png)

	A guia **Hospedagem** da caixa de diálogo **Criar Serviço de Aplicativo** é exibida.

	Como você está implantando um projeto de API Web com o Swashbuckle instalado, o Visual Studio pressupõe que você queira criar Aplicativo de API. Isso é indicado pelo título **Nome do Aplicativo de API** e pelo fato de que a lista suspensa **Alterar Tipo** é definida como **Aplicativo de API**.

	![](./media/app-service-api-dotnet-get-started/apptype.png)

	O tipo de aplicativo não determina os recursos que estarão disponíveis para o novo aplicativo de API, aplicativo Web ou aplicativo móvel. Todos os recursos do aplicativo de API mostrados nestes tutoriais estão disponíveis para todos os três tipos. A única diferença é o ícone e o texto que o portal do Azure exibe para identificar o tipo de aplicativo e a ordem na qual os recursos são listados em algumas páginas do portal. Você verá o portal do Azure posteriormente no tutorial; é uma interface da Web para gerenciar recursos do Azure.

4. Insira um **Nome do Aplicativo de API** que seja exclusivo no domínio *azurewebsites.net*, como ToDoListDataAPI mais um número para torná-lo exclusivo.

	O Visual Studio sugere um nome exclusivo acrescentando uma cadeia de caracteres de data e hora ao nome do projeto. Se preferir, você poderá aceitar esse nome.

	Se você inserir um nome usado por outra pessoa, verá um ponto de exclamação vermelho à direita em vez de uma marca de verificação verde e precisará inserir outro nome de site.

	O Azure usará esse nome como o prefixo para a URL do aplicativo. A URL completa consistirá nesse nome mais *.azurewebsites.net*. Por exemplo, se o nome for `ToDoListDataAPI`, a URL será `todolistdataapi.azurewebsites.net`.

6. Na lista suspensa **Grupo de Recursos**, clique em **Novo** e insira "ToDoListGroup" ou outro nome, se preferir.

	Um grupo de recursos é uma coleção de recursos do Azure, como aplicativos de API, bancos de dados, VMs e assim por diante. Para este tutorial, é melhor criar um novo grupo de recursos porque isso facilitará a exclusão de todos os recursos do Azure criados para o tutorial em uma única etapa.

	Essa caixa permite que você selecione um [grupo de recursos](../azure-preview-portal-using-resource-groups.md) existente ou crie um novo digitando um nome diferente de qualquer grupo de recursos existente na assinatura.

4. Clique no botão **Novo** ao lado da lista suspensa **Plano do Serviço de Aplicativo**.

	A captura de tela mostra valores de exemplo para **Nome de Aplicativo de API**, **Assinatura**, e **Grupo de Recursos** – os valores serão diferentes.

	![](./media/app-service-api-dotnet-get-started/createas.png)

	Nas etapas a seguir, você criará um plano de Serviço de Aplicativo para o novo grupo de recursos. Um plano de Serviço de Aplicativo especifica os recursos de computação em que seu aplicativo de API é executado. Por exemplo, se você escolher a camada gratuita, seu aplicativo de API será executado em VMs compartilhadas, enquanto que para algumas camadas pagas, ele é executado em VMs dedicadas. Para saber mais sobre os planos do Serviço de Aplicativo, consulte a [Visão geral dos planos do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. No diálogo **Configurar Plano de Serviço de Aplicativo**, insira "ToDoListPlan" ou outro nome, se preferir.

5. Na lista suspensa **Local**, escolha o local mais próximo de você.

	Essa configuração especifica em qual datacenter do Azure o aplicativo será executado. Para este tutorial, você pode selecionar qualquer região e isto não fará uma diferença notável. Porém, para um aplicativo de produção, o ideal é que seu servidor esteja o mais próximo possível dos clientes que o acessam, de modo a minimizar a [latência](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Na lista suspensa **Tamanho**, clique em **Gratuito**.

	Neste tutorial, o tipo de preço gratuito permitirá o desempenho suficiente.

6. Na caixa de diálogo **Configurar o Plano do Serviço de Aplicativo**, clique em **OK**.

	![](./media/app-service-api-dotnet-get-started/configasp.png)

7. Na caixa de diálogo **Criar Serviço de Aplicativo**, clique em **Criar**.

	![](./media/app-service-api-dotnet-get-started/clickcreate.png)

	O Visual Studio cria o aplicativo de API.

	**Observação:** existem outras maneiras de criar aplicativos de API no Serviço de Aplicativo do Azure. Por exemplo, no Visual Studio, quando você cria um novo projeto, pode criar recursos do Azure para ele da mesma maneira que acabou de ver para um projeto existente. Você também pode criar aplicativos de API usando o [portal do Azure](https://portal.azure.com/), os [cmdlets do Azure para o Windows PowerShell](../powershell-install-configure.md) ou a [interface de linha de comando de plataforma cruzada](../xplat-cli.md).

	Quando o Visual Studio terminar de criar o aplicativo de API, ele criará um perfil de publicação com todas as configurações necessárias para o novo aplicativo de API. Nas etapas a seguir, você usará o novo perfil de publicação para implantar o projeto.

8. Na guia **Conexão** do assistente **Publicar Web**, clique em **Avançar**.

	Em vez disso, você pode prosseguir e clicar em **Publicar** agora para implantar imediatamente o projeto para o novo aplicativo de API; mas, no tutorial, você percorrerá outras guias desse diálogo para ver o que pode fazer nelas.

	![](./media/app-service-api-dotnet-get-started/connnext.png)

	A próxima é a guia **Configurações**. Aqui, você pode alterar a guia de configuração de compilação para implantar uma compilação de depuração para [depuração remota](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). A guia também oferece várias **Opções de Publicação do Arquivo**:

	* Remover os arquivos adicionais no destino
	* Pré-compilar durante a publicação
	* Excluir arquivos da pasta App\_Data

	Para este tutorial, você não precisará de qualquer uma delas. Para obter explicações detalhadas sobre o que elas fazem, veja [Como implantar um projeto Web usando a publicação de um clique no Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx).

14. Clique em **Próximo**.

	![](./media/app-service-api-dotnet-get-started/settingsnext.png)

	A guia **Visualização** oferece uma oportunidade de ver quais arquivos serão copiados do seu projeto para o aplicativo de API. Quando você estiver implantando um projeto para um aplicativo de API no qual já tenha implantado antes, somente os arquivos alterados serão copiados. Se você quiser ver uma lista do que será copiado, poderá clicar no botão **Iniciar Visualização**.

15. Clique em **Publicar**.

	![](./media/app-service-api-dotnet-get-started/clickpublish.png)

	O Visual Studio implanta o projeto ToDoListDataAPI para o novo aplicativo de API. A janela **Saída** registra uma implantação bem-sucedida e uma página "criada com êxito" será exibida em uma janela de navegador aberta para a URL do aplicativo de API.

	![](./media/app-service-api-dotnet-get-started/deploymentoutput.png)

	![](./media/app-service-api-dotnet-get-started/appcreated.png)

11. Adicione "swagger" à URL na barra de endereço do navegador e pressione Enter. (A URL será `http://{apiappname}.azurewebsites.net/swagger`).

	O navegador exibe a mesma interface do usuário do Swagger que você viu anteriormente, mas agora ela está em execução na nuvem. Experimente o método Get e você verá que retornou aos dois itens pendentes padrão, pois as alterações feitas anteriormente foram salvas na memória no computador local.

12. Abra o [Portal do Azure](https://portal.azure.com/).

	O portal do Azure é uma interface da Web para gerenciar recursos do Azure, como aplicativos de API.
 
14. Clique em **Procurar > Serviços de Aplicativos**.

	![](./media/app-service-api-dotnet-get-started/browseas.png)

15. Na folha **Serviços de Aplicativos**, localize e clique em seu novo aplicativo de API. (No portal do Azure, as janelas abertas à direita são chamadas de *folhas*).

	![](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

	Duas folhas são abertas, uma com uma visão geral do aplicativo de API e outra com uma longa lista de configurações que você pode exibir e alterar.

16. Na folha **Configurações**, localize a seção **API** e clique em **Definição de API**.

	![](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

	A folha **Definição de API** permite que você especifique a URL para retornar metadados do Swagger 2.0 no formato JSON. Quando o Visual Studio cria o aplicativo de API, ele define a URL de definição de API com o valor padrão para metadados gerados pelo Swashbuckle que vimos anteriormente, que é a URL base do aplicativo de API mais `/swagger/docs/v1`.

	![](./media/app-service-api-dotnet-get-started/apidefurl.png)

	Quando você seleciona um aplicativo de API para gerar código para ele, o Visual Studio recupera os metadados dessa URL.

## <a id="codegen"></a> Consumir o aplicativo de API usando código de cliente gerado

Uma das vantagens da integração do Swagger a aplicativos de API do Azure é a geração automática de código. As classes de cliente geradas tornam mais fácil escrever código para chamar um aplicativo de API.

Nesta seção, você verá como consumir um aplicativo de API do código de API Web ASP.NET.

### Gerar código de cliente

Você pode gerar o código cliente para um aplicativo de API usando o Visual Studio ou a linha de comando. Para este tutorial, você usará o Visual Studio. Para saber mais sobre como fazer isso na linha de comando, confira o arquivo Leiame do repositório [Azure/autorest](https://github.com/azure/autorest) no GitHub.com.

O projeto ToDoListAPI já tem o código de cliente gerado, mas você o excluirá e o regenerará para ver como ele é concluído.

1. No **Gerenciador de Soluções** do Visual Studio, no projeto ToDoListAPI, exclua a pasta *ToDoListDataAPI*.

	Essa pasta foi criada usando o processo de geração de código que você está prestes a seguir.

	![](./media/app-service-api-dotnet-get-started/deletecodegen.png)

2. Clique com o botão direito do mouse no projeto ToDoListAPI e clique em **Adicionar > Cliente da API REST**.

	![](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. Na caixa de diálogo **Adicionar Cliente da API REST**, clique em **URL do Swagger** e em **Selecionar Ativo do Azure**.

	![](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. Na caixa de diálogo **Serviço de Aplicativo**, expanda o grupo de recursos que você está usando neste tutorial, selecione o aplicativo de API e clique em **OK**.

	![](./media/app-service-api-dotnet-get-started/codegenselect.png)

	Essa caixa de diálogo permite mais de uma forma de organização de aplicativos de API na lista, caso você tenha muito muitos para percorrer. Ele também permite inserir uma cadeia de caracteres de pesquisa para filtrar os aplicativos de API pelo nome.

	Observe que, quando você retornar à caixa de diálogo **Adicionar Cliente da API REST**, a caixa de texto terá sido preenchida com o valor de URL de definição de API visto anteriormente no portal.

	![](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

	Uma maneira alternativa de obter metadados para a geração de código é inserir a URL diretamente em vez de passar pelo diálogo Procurar. Outra alternativa é usar a opção **Selecionar um arquivo de metadados do Swagger existente**. Por exemplo, se você quiser gerar código cliente antes de implantar no Azure, poderá executar o projeto de API Web localmente, vá para a URL que fornece o arquivo JSON do Swagger, salve o arquivo e o selecione aqui.

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

	O parâmetro constructor obtém a URL de ponto de extremidade da configuração do aplicativo `toDoListDataAPIURL`. No arquivo Web.config, esse valor é definido como a URL do IIS Express local do projeto de API para que você possa executar o aplicativo localmente. Se você omitir o parâmetro do construtor, o ponto de extremidade padrão será a URL da qual você gerou o código.

6. A classe de cliente será gerada com um nome diferente baseado no nome de seu aplicativo de API; altere o código em *Controllers\\ToDoListController.cs* para que o nome do tipo corresponda ao que foi gerado em seu projeto. Por exemplo, se você desse ao Aplicativo de API o nome ToDoListDataAPI0121, o código seria semelhante ao seguinte exemplo:

		private ToDoListDataAPI0121 db = new ToDoListDataAPI0121(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

### Criar um aplicativo de API para hospedar a camada intermediária

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ToDoListAPI (e não no ToDoListDataAPI) e clique em **Publicar**.

3.  Na guia **Perfil** do assistente **Publicar Web**, clique em **Serviço de Aplicativo do Microsoft Azure**.

5. Na caixa de diálogo **Serviço de Aplicativo**, clique em **Novo**.

3. Na guia **Hospedagem** da caixa de diálogo **Criar Serviço de Aplicativo**, insira um **Nome de Aplicativo de API** que seja exclusivo no domínio *azurewebsites.net*.

5. Escolha a **Assinatura** do Azure com a qual você deseja trabalhar.

6. Na lista suspensa **Grupo de Recursos**, escolha o mesmo grupo de recursos que você criou anteriormente.

4. Na lista suspensa **Plano do Serviço de Aplicativo**, escolha o mesmo plano criado anteriormente. O padrão será a esse valor.

7. Clique em **Criar**.

	O Visual Studio cria o aplicativo de API, cria um perfil de publicação para ele e exibe a etapa **Conexão** do assistente **Publicar Web**.

3.  Na etapa **Conexão** do assistente **Publicar Web**, clique em **Publicar**.

	O Visual Studio implanta o projeto ToDoListAPI no novo aplicativo de API e abre um navegador para a URL do aplicativo de API. A página "Criado com êxito" é exibida.

### Definir a URL do aplicativo de API da camada de dados no aplicativo de API da camada intermediária

Se você chamasse o aplicativo de API da camada intermediária agora, ele tentaria chamar a camada de dados usando a URL do localhost que ainda está no arquivo Web.config. Nesta seção, você insere a URL do aplicativo de API da camada de dados em uma configuração de ambiente no aplicativo de API da camada intermediária. Quando o código no aplicativo de API da camada intermediária recupera a configuração da URL da camada de dados, a configuração do ambiente substitui o que está no arquivo Web.config.
 
1. Vá para o [portal do Azure](https://portal.azure.com/) e navegue até a folha **Aplicativo de API** do aplicativo de API que você criou para hospedar o projeto TodoListAPI (camada intermediária).

2. Na folha **Configurações** do Aplicativo de API, clique em **Configurações do aplicativo**.
 
4. Na folha **Configurações do Aplicativo** do Aplicativo de API, role para baixo até a seção **Configurações do aplicativo** e adicione a chave e o valor a seguir:

	| **Chave** | toDoListDataAPIURL |
	|---|---|
	| **Valor** | https://{your nome do aplicativo de API de camada de dados}.azurewebsites.net |
	| **Exemplo** | https://todolistdataapi0121.azurewebsites.net |

4. Clique em **Salvar**.

	![](./media/app-service-api-dotnet-get-started/asinportal.png)

	Quando o código for executado no Azure, esse valor substituirá a URL de localhost no arquivo Web.config.

### Teste para verificar se ToDoListAPI chama ToDoListDataAPI

11. Em uma janela de navegador, navegue até a URL do novo aplicativo de API da camada intermediária que você acabou de criar (faça isso clicando na URL na folha de principal do aplicativo de API no portal).

13. Adicione "swagger" à URL na barra de endereço do navegador e pressione Enter. (A URL será `http://{apiappname}.azurewebsites.net/swagger`).

	O navegador exibe a mesma interface do usuário do Swagger que você viu anteriormente para ToDoListDataAPI, mas agora `owner` não é um campo obrigatório para a operação Get, pois o aplicativo de API de camada intermediária está enviando esse valor ao aplicativo de API de camada de dados para você. (Quando você executar os tutoriais de autenticação, a camada intermediária enviará as IDs de usuário reais para o parâmetro `owner`; no momento, ela está codificando um asterisco).

12. Experimente o método Get e os outros métodos para validar se o aplicativo de API de camada intermediária está chamando o aplicativo de API de camada de dados com êxito.

	![](./media/app-service-api-dotnet-get-started/midtierget.png)

## <a id="creating"></a> Opcional: Criar um projeto de aplicativo de API do zero

Neste tutorial, você baixará os projetos de API Web do ASP.NET para implantação no Serviço de Aplicativo em vez de criar novos projetos do zero. Para criar um projeto que pretenda implantar em um aplicativo de API, você poderá criar um projeto de API Web típica e instalar o pacote Swashbuckle ou poderá usar o novo modelo de projeto **aplicativo de API do Azure**. Para usar esse modelo, clique em **Arquivo > Novo > Projeto > Aplicativo Web ASP.NET > Aplicativo de API do Azure**.

![](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

O modelo de projeto **Aplicativo de API do Azure** é equivalente a escolher o modelo **Vazio** do ASP.NET 4.5.2, clicar na caixa de seleção para adicionar o suporte a API Web e instalar o pacote Swashbuckle. Além disso, o modelo adiciona alguns códigos de configuração do Swashbuckle projetados para evitar a criação de IDs de operação do Swagger duplicadas.

## Opcional: Alterar um tipo de aplicativo

Conforme explicado anteriormente, a única diferença entre os aplicativos de API, os aplicativos Web e os aplicativos móveis é a forma como eles são representados no portal. Como todos eles têm os mesmos recursos, nunca será necessário alterar um tipo de aplicativo.

No entanto, é fácil alterar a representação do portal. Por exemplo, você poderia mudar um dos aplicativos de API que acabou de criar para um aplicativo Web ao executar as etapas a seguir.

1. Abra o Gerenciador de Recursos.

2. No painel de navegação esquerdo, expanda **assinaturas** e expanda a assinatura em que você tem trabalhado.

4. Expanda **resourceGroups** e expanda o grupo de recursos com que você tem trabalhado.

5. Expanda **Microsoft.Web**, expanda **sites** e selecione o aplicativo de API que deseja alterar.

6. Clique em **Editar**.

8. Encontre a propriedade `kind` e altere-a de "api" para "WebApp".

	![](./media/app-service-api-dotnet-get-started/resexp.png)

9. Clique em **Put**.

10. Acesse o portal do Azure para ver que o ícone foi alterado para refletir o novo tipo de aplicativo.

## Opcional: URL da definição de API nos modelos do Gerenciador de Recursos do Azure

Neste tutorial, você viu a URL de definição de API no Visual Studio e no portal do Azure. Você também pode configurar a URL de definição de API para um aplicativo de API usando os [modelos do Azure Resource Manager](../resource-group-authoring-templates.md) nas ferramentas de linha de comando, como o [Azure PowerShell](../powershell-install-configure.md) e a [CLI do Azure](../xplat-cli-install.md).

Para ver um exemplo de um modelo do Azure Resource Manager que defina a propriedade de definição de API, abra o [arquivo azuredeploy.json no repositório do aplicativo de exemplo deste tutorial](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Localize a seção do modelo que se parece com o exemplo a seguir:

		"apiDefinition": {
		  "url": "https://todolistdataapi.azurewebsites.net/swagger/docs/v1"
		}

## Próximas etapas

Neste tutorial, você viu como criar aplicativos de API, implantar código neles, gerar código cliente para eles e consumi-los usando clientes .NET. O próximo tutorial na série de introdução aos Aplicativos de API mostra como [consumir aplicativos de API de clientes JavaScript usando CORS](app-service-api-cors-consume-javascript.md).

<!---HONumber=AcomDC_0309_2016-->