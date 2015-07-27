<properties
   pageTitle="Criar um aplicativo API ASP.NET 5 no Visual Studio Code"
   description="Este tutorial mostra como criar um aplicativo de API ASP.NET 5 usando o Visual Studio Code."
   services="app-service\api"
   documentationCenter=".net"
   authors="erikre"
   manager="wpickett"
   editor="jimbe"/>

<tags
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="erikre"/>

# Criar um aplicativo API ASP.NET 5 no Visual Studio Code

> [AZURE.SELECTOR]
- [Visual Studio 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio 2015 RC](app-service-dotnet-create-api-app-vs2015.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## Visão geral

Este tutorial mostra como criar um aplicativo de API ASP.NET 5 usando o [Visual Studio Code](http://code.visualstudio.com//Docs/whyvscode). O ASP.NET 5 é uma reestruturação significativa do ASP.NET. ASP.NET 5 é uma nova estrutura de código-fonte aberto entre plataformas para criar modernos aplicativos baseados em nuvem da Web usando o .NET. Para obter mais informações, consulte [Introdução ao ASP.NET 5](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html). Para obter informações sobre aplicativos de API, consulte [O que são aplicativos de API?](app-service-api-apps-why-best-platform.md)

> [AZURE.NOTE]Para concluir este tutorial, você precisa de uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [inscrever-se para uma avaliação gratuita](/pricing/free-trial/) ou [ativar seus benefícios de assinante MSDN](/pricing/member-offers/msdn-benefits-details/). Você também pode experimentar gratuitamente os [Exemplos de Aplicativo do Serviço de Aplicativo](http://tryappservice.azure.com).

## Pré-requisitos  

* Instale e configure o [Visual Studio Code](http://code.visualstudio.com/Docs/setup).
* Instale o [Node.js](http://nodejs.org/download/).<br\> 
	O [Nó](http://nodejs.org/) é uma plataforma para a criação rápida e dimensionável de aplicativos de servidor usando JavaScript. O Nó é o tempo de execução (nó) e [npm](http://www.npmjs.com/) é o Gerenciador de Pacotes para os módulos do Nó. Você usará o npm para criar o scaffolding de um aplicativo de API 5 ASP.NET neste tutorial.

## Instalar o ASP.NET 5 e DNX
O ASP.NET 5/DNX é uma pilha enxuta do .NET para criar aplicativos da Web e de nuvem modernos e executados em OS X, Linux e Windows. Ele foi criado do zero para fornecer uma estrutura de desenvolvimento otimizada para aplicativos que são implantados na nuvem ou então executados localmente. Ele consiste em componentes modulares com sobrecarga mínima, para que você mantenha a flexibilidade durante a construção de suas soluções.

> [AZURE.NOTE]O ASP.NET 5 e o DNX (o ambiente de execução do .NET) em OS X e Linux estão em um estado inicial Beta/de Visualização.

Este tutorial é projetado para começar a criar aplicativos com as versões de desenvolvimento ASP.NET 5 e DNX mais recentes. Se você quiser uma experiência mais estável e liberada, vá para [http://www.asp.net/vnext](http://www.asp.net/vnext). As instruções a seguir são específicas do Windows. Para obter instruções de instalação mais detalhadas para OS X, Linux e Windows, consulte [Instalação do ASP.NET 5 e DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

1. Para instalar o Gerenciador de Versão do .NET (DNVM) no Windows, execute o comando a seguir na Janela de Comando:

	<pre class="prettyprint">
	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "&amp;{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"
	</pre>

	Isso baixará o script DNVM e o colocará em seu perfil do usuário.

2. Talvez seja necessário fazer logoff depois de inserir o comando acima para que a alteração para a variável de ambiente PATH entre em vigor.
3. Verifique o local do DNVM executando o seguinte na Janela de Comando: 

	<pre class="prettyprint">
	where dnvm
	</pre>

	A Janela de Comando mostrará um caminho semelhante ao seguinte:

	![local dnvm](./media/app-service-create-aspnet-api-app-using-vscode/00-where-dnvm.png)

4. Agora que você tem o DNVM, você precisa usá-lo para baixar o DNX, para então executar os aplicativos. Execute o comando a seguir na Janela de Comando:

	<pre class="prettyprint">
	dnvm upgrade
	</pre>

5. Verifique o DNVM e visualize o tempo de execução ativo digitando o seguinte na Janela de Comando:

	<pre class="prettyprint">
	dnvm list
	</pre>

	A Janela de Comando mostrará os detalhes do tempo de execução ativo:

	![local dnvm](./media/app-service-create-aspnet-api-app-using-vscode/00b-dnvm-list.png)

Para obter instruções de instalação mais detalhadas para OS X, Linux e Windows, consulte [Instalação do ASP.NET 5 e DNX](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx).

## Criar o Aplicativo de API 

Esta seção mostra como criar o scaffolding de um novo aplicativo de API do ASP.NET do aplicativo. Você usará o gerenciador de pacotes do nó (npm) para instalar o [Yeoman](http://yeoman.io/), [Grunt](http://gruntjs.com/) e [Bower](http://bower.io/).

1. Depois de instalar o Visual Studio Code e o Node.js, abra um prompt de comando com direitos de administrador e navegue até o local onde você deseja que fiquem todos os seus projetos do ASP.NET usando VSCode.
2. Digite o seguinte na janela de comando para instalar as ferramentas de suporte e o Yeoman:

	<pre class="prettyprint">
	npm install -g yo grunt-cli generator-aspnet bower
	</pre>

3. Digite o seguinte na janela de comando para criar a pasta do projeto e o scaffolding do aplicativo:

	<pre class="prettyprint">
	yo aspnet
	</pre>

4. Siga as instruções fornecidas pelo gerador realizando a rolagem e selecionando o tipo **Aplicativo de API da Web**.

	![Yoman - gerador ASP.NET 5](./media/app-service-create-aspnet-api-app-using-vscode/01-yo-aspnet.png)

5. Defina o nome do seu novo aplicativo de API do ASP.NET como **ContactsList**. Esse nome será usado no código fornecido posteriormente neste tutorial. <br>
	 Yoman criará uma nova pasta chamada **ContactsList** e os arquivos necessários para o novo aplicativo.
6. Abra o **Visual Studio Code**.<br> Você pode abrir VSCode da janela de comando digitando **code .**.
7. Do menu **Arquivo**, selecione **Abrir pasta** e selecione a pasta onde seu aplicativo de API do ASP.NET está localizado.

	![caixa de diálogo Selecionar Pasta](./media/app-service-create-aspnet-api-app-using-vscode/02-open-folder.png)

	O VSCode carregará o projeto e o exibirá na janela o **Explorar**.

	![VSCode exibindo o projeto de Contato](./media/app-service-create-aspnet-api-app-using-vscode/03-vscode-contacts-project.png)

8. Em **VSCode**, no menu **Exibição**, selecione **Paleta de Comandos**.
9. Em **Paleta de Comandos**, digite os comandos a seguir:

	<pre class="prettyprint">
	dnx:dnu restore - (ContactsList)
	</pre>
	Conforme você começa a digitar, você verá a linha de comando completa na lista.

	![Comando Restore](./media/app-service-create-aspnet-api-app-using-vscode/04-dnu-restore.png)

	O comando Restore instala os pacotes do NuGet necessários para executar o aplicativo. Uma janela de comando exibirá **Restauração concluída** quando a restauração estiver pronta.

## Modificar o Aplicativo de API

Agora você modificará o aplicativo **ContactsList** adicionando uma classe **Contact** e uma classe **ContactsController**.

1. Coloque o cursor sobre o nome do projeto **ContactsList** e adicione uma nova pasta chamada *Modelos*, usando o ícone de nova pasta.

	![pasta Novos Modelos](./media/app-service-create-aspnet-api-app-using-vscode/07-new-folder.png)

2. Clique com o botão direito do mouse na pasta **Modelos** para adicionar um novo arquivo de classe chamado *Contact.cs* com o código a seguir:

	<pre class="prettyprint">
	namespace ContactsList.Models
	{
	    public class Contact
	    {
	        public int Id { get; set; }
	        public string Name { get; set; }
	        public string EmailAddress { get; set; }
	    }
	}
	</pre>

3. Clique com o botão direito do mouse na pasta **controladores** e adicione um arquivo *ContactsController.cs*, para que ele apareça da seguinte maneira:

	<pre class="prettyprint">
	using System.Collections.Generic;
	using Microsoft.AspNet.Mvc;
	using ContactsList.Models;
	
	namespace ContactsList.Controllers
	{
	    [Route("api/[controller]")]
	    public class ContactsController : Controller
	    {
	        // GET: api/Contacts
	        [HttpGet]
	        public IEnumerable&lt;Contact&gt; Get()
	        {
	            return new Contact[]{
	                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
	                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
	                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
	            };
	        }
	    }
	}
	</pre>

4. Assegure que todos os arquivos estejam salvos, selecionando **Arquivo** > **Salvar Tudo**.
5. Na **Paleta de Comandos**, digite o seguinte para executar o comando localmente:

	<pre class="prettyprint">
	dnx: kestrel - (ContactsList, Microsoft.AspNet.Hosting --server Kestrel --server.urls http://localhost:5001
	</pre>
	A janela de comando exibirá a mensagem *Iniciado*. Se a janela de comando não exibir a mensagem *Iniciado*, verifique o canto inferior esquerdo de VSCode em busca de erros em seu projeto.

5. Abra uma janela de navegador e navegue até a seguinte URL:

	**http://localhost:5001/api/Contacts**

	Você pode então exibir o conteúdo de *Contacts.json*. Quando você exibir o arquivo, verá o seguinte conteúdo:

	![Conteúdo json retornado](./media/app-service-create-aspnet-api-app-using-vscode/08-contacts-json.png)

## Modificar metadados do aplicativo de API
Os metadados que habilitam um projeto de API ASP.NET a ser implantado como um aplicativo de API estão contidos em um arquivo *apiapp.json* na raiz do projeto.

1. No VSCode, clique com o botão direito do mouse na pasta *wwwroot* e selecione a opção **Novo Arquivo**.
2. Nomeie o novo arquivo *apiapp.json*. <br\>
	 Certifique-se de que *apiapp.json* está na pasta *wwwroot*.
3. Adicione o seguinte ao arquivo *apiapp.json*:

	<pre class="prettyprint">
	{
	    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
	    "id": "ContactsList",
	    "namespace": "microsoft.com",
	    "gateway": "2015-01-14",
	    "version": "1.0.0",
	    "title": "ContactsList",
	    "summary": "",
	    "author": "",
	    "endpoints": null
	}
	</pre>

No arquivo *apiapp.json*, você pode especificar um ponto de extremidade para JSON de definição de API Swagger dinâmica, mas para este tutorial, você usará um arquivo de definição de API estática. Para obter um exemplo que usa a geração Swagger dinâmica, consulte [Configurar um projeto de API da Web como um aplicativo de API](app-service-dotnet-create-api-app-visual-studio.md).

## Adicionar definição de API Swagger estática
Para fornecer um arquivo de definição de API Swagger 2.0 estática, você precisa criar uma pasta na raiz da Web e posicionar um arquivo de definição de API nessa pasta.

1. Em VSCode, crie uma pasta chamada *metadados* na pasta *wwwroot*.
2. Clique com o botão direito do mouse na nova pasta *metadados* e adicione um novo arquivo chamado *apiDefinition.swagger.json*. 
3. Adicione a sintaxe json a seguir para o novo arquivo:

	<pre class="prettyprint">
	{
	  "swagger": "2.0",
	  "info": {
	    "version": "v1",
	    "title": "ContactsList"
	  },
	  "host": "DEVE SUBSTITUIR PELA URL DO SEU HOST",
	  "schemes": [
	    "https"
	  ],
	  "paths": {
	    "/api/Contacts": {
	      "get": {
	        "tags": [
	          "Contacts"
	        ],
	        "operationId": "Contacts_Get",
	        "consumes": [],
	        "produces": [
	          "application/json",
	          "text/json",
	          "application/xml",
	          "text/xml"
	        ],
	        "responses": {
	          "200": {
	            "description": "OK",
	            "schema": {
	              "type": "array",
	              "items": {
	                "$ref": "#/definitions/Contact"
	              }
	            }
	          }
	        },
	        "deprecated": false
	      },
	      "post": {
	        "tags": [
	          "Contacts"
	        ],
	        "operationId": "Contacts_Post",
	        "consumes": [
	          "application/json",
	          "text/json",
	          "application/xml",
	          "text/xml",
	          "application/x-www-form-urlencoded"
	        ],
	        "produces": [
	          "application/json",
	          "text/json",
	          "application/xml",
	          "text/xml"
	        ],
	        "parameters": [
	          {
	            "name": "contact",
	            "in": "body",
	            "required": true,
	            "schema": {
	              "$ref": "#/definitions/Contact"
	            }
	          }
	        ],
	        "responses": {
	          "200": {
	            "description": "OK",
	            "schema": {
	              "$ref": "#/definitions/Object"
	            }
	          }
	        },
	        "deprecated": false
	      }
	    }
	  },
	  "definitions": {
	    "Contact": {
	      "type": "object",
	      "properties": {
	        "Id": {
	          "format": "int32",
	          "type": "integer"
	        },
	        "Name": {
	          "type": "string"
	        },
	        "EmailAddress": {
	          "type": "string"
	        }
	      }
	    },
	    "Object": {
	      "type": "object",
	      "properties": {}
	    }
	  }
	}
	</pre>

Mais adiante neste tutorial, você substituirá a cadeia de caracteres de espaço reservado de URL do host acima pela sua URL de host do Azure que você criará e copiar posteriormente.

## Criar um aplicativo de API no Portal de Visualização do Azure

> [AZURE.NOTE]Para concluir este tutorial, você precisa de uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [inscrever-se para uma avaliação gratuita](/pricing/free-trial/) ou [ativar seus benefícios de assinante MSDN](/pricing/member-offers/msdn-benefits-details/). Você também pode experimentar gratuitamente os [Exemplos de Aplicativo do Serviço de Aplicativo](http://tryappservice.azure.com).

1. Faça logon no [Portal de Visualização do Azure](https://portal.azure.com).

2. Clique em **NOVO** na parte superior esquerda do portal.

3. Clique em **Web + Móvel > Aplicativo de API**.

	![Novo Aplicativo de API do Azure](./media/app-service-create-aspnet-api-app-using-vscode/09-azure-newapiapp.png)

4. Insira um valor para **Nome**, como ContactsList.

5. Selecione um plano de Serviço de Aplicativo ou crie um plano novo. Se você criar um novo plano, selecione a camada de preços, localização e outras opções.

	![Folha Novo Aplicativo de API do Azure](./media/app-service-create-aspnet-api-app-using-vscode/10-azure-newappblade.png)

6. Clique em **Criar**.

	![Folha Aplicativo de API](./media/app-service-create-aspnet-api-app-using-vscode/11-azure-apiappblade.png)

	Se você deixou a caixa de seleção **Fixar no Quadro Inicial** marcada ao criar o aplicativo, você pode localizar facilmente o aplicativo clicando em **Início** ou **Procurar**. Se você desmarcou a caixa de seleção, clique em **Notificações** na esquerda para ver o status de criação do aplicativo de API e clique na notificação para ir até a folha do novo aplicativo de API.

7. Clique em **Configurações > Configurações do aplicativo**.

8. Defina o nível de acesso como **Público (anônimo)**.

9. Clique em **Salvar**.

	![Configurações do Aplicativo do Azure](./media/app-service-create-aspnet-api-app-using-vscode/12-azure-appsettings.png)

## Habilitar a publicação de Git no novo aplicativo de API

O Git é um sistema de controle de versão distribuído que você pode usar para implantar seu Site do Azure. Você armazenará o código que você escreve para seu aplicativo de API em um repositório Git local e você implantará seu código no Azure enviando-o por push para um repositório remoto. Esse método de implantação é um recurso dos aplicativos Web do serviço de aplicativo que pode ser usado em um aplicativo de API, já que aplicativos de API se baseiam em aplicativos Web: um aplicativo de API no serviço de aplicativo do Azure é um aplicativo Web com recursos adicionais para a hospedagem de serviços da Web.

No portal, você gerencia os recursos específicos dos aplicativos de API na folha **Aplicativo de API** e gerencia os recursos que são compartilhados com os aplicativos Web na folha **Host de aplicativo de API**. Portanto, nesta seção, você vai até a folha **Host de aplicativo de API** para configurar o recurso de implantação do Git.

1. Na folha Aplicativo de API, clique em **Host de aplicativo de API**.

	![Host de Aplicativo de API do Azure](./media/app-service-create-aspnet-api-app-using-vscode/13-azure-apiapphost.png)

2. Encontre a seção **Implantação** da folha **Aplicativo de API** e clique em **Configurar implantação contínua**. Talvez seja necessário rolar para baixo para ver essa parte da folha.

	![Host de Aplicativo de API do Azure](./media/app-service-create-aspnet-api-app-using-vscode/14-azure-deployment.png)

3. Clique em **Escolher fonte > Repositório Git local**.

5. Clique em **OK**.

	![Repositório Git Local do Azure](./media/app-service-create-aspnet-api-app-using-vscode/15-azure-localrepository.png)

6. Se você não configurou previamente as credenciais de implantação para publicação de um Aplicativo de API ou outro aplicativo de Serviço de Aplicativo, configure-as agora:

	* Clique em **Configurar as credenciais de implantação**.

	* Digite um nome de usuário e senha. Você precisará dessa senha posteriormente ao configurar o Git.

	* Clique em **Salvar**.

	![Credenciais de implantação do Azure](./media/app-service-create-aspnet-api-app-using-vscode/16-azure-credentials.png)

7. Na folha **Host de aplicativo de API**, clique em **Configurações > Propriedades**. A URL do repositório Git remoto na qual você vai implantar é mostrada em "URL GIT".

8. Copie a **URL GIT** para uso posterior no tutorial.

	![URL Git do Azure](./media/app-service-create-aspnet-api-app-using-vscode/17-azure-giturl.png)

9. Além disso, na folha **APLICATIVO DE API**, copie a **URL** que você usará para atualizar o valor de "host" no arquivo *apiDefinition.swagger.json*.

	![URL do Azure](./media/app-service-create-aspnet-api-app-using-vscode/18-azure-url.png)

10. No VSCode, abra o arquivo *apiDefinition.swagger.json* e substitua o valor de host "É PRECISO SUBSTITUIR ISTO PELA SUA URL DE HOST" pela **URL** você copiou na etapa anterior.
11. Além disso, remova os caracteres "https://" do início do valor do host.
12. Salve suas alterações ao arquivo *apiDefinition.swagger.json*.

## Publicar seu aplicativo de API no Serviço de Aplicativo do Azure

Nesta seção você criará um repositório Git local e enviará por push por meio desse repositório para o Azure, para implantar seu aplicativo de exemplo no Aplicativo de API em execução no Serviço de Aplicativo do Azure.

1. No VSCode na barra de navegação à esquerda, selecione a opção Git.
2. Se o Git ainda não estiver instalado instale-o, escolhendo um dos links fornecidos ([Chocolatey](https://chocolatey.org/packages/git) ou [git-scm.com](http://git-scm.com/downloads)). Se você for novo no Git, escolha **git-scm.com** e selecione a opção para usar Git com GitBash no Prompt de Comando do Windows. 
3. Quando Git estiver instalado, reinicie VSCode e selecione a opção Git na barra à esquerda.
4. No VSCode, selecione **Inicializar repositório git** para assegurar que o espaço de trabalho está sob controle do código-fonte git. 

	![Inicializar Git](./media/app-service-create-aspnet-api-app-using-vscode/19-initgit.png)

5. 
6. Adicione uma mensagem de confirmação e marque a caixa de seleção **Confirmar Todos**.

	![Confirmar Tudo do Git](./media/app-service-create-aspnet-api-app-using-vscode/20-git-commit.png)

6. Localize e abra **GitBash**. Como alternativa, você pode usar o Prompt de Comando do Windows.
7. Em **GitBash**, altere as pastas para sua pasta do projeto VSCode. Por exemplo:

	<pre class="prettyprint">
	cd c:\VSCodeProjects\ContactsList
	</pre>

7. Crie uma referência remota para enviar atualizações por push para o aplicativo Web (host de aplicativo de API) criado anteriormente, usando a URL de Git (com final “.git”) que você copiou anteriormente:

	<pre class="prettyprint">
	git remote add azure [URL para repositório remoto]
	</pre>

8. Envie as alterações por push ao Azure usando o seguinte comando:

	<pre class="prettyprint">
	git push azure master
	</pre>
	Será solicitada a senha que você criou anteriormente. **Observação: a senha não será visível.**

	A saída do comando acima termina com uma mensagem de que a implantação foi bem-sucedida:

	<pre class="prettyprint">
	remote: implantação bem-sucedida.
	Para https://user@testsite.scm.azurewebsites.net/testsite.git
	[new branch]      master -> master
	</pre>

> [AZURE.NOTE]Se você fizer alterações ao seu aplicativo, você poderá republicar marcando a caixa de seleção **Confirmar Todos** no VSCode e, em seguida, inserindo o comando **git push azure master** no **GitBash**.

## Exibir a definição de API no portal de visualização do Azure
Agora que você implantou uma API para seu aplicativo de API, você pode ver a definição de API no portal de visualização do Azure. Você começará reiniciando o gateway, que permite que o Azure reconheça que a definição de API de um aplicativo de API foi alterada. O gateway é um aplicativo Web que trata da administração da API e da autorização para os Aplicativos de API em um grupo de recursos.

1. No portal de visualização do Azure, vá até a folha **APLICATIVO DE API** para o aplicativo de API que você criou anteriormente e clique no link **Gateway**.
2. Na folha **GATEWAY**, clique em **Reiniciar**. Agora você pode fechar esta folha.
3. Na folha **APLICATIVO DE API**, clique em **Reiniciar**. 
4. Na folha **APLICATIVO DE API**, clique em **Definição de API**.<br>
	 A folha Definição de API mostra dois métodos. Se você não vir os métodos GET e POST imediatamente, aguarde alguns segundos para o Azure atualizar o aplicativo. Então, clique em **Definição de API** na folha **APLICATIVO DE API**.

## Executar o aplicativo no Azure
No Portal de Visualização do Azure, vá até a folha **HOST DE APLICATIVO DE API** para seu aplicativo de API e clique em **Procurar**. Em seguida, adicione **api/Contatos** ao final da URL para exibir os detalhes do contato.


## Conclusão
Neste tutorial, você aprendeu como criar um aplicativo de API no Visual Studio Code. Para obter mais informações sobre o Visual Studio Code, consulte [Visual Studio Code](https://code.visualstudio.com/Docs/). Para obter informações sobre aplicativos de API, consulte [O que são aplicativos de API?](app-service-api-apps-why-best-platform.md)
 

<!----HONumber=July15_HO3-->