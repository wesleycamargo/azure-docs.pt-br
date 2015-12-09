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
	ms.date="11/25/2015"
	ms.author="tdykstra"/>

# Introdução aos aplicativos de API e ao ASP.NET no Serviço de Aplicativo do Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Visão geral

Neste tutorial, você criará um [aplicativo de API do Serviço de Aplicativo](app-service-api-apps-why-best-platform.md), implantará uma API Web ASP.NET no aplicativo de API e consumirá o aplicativo de API de um cliente MVC ASP.NET. O tutorial presume que você está familiarizado com o ASP.NET, mas não tem experiência anterior com o Microsoft Azure. Ao concluir o tutorial, você terá uma API Web e um aplicativo cliente em funcionamento na nuvem.

O aplicativo de exemplo é uma lista de contatos simples. A ilustração a seguir mostra como o aplicativo MVC exibe os dados recebidos da API.

![](./media/app-service-api-dotnet-get-started/mvccontacts.png)

## O que você aprenderá

Neste tutorial, você aprenderá:

* Como preparar seu computador para desenvolvimento do Azure instalando o SDK do Azure para .NET.
* Como trabalhar com aplicativos de API e aplicativos Web no Serviço de Aplicativo do Azure usando ferramentas incorporadas ao Visual Studio 2015.
* Como automatizar a descoberta de API usando o pacote NuGet do Swashbuckle para gerar dinamicamente a definição de API do Swagger JSON.
* Como usar código cliente gerado automaticamente para consumir um aplicativo de API de um cliente .NET.
* Como usar o portal do Azure para configurar o ponto de extremidade de metadados do aplicativo de API.

Este é o primeiro de uma série de tutoriais. Os tutoriais seguintes se baseiam no que você criar neste; nos tutoriais, você aprenderá:
 
* Como usar o CORS para chamar um aplicativo de API de um cliente JavaScript quando o cliente é de um domínio diferente da API.
* Como usar o Active Directory do Azure para proteger uma API de acesso não autenticado.
* Como consumir uma API protegida para usuários registrados no Active Directory do Azure.
* Como consumir uma API protegida usando uma entidade de serviço.

## Pré-requisitos

O tutorial presume que você esteja familiarizado com a API Web ASP.NET; se precisar de uma introdução, veja [Introdução à API Web ASP.NET 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).

As instruções e capturas de tela presumem que você esteja usando o Visual Studio 2015, mas a mesma orientação deve funcionar para o Visual Studio 2013.

Você precisa de uma conta do Azure para concluir este tutorial. Você pode:

* [Abrir uma conta do Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F). Obtenha créditos que possam ser usados para experimentar os serviços pagos do Azure. Mesmo depois que os créditos são usados, você pode manter a conta e usar os serviços e recursos do Azure gratuitos, como o recurso de aplicativos Web do Serviço de Aplicativo do Azure.
* [Ativar benefícios de assinante do Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). - todos os meses, sua assinatura do MSDN lhe oferece créditos que podem ser usados para serviços pagos do Azure.

Se você quiser ter uma introdução ao Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751). Lá, você poderá criar imediatamente um aplicativo de curta duração inicial no Serviço de Aplicativo — sem exigência de cartão de crédito e sem compromissos.

[AZURE.INCLUDE [install-sdk-2015-2013](../../includes/install-sdk-2015-2013.md)]

Este tutorial requer a versão 2.8.1 ou posterior do SDK do Azure para .NET.

## Visão geral do aplicativo de exemplo

O código que você implantará em um aplicativo de API e em um aplicativo Web neste tutorial está no repositório GitHub [Azure-Samples/app-service-api-dotnet-contact-list](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list). A solução ContactsList Visual Studio inclui os seguintes projetos:

* **ContactsList.API** – projeto de uma API Web ASP.NET que retorna uma lista de nomes e endereços de email. A chamada inicial para o método Get retorna três contatos embutidos em código e as chamadas seguintes para os métodos Put, Post e Delete salvam alterações em um arquivo JSON local.
* **ContactsList.MVC** - cliente MVC ASP.NET para a API ContactsList.
* **ContactsList.Angular** - cliente simples da interface do usuário do AngularJS para a API ContactsList. Demonstra como chamar um aplicativo de API desprotegido (sem autenticação).
* **ContactsList.Angular.AAD** - cliente AngularJS que mostra como usar o Active Directory do Azure para autenticar usuários.
* **CompanyContacts.API** - projeto de uma API Web ASP.NET que retorna uma lista de contatos codificada em resposta a uma solicitação Get. Chamando pelo método Get **ContactsList.API** para demonstrar como chamar uma API usando a autenticação serviço a serviço (entidade de serviço).
 
## Baixar o aplicativo de exemplo 

1. Baixe o repositório [Azure-Samples/app-service-api-dotnet-contact-list](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list).

	Você pode [baixar um arquivo .zip](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list/archive/master.zip) ou clonar o repositório em seu computador local.

2. Abra a solução ContactsList no Visual Studio 2015 ou 2013.

2. Compile o projeto para restaurar os pacotes NuGet.

## Usar o Swashbuckle e o Swagger durante a execução local

O [Swagger](http://swagger.io/) é um formato JSON para descrever métodos e respostas de uma API. O suporte ao Swagger 2.0 é interno ao Serviço de Aplicativo do Azure. Como você verá posteriormente no tutorial, se fornecer metadados do Swagger para uma API, o Visual Studio poderá gerar código cliente para facilitar o consumo da API.

Para fornecer metadados do Swagger 2.0 a um projeto de API Web ASP.NET, você poderá instalar o pacote NuGet [Swashbuckle](https://www.nuget.org/packages/Swashbuckle). O Swashbuckle usa a Reflexão para gerar os metadados dinamicamente. O pacote NuGet já está instalado no projeto ContactsList.API que você baixou e já estará instalado quando um novo projeto for criado usando o modelo de projeto **Aplicativo de API do Azure**.

Nesta seção do tutorial, você verá os metadados do Swagger 2.0 gerados e experimentará uma interface do usuário de teste baseada nos metadados do Swagger.

2. Defina o projeto ContactsList.API como projeto de inicialização. (Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e clique em **Definir como Projeto de Inicialização** no menu de contexto).
 
4. Pressione F5 para executar o projeto no modo de depuração.

	O navegador abre mostrando a página 403 Proibido.

	![](./media/app-service-api-dotnet-get-started/403.png)

12. Na barra de endereços do navegador, adicione `swagger/docs/v1` ao final da linha e pressione Return. (A URL será `http://localhost:51864/swagger/docs/v1`).

	Essa é a URL padrão usada pelo Swashbuckle para retornar metadados JSON do Swagger 2.0 para a API. Se você estiver usando o Internet Explorer, o navegador solicitará que baixe um arquivo v1.json.

	![](./media/app-service-api-dotnet-get-started/iev1json.png)

	Se você estiver usando o Chrome, o navegador exibirá o JSON na própria janela.

	![](./media/app-service-api-dotnet-get-started/chromev1json.png)

	O exemplo a seguir mostra a primeira seção dos metadados do Swagger para a API, com a definição para o método Get. Esses metadados geram a interface do usuário do Swagger que você usará nas etapas a seguir e em uma seção posterior do tutorial para gerar o código cliente automaticamente.

		{
		  "swagger": "2.0",
		  "info": {
		    "version": "v1",
		    "title": "ContactsList.API"
		  },
		  "host": "localhost:51864",
		  "schemes": [ "http" ],
		  "paths": {
		    "/api/Contacts": {
		      "get": {
		        "tags": [ "Contacts" ],
		        "operationId": "Contacts_Get",
		        "consumes": [ ],
		        "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
		        "responses": {
		          "200": {
		            "description": "OK",
		            "schema": {
		              "type": "array",
		              "items": { "$ref": "#/definitions/Contact" }
		            }
		          }
		        },
		        "deprecated": false
		      },

1. Feche o navegador.

3. No projeto ContactsList.API no **Gerenciador de Soluções**, abra o arquivo *App\_Start\\SwaggerConfig.cs*, role para baixo até o código a seguir e remova os comentários dele.

		/*
		    })
		.EnableSwaggerUi(c =>
		    {
		*/

	O arquivo SwaggerConfig.cs é criado quando você instala o pacote do Swashbuckle em um projeto. O arquivo fornece várias maneiras de se configurar o Swashbuckle.

	O código que você removeu habilita a interface do usuário do Swagger que será usada nas etapas a seguir. Quando você cria um projeto de API Web usando o modelo de projeto de aplicativo de API, o código é comentado por padrão como medida de segurança.

5. Execute o projeto novamente.

3. Na barra de endereços do navegador, adicione `swagger` ao final da linha e pressione Return. (A URL será `http://localhost:51864/swagger`).

4. Na página de interface do usuário do Swagger, clique em **Contatos** para ver os métodos disponíveis.

	![](./media/app-service-api-dotnet-get-started/contactsmethods.png)

5. Clique em **Obter > Experimentar**.

	A interface do usuário do Swagger chama o método ContactsList e exibe os resultados JSON.

	![](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. Clique em **Postar** e marque a caixa em **Esquema de Modelo**.

	Clicar no esquema de modelo preencherá previamente a caixa de entrada onde é possível especificar o valor do parâmetro para o método Post.

	![](./media/app-service-api-dotnet-get-started/post.png)

7. Altere o JSON na caixa de entrada do parâmetro `contact` para que ele se pareça com o exemplo a seguir, ou substitua por seu próprio nome e endereço de email:

		{
		  "CreatedBy": "",
		  "EmailAddress": "carson@contoso.com",
		  "Id": 4,
		  "Name": "Alexander Carson"
		} 

10. Clique em **Experimentar**.

	A API ContactsList retorna um HTTP 200 e um corpo de resposta que confirma a adição.

11. Clique em **Obter > Experimentar**.

	A resposta do método Get agora inclui o novo contato.

12. Experimente também os métodos Put, Delete e Get by ID e feche o navegador.

O Swashbuckle funciona com qualquer projeto de API Web ASP.NET. Se você deseja adicionar a geração de metadados de Swagger a um projeto existente, instale o pacote Swashbuckle. Se você deseja criar um novo projeto a ser implantado como um aplicativo de API do Serviço de Aplicativo, use o modelo de projeto ASP.NET para o **aplicativo de API do Azure**. Esse modelo cria um projeto de API Web com o Swashbuckle instalado.

![](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

## Criar um aplicativo de API no Azure e implantar o projeto ContactsList.API nele

Nesta seção, use as ferramentas do Azure integradas ao assistente **Publicar Web** do Visual Studio para criar um novo aplicativo de API no Azure. Em seguida, implante o projeto ContactsList.API no novo aplicativo de API e chame a API executando a interface do usuário do Swagger novamente, desta vez enquanto ele estiver sendo executado na nuvem.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ContactsList.API e clique em **Publicar**.

3.  Na etapa **Perfil** do assistente **Publicar Web**, clique em **Serviço de Aplicativo do Microsoft Azure**.

	![](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. Entre em sua conta do Azure, se ainda não tiver feito isso, ou atualize suas credenciais se elas tiverem expirado.

4. Na caixa de diálogo Serviço de Aplicativo, escolha a **Assinatura** do Azure que você deseja usar e clique em **Novo**.

	![](./media/app-service-api-dotnet-get-started/clicknew.png)

3. Na guia **Hospedagem** da caixa de diálogo **Criar Serviço de Aplicativo**, clique em **Alterar Tipo** e em **Aplicativo de API**.

4. Insira um **nome de aplicativo de API** que seja exclusivo em todo o domínio *azurewebsites.net*.

	O Visual Studio sugere um nome exclusivo acrescentando a cadeia de caracteres de data e hora ao nome do projeto. Se preferir, você poderá aceitar esse nome.

	Se você inserir um nome usado por outra pessoa, verá um ponto de exclamação vermelho à direita em vez de uma marca de verificação verde e precisará inserir outro nome de site.

	O Azure usará esse nome como o prefixo para a URL do aplicativo. A URL completa consistirá nesse nome mais *.azurewebsites.net*. Por exemplo, se o nome for `ContactsListAPI`, a URL será `contactslistapi.azurewebsites.net`.

6. Na lista suspensa **Grupo de Recursos**, insira "ContactsListGroup" ou outro nome, se preferir.

	Essa caixa permite que você selecione um [grupo de recursos](../azure-preview-portal-using-resource-groups.md) existente ou crie um novo digitando um nome diferente de qualquer grupo de recursos existente na assinatura.

	Para este tutorial, é melhor criar um novo porque facilita a exclusão de todos os recursos do Azure criados para o tutorial em uma única etapa.

4. Clique no botão **Novo** ao lado da lista suspensa **Plano do Serviço de Aplicativo**.

	![](./media/app-service-api-dotnet-get-started/createas.png)

	Para saber mais sobre os planos do Serviço de Aplicativo, confira [Visão geral dos planos do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

5. No diálogo **Configurar o Plano de Serviço de Aplicativo**, insira "ContactsListPlan" ou outro nome, se preferir.

5. Na lista suspensa **Local**, escolha o local mais próximo de você.

	Essa configuração especifica em qual datacenter do Azure o aplicativo será executado. Para este tutorial, você pode selecionar qualquer região e isto não fará uma diferença notável. Mas para um aplicativo de produção, o ideal é que seu servidor esteja tão próximo quanto possível dos clientes que estão acessando-os a fim de reduzir a [latência](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Na lista suspensa **Tamanho**, clique em **Gratuito**.

	Neste tutorial, o tipo de preço gratuito permitirá o desempenho suficiente.

6. No diálogo **Configurar o Plano do Serviço de Aplicativo**, clique em **OK**.

	![](./media/app-service-api-dotnet-get-started/configasp.png)

7. Na caixa de diálogo **Criar Serviço de Aplicativo**, clique em **Criar**.

	O Visual Studio cria o aplicativo de API e um perfil de publicação que tem todas as configurações necessárias para o novo aplicativo de API. Nas etapas a seguir, você usará o novo perfil de publicação para implantar o projeto.
 
	Observação: existem outras maneiras de criar aplicativos de API no Serviço de Aplicativo do Azure. No Visual Studio, os mesmos diálogos estão disponíveis durante a criação de um novo projeto. Você também pode criar aplicativos de API usando o portal do Azure, [cmdlets do Azure para o Windows PowerShell](../powershell-install-configure.md) ou a [interface de linha de comando de plataforma cruzada](../xplat-cli.md).

8. Na guia **Conexão** do assistente **Publicar Web**, clique em **Publicar**.

	![](./media/app-service-api-dotnet-get-started/clickpublish.png)

	O Visual Studio implanta o projeto ContactsList.API no novo aplicativo de API e abre um navegador para a URL do aplicativo de API. Uma página "criado com êxito" será exibida no navegador.

	![](./media/app-service-api-dotnet-get-started/appcreated.png)

11. Adicione "swagger" à URL na barra de endereço do navegador e pressione Enter. (A URL será `http://{apiappname}.azurewebsites.net/swagger`).

	O navegador exibe a mesma interface do usuário do Swagger que você viu anteriormente, mas agora ela está em execução na nuvem. Experimente o método Get e você verá que voltará aos três contatos padrão, pois as alterações feitas anteriormente foram salvas em um arquivo local. Qualquer alteração feita agora será salva no sistema de arquivos do aplicativo de API do Azure.

12. Abra o [Portal do Azure](https://portal.azure.com/).
 
14. Clique em **Procurar > Aplicativos de API > {seu novo aplicativo de API}**.

	![](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

16. Clique em **Configurações** e, na folha **Configurações**, localize a seção de API e clique em **Definição de API**.

	![](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

	A folha Definição de API permite que você especifique a URL para retornar metadados do Swagger 2.0 no formato JSON. Quando o Visual Studio cria o aplicativo de API, ele define a URL de definição de API com o valor padrão que vimos anteriormente, que é a URL base do aplicativo de API mais `/swagger/docs/v1`.

	![](./media/app-service-api-dotnet-get-started/apidefurl.png)

	Quando você seleciona um aplicativo de API para gerar código, o Visual Studio recupera os metadados dessa URL.

## <a id="codegen"></a> Consumir do .NET usando código de cliente gerado 

Uma das vantagens da integração do Swagger a aplicativos de API do Azure é a geração automática de código. As classes de cliente geradas tornam mais fácil escrever código para chamar um aplicativo de API.

Nesta seção, você verá como consumir um aplicativo de API de um aplicativo Web MVC ASP.NET. Você executará o cliente MVC e a API Web localmente primeiro, implantará o cliente em um novo aplicativo Web no Serviço de Aplicativo do Azure e o executará na nuvem.

### Gerar código de cliente

Você pode gerar o código cliente para um aplicativo de API usando o Visual Studio ou a linha de comando. Para este tutorial, você usará o Visual Studio. Para saber mais sobre como fazer isso na linha de comando, confira o arquivo Leiame do repositório [Azure/autorest](https://github.com/azure/autorest) no GitHub.com.

O projeto ContactsList.MVC já tem o código cliente gerado, mas você o excluirá e o recriará para ver como isso é feito e para tornar a URL do seu próprio aplicativo de API a URL de destino padrão.

1. No **Gerenciador de Soluções** do Visual Studio, no projeto ContactsList.MVC, exclua a pasta *ContactsList.API*.

	![](./media/app-service-api-dotnet-get-started/deletecodegen.png)

2. Clique com botão direito do mouse no projeto ContactsList.MVC e clique em **Adicionar > Cliente da API REST**.

	![](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. Na caixa de diálogo **Adicionar Cliente da API REST**, clique em **Baixar do Aplicativo de API do Microsoft Azure** e em **Procurar**.

	![](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. Na caixa de diálogo **Serviço de Aplicativo**, expanda o grupo de recursos **ContactsListGroup**, selecione seu aplicativo de API e clique em **OK**.

	Essa caixa de diálogo permite mais de uma forma de organização de aplicativos de API na lista, caso você tenha muito muitos para percorrer. Ele também permite inserir uma cadeia de caracteres de pesquisa para filtrar os aplicativos de API pelo nome.

	![](./media/app-service-api-dotnet-get-started/codegenselect.png)

	Observe que, quando você retornar para o diálogo **Adicionar Cliente da API REST**, a caixa de texto terá sido preenchida com o valor de URL de definição de API vista anteriormente no portal.

	![](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

	Como alternativa, você pode inserir a URL diretamente em vez de passar pelo diálogo Procurar. Por exemplo, se você tiver implantado sua API em um aplicativo Web e ele não apareceu na caixa de diálogo Procurar, poderá inserir manualmente a URL que retorna metadados Swagger aqui.

	Observe também a opção **Selecionar um arquivo de metadados do Swagger existente**. Se você quiser gerar código antes de implantar no Azure, poderá executar localmente, baixar o arquivo JSON do Swagger e selecioná-lo aqui.

9. Na caixa de diálogo **Adicionar Cliente da API REST**, clique em **OK**.

	O Visual Studio cria uma pasta com o nome do aplicativo de API e gera classes de cliente.

	![](./media/app-service-api-dotnet-get-started/codegenfiles.png)

5. Abra *Controllers\\ContactsController.cs* para ver o código que chama a API usando o cliente gerado.

	O trecho a seguir mostra como instanciar o objeto de cliente e chamar o método Get.

		private ContactsListAPI db = new ContactsListAPI(new Uri("http://localhost:51864"));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

	Esse código passa para a URL do IIS Express local do projeto da API e para o construtor de classe de cliente a fim de que você possa executar o projeto Web do MVC e o projeto de API localmente. Se você omitir o parâmetro do construtor, o ponto de extremidade padrão será a URL da qual você gerou o código.

6. A classe de cliente será gerada com um nome diferente baseado no nome de seu aplicativo de API. Altere o código para que o nome do tipo corresponda ao que foi gerado em seu projeto.

7. Compilar a solução.

O controlador e os modos de exibição de projeto MVC são parecidos com o controlador e os modos de exibição com scaffolding para o Entity Framework porque é como eles foram criados, realizando o scaffolding de um modelo de dados do Entity Framework e fazendo alterações secundárias para usar o cliente da API REST em vez de um contexto de banco de dados do Entity Framework.

### Executar localmente

1. Defina os projetos ContactsList.API e ContactsList.MVC como projetos de inicialização, com ContactsList.API iniciando antes de ContactsList.MVC. (Clique com o botão direito do mouse na solução, clique em **Propriedades**, clique em **Vários projetos de inicialização** e defina cada projeto como **Iniciar**. Use os ícones de seta para cima/para baixo a fim de colocar ContactsList.API como o primeiro da lista). 

2. Pressione F5 para iniciar os projetos.

	Um navegador exibe a página 403 para a API e outro exibe a home page do aplicativo MVC.

3. Clique em **Contatos** na barra de menus do navegador que exibe a página inicial do aplicativo MVC.

	A interface do usuário do MVC exibe os contatos armazenados localmente; você pode usá-la para adicionar e excluir contatos.

	![](./media/app-service-api-dotnet-get-started/mvccontacts.png)

### Criar um aplicativo Web no Azure e implantar o projeto ContactsList.MVC nele

Nesta seção, você usa o mesmo método para criar um aplicativo Web usado anteriormente para criar um aplicativo de API, e o mesmo método para implantar um projeto Web no aplicativo Web do Azure.

#### Alterar o projeto MVC para apontar para o aplicativo de API do Azure 

Antes de implantar no Azure, altere o ponto de extremidade de API no projeto MVC para que, quando o código for implantado, chame o aplicativo de API do Azure que você criou anteriormente em vez do localhost.

1. No projeto ContactsList.MVC, abra *Controllers\\ContactsController.cs*.

2. Comente a linha que define a URL base da API como a URL de localhost, remova o comentário da linha que não tem parâmetros de construtor. O código agora se parece com o exemplo a seguir, mas ambas as linhas devem ter o nome de classe refletindo o nome do aplicativo de API do qual você gerou o código.

		private ContactsListAPI db = new ContactsListAPI();
		//private ContactsListAPI db = new ContactsListAPI(new Uri("http://localhost:51864"));

	A URL de destino padrão é o seu aplicativo de API do Azure porque o código é gerado a partir dele; se você tiver usado um método diferente para gerar o código, talvez tenha de especificar a URL do aplicativo de API do Azure da mesma maneira que especificou a URL local.

#### Criar um aplicativo Web para hospedar o site do MVC

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ContactsList.MVC e clique em **Publicar**.

2. No assistente **Publicar Web**, clique na guia **Perfil**.

3.  Na guia **Perfil** do assistente **Publicar Web**, clique em **Serviço de Aplicativo do Microsoft Azure**.

5. Na caixa de diálogo **Serviço de Aplicativo**, clique em **Novo**.

3. Na guia **Hospedagem** da caixa de diálogo **Criar Serviço de Aplicativo**, clique em **Alterar Tipo** e não deixe de definir o tipo como **Aplicativo Web**.

4. Insira um **nome de aplicativo Web** que seja exclusivo em todo o domínio *azurewebsites.net*.

5. Escolha a **assinatura** do Azure com a qual você deseja trabalhar.

6. Na lista suspensa **Grupo de Recursos**, escolha o mesmo grupo de recursos que você criou anteriormente.

4. Na lista suspensa **Plano do Serviço de Aplicativo**, escolha o mesmo plano criado anteriormente.

7. Clique em **Criar**.

	O Visual Studio cria o aplicativo Web, cria um perfil de publicação para ele e exibe a etapa **Conexão** do assistente **Publicar Web**.

### Implantar o projeto ContactsList.Web no novo aplicativo Web

3.  Na etapa **Conexão** do assistente **Publicar Web**, clique em **Publicar**.

	O Visual Studio implanta o projeto ContactsList.MVC no novo aplicativo Web e abre um navegador para a URL do aplicativo Web. Será mostrada a mesma interface do usuário do MVC que você viu em execução localmente, mas agora ela mostra os contatos armazenados no sistema de arquivos do aplicativo de API do Azure.

	![](./media/app-service-api-dotnet-get-started/codegencontacts.png)

## Próximas etapas

Neste tutorial, você viu como criar aplicativos de API, implantar código neles e consumi-los usando clientes .NET. O tutorial a seguir na série de introdução mostra como [consumir aplicativos de API de clientes JavaScript usando CORS](app-service-api-cors-consume-javascript.md).

<!---HONumber=AcomDC_1203_2015-->