<properties 
	pageTitle="Criar um serviço REST usando a API Web ASP.NET e o Banco de Dados SQL no Serviço de Aplicativo do Azure" 
	description="Um tutorial que ensina como implantar um aplicativo que usa a API Web ASP.NET em um aplicativo Web do Azure usando o Visual Studio." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	writer="Rick-Anderson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/31/2015" 
	ms.author="riande"/>

# Criar um serviço REST usando a API Web ASP.NET e o Banco de Dados SQL no Serviço de Aplicativo do Azure

Este tutorial mostra como implantar um aplicativo Web ASP.NET em um [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) usando o assistente Publicar Web no Visual Studio 2013 ou no Visual Studio 2013 para Web Express.

É possível abrir uma conta do Azure gratuitamente e, se você ainda não tiver o Visual Studio 2013, o SDK instalará automaticamente o Visual Studio 2013 para o Web Express. Portanto, você pode começar a desenvolver para o Azure de maneira totalmente gratuita.

Este tutorial pressupõe que você não tem nenhuma experiência anterior com o Azure. Ao concluir este tutorial, você terá um aplicativo Web simples em funcionamento na nuvem.
 
Você aprenderá:

* Como habilitar seu computador para desenvolvimento do Azure ao instalar o SDK do Azure.
* Como criar um projeto MVC 5 do Visual Studio ASP.NET e publicá-lo em um aplicativo do Azure.
* Como usar a API Web ASP.NET para habilitar chamadas à API Restful.
* Como usar um banco de dados SQL para armazenar dados no Azure.
* Como publicar atualizações de aplicativo no Azure.

Você criará um aplicativo Web de lista de contatos simples desenvolvido no ASP.NET MVC 5 e que usa o ADO.NET Entity Framework para acesso ao banco de dados. A ilustração a seguir mostra o aplicativo concluído:

![captura de tela do site][intro001]

<!-- the next line produces the "Set up the development environment" section as see at http://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/ -->
[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

### Criar o projeto

1. Inicie o Visual Studio 2013.
1. No menu **Arquivo**, clique em **Novo Projeto**.
3. Na caixa de diálogo **Novo Projeto**, expanda **Visual C#**, selecione **Web** e, em seguida, selecione **Aplicativo Web ASP.NET MVC 5**. Nomeie o aplicativo **ContactManager** e clique em **OK**.

	![Caixa de diálogo Novo Projeto](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr4.PNG)]

1. Na caixa de diálogo **Novo Projeto ASP.NET**, selecione o modelo **MVC**, marque **API Web** e clique em **Alterar Autenticação**.

	![Caixa de diálogo Novo Projeto ASP .NET](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt3.PNG)

1. Na caixa de diálogo **Alterar Autenticação**, clique em **Sem Autenticação** e clique em **OK**.

	![Sem Autenticação](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/GS13noauth.png)

	O aplicativo de exemplo que você está criando não possui recursos que exijam que os usuários façam logon. Para obter informações sobre como implementar recursos de autenticação e autorização, consulte a seção [Próximas Etapas](#nextsteps) no final deste tutorial.

1. Na caixa de diálogo **Novo Projeto ASP .NET**, clique em **OK**.

	![Caixa de diálogo Novo Projeto ASP .NET](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt3.PNG)

Se você não entrou anteriormente no Azure, será solicitado que você entre.

1. O assistente de configuração sugerirá um nome exclusivo com base em *ContactManager* (consulte a imagem abaixo). Selecione uma região perto de você. É possível utilizar o [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com") para localizar o data center de latência mais baixa. 
2. Se você não criou um servidor de banco de dados antes, selecione **Criar novo servidor**, digite um nome de usuário de banco de dados e uma senha.

	![Configurar o site do Azure](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/configAz.PNG)

Se você tiver um servidor de banco de dados, use isso para criar um novo banco de dados. Os servidores de banco de dados são um recurso precioso e você geralmente deseja criar vários bancos de dados no mesmo servidor de teste e desenvolvimento em vez de criar um servidor de banco de dados por banco de dados. Certifique-se de que o seu site e banco de dados estejam na mesma região.

![Configurar o site do Azure](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/configWithDB.PNG)

### Configurar o cabeçalho e o rodapé da página


1. No **Gerenciador de Soluções**, expanda a pasta *Views\\Shared* e abra o arquivo *\_Layout.cshtml*.

	![\_Layout.cshtml no Gerenciador de Soluções][newapp004]

1. Substitua o conteúdo do arquivo *Views\\Shared\_Layout.cshtml* pelo código a seguir:


		<!DOCTYPE html>
		<html lang="en">
		<head>
		    <meta charset="utf-8" />
		    <title>@ViewBag.Title - Contact Manager</title>
		    <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />
		    <meta name="viewport" content="width=device-width" />
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		</head>
		<body>
		    <header>
		        <div class="content-wrapper">
		            <div class="float-left">
		                <p class="site-title">@Html.ActionLink("Contact Manager", "Index", "Home")</p>
		            </div>
		        </div>
		    </header>
		    <div id="body">
		        @RenderSection("featured", required: false)
		        <section class="content-wrapper main-content clear-fix">
		            @RenderBody()
		        </section>
		    </div>
		    <footer>
		        <div class="content-wrapper">
		            <div class="float-left">
		                <p>&copy; @DateTime.Now.Year - Contact Manager</p>
		            </div>
		        </div>
		    </footer>
		    @Scripts.Render("~/bundles/jquery")
		    @RenderSection("scripts", required: false)
		</body>
		</html>
			
A marcação acima altera o nome do aplicativo de "My ASP.NET App" para "Contact Manager" e remove os links para **Página Inicial**, **Sobre** e **Contato**.

### Executar o aplicativo localmente

1. Pressione CTRL+F5 para executar o aplicativo. A página inicial do aplicativo é exibida no navegador padrão. ![Home page da lista de tarefas pendentes](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr5.PNG)

Isso é tudo o que você precisa fazer por enquanto para criar o aplicativo que você implantará no Azure. Posteriormente, você adicionará a funcionalidade do banco de dados.

## Implantar o aplicativo no Azure

1. No Visual Studio, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

	![Publicar no menu de contexto do projeto][PublishVSSolution]

	O assistente de **Publicar Web** é aberto.

12. Clique em **Publicar**.

![Guia Configurações](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/pw.png)

O Visual Studio inicia o processo de cópia dos arquivos no servidor do Azure. A janela **Saída** mostra quais ações de implantação foram executadas e os relatórios da conclusão com êxito da implantação.

14. O navegador padrão abre automaticamente a URL do site implantado.

	O aplicativo que você criou agora está em execução na nuvem.
	
	![A home page da lista de tarefas pendentes em execução no Azure][rxz2]

## Adicionar um banco de dados ao aplicativo

Em seguida, você atualizará o aplicativo MVC para adicionar a habilidade de exibir e atualizar contatos e armazenar os dados em um banco de dados. O aplicativo usará o Entity Framework para criar o banco de dados e ler e atualizar dados no banco de dados.

### Adicionar classes de modelo de dados aos contatos

Você começa criando um modelo de dados simples no código.

1. No **Gerenciador de Soluções**, clique com o botão direito na pasta Modelos e clique em **Adicionar**. Em seguida, clique em **Classe**.

	![Adicionar Classe no menu de contexto da pasta Modelos][adddb001]

2. Na caixa de diálogo **Adicionar Novo Item**, nomeie o novo arquivo de classe *Contact.cs* e clique em **Adicionar**.

	![Caixa de diálogo Adicionar Novo Item][adddb002]

3. Substitua o conteúdo do arquivo Contacts.cs pelo código a seguir.

		using System.Globalization;
		namespace ContactManager.Models
		{
    		public class Contact
   			{
        		public int ContactId { get; set; }
				public string Name { get; set; }
				public string Address { get; set; }
	        	public string City { get; set; }
				public string State { get; set; }
				public string Zip { get; set; }
				public string Email { get; set; }
				public string Twitter { get; set; }
				public string Self
        		{
            		get { return string.Format(CultureInfo.CurrentCulture,
				         "api/contacts/{0}", this.ContactId); }
            		set { }
        		}
    		}
		}

A classe **Contatos** define os dados que você armazenará para cada contato, além de uma chave primária, ContactID, exigida pelo banco de dados. Você pode obter mais informações sobre modelos de dados na seção [Próximas etapas](#nextsteps) ao final deste tutorial.

### Criar as páginas da Web que permitem que os usuários do aplicativo trabalhem com os contatos

No ASP.NET MVC, o recurso de scaffolding pode gerar automaticamente o código que executa as ações CRUD (criar, ler, atualizar e excluir).

## Adicionar um controlador e uma exibição para os dados

1. No **Gerenciador de Soluções**, expanda a pasta Controllers.

3. Compile o projeto **(Ctrl+Shift+B)**. (Você deve compilar o projeto antes de usar o mecanismo de scaffolding.)

4. Clique com o botão direito do mouse na pasta Controladores e clique em **Adicionar** e em **Controlador**.

	![Adicionar Controlador no menu de contexto da pasta Controladores][addcode001]

1. Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC com exibições, usando Entity Framework** e, clique em **Adicionar**.

 ![Adicionar controlador](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrAC.PNG)

6. Defina o nome do controlador como **HomeController**. Selecione **Contato** como a classe de modelo. Clique no botão **Novo contexto de dados** e aceite o padrão "ContactManager.Models.ContactManagerContext" para o **Novo tipo de contexto de dados**. Clique em **Adicionar**.

	![Caixa de diálogo Adicionar Controlador](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr9.PNG)

	Uma caixa de diálogo avisará: "Já existe um arquivo com o nome HomeController. Deseja substituí-lo?". Clique em **Sim**. Estamos substituindo o controlador inicial que foi criado com o novo projeto. Usaremos o novo controlador inicial para nossa lista de contatos.

	O Visual Studio cria métodos do controlador e modos de exibição para as operações de banco de dados CRUD para objetos **Contact**.

## Habilitar migrações, criar o banco de dados, adicionar dados de exemplo e um inicializador de dados ##

A próxima tarefa é habilitar o recurso [Codificar Primeiras Migrações](http://curah.microsoft.com/55220) para criar o banco de dados com base no modelo de dados criado.

1. No menu **Ferramentas**, selecione **Gerenciador de Pacotes de Biblioteca** e, em seguida, selecione **Console do Gerenciador de Pacotes**.

	![Console do Gerenciador de Pacotes no menu Ferramentas][addcode008]

2. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

		enable-migrations 
  
	O comando **enable-migrations** cria a pasta *Migrações* e coloca nessa pasta um arquivo *Configuration.cs*, que pode ser editado para configurar Migrações.

2. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

		add-migration Initial

	O comando **add-migration Initial** gera uma classe denominada **&lt;carimbo\_de\_data/hora&gt;Initial** que cria o banco de dados. O primeiro parâmetro ( *Initial* ) é arbitrário e é usado para criar o nome do arquivo. Você pode ver os novos arquivos de classe no **Gerenciador de Soluções**.

	Na classe **Inicial**, o método **Up** cria a tabela Contatos e o método **Down** (usado quando você deseja retornar ao estado anterior) a descarta.

3. Abra o arquivo *Migrations\\Configuration.cs*.

4. Adicione os seguintes namespaces.

    	 using ContactManager.Models;

5. Substitua o método *Seed* pelo seguinte código:
		
        protected override void Seed(ContactManager.Models.ContactManagerContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
                   Twitter = "debra_example"
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                    Twitter = "thorsten_example"
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                    Twitter = "yuhong_example"
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                    Twitter = "jon_example"
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                    Twitter = "diliana_example"
                }
                );
        }

	O código acima inicializa o banco de dados com as informações de contato. Para obter mais informações sobre a propagação do banco de dados, consulte [Depurando BDs do Entity Framework (EF) (a página pode estar em inglês)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


1. Em **Console do Gerenciador de Pacotes**, digite o comando:

		update-database

	![Comandos do Console do Gerenciador de Pacotes][addcode009]

	O **update-database** executa a primeira migração que cria o banco de dados. Por padrão, o banco de dados é criado como um banco de dados LocalDB do SQL Server Express.

1. Pressione CTRL+F5 para executar o aplicativo.

O aplicativo mostra os dados de semente e fornece links de edição, detalhes e exclusão.

![Exibição do MVC de dados][rxz3]

## Editar a exibição

1. Abra o arquivo *Views\\Home\\Index.cshtml*. Na próxima etapa, substituiremos a marcação gerada pelo código que usa [jQuery](http://jquery.com/) e [Knockout.js](http://knockoutjs.com/). Esse novo código recupera a lista de contatos do uso da API Web e do JSON e associa os dados de contato à interface do usuário usando knockout.js. Para obter mais informações, consulte a seção [Próximas Etapas](#nextsteps) no final deste tutorial. 


2. Substitua o conteúdo do arquivo pelo código a seguir.

		@model IEnumerable<ContactManager.Models.Contact>
		@{
		    ViewBag.Title = "Home";
		}
		@section Scripts {
		    @Scripts.Render("~/bundles/knockout")
		    <script type="text/javascript">
		        function ContactsViewModel() {
		            var self = this;
		            self.contacts = ko.observableArray([]);
		            self.addContact = function () {
		                $.post("api/contacts",
		                    $("#addContact").serialize(),
		                    function (value) {
		                        self.contacts.push(value);
		                    },
		                    "json");
		            }
		            self.removeContact = function (contact) {
		                $.ajax({
		                    type: "DELETE",
		                    url: contact.Self,
		                    success: function () {
		                        self.contacts.remove(contact);
		                    }
		                });
		            }

		            $.getJSON("api/contacts", function (data) {
		                self.contacts(data);
		            });
		        }
		        ko.applyBindings(new ContactsViewModel());	
		</script>
		}
		<ul id="contacts" data-bind="foreach: contacts">
		    <li class="ui-widget-content ui-corner-all">
		        <h1 data-bind="text: Name" class="ui-widget-header"></h1>
		        <div><span data-bind="text: $data.Address || 'Address?'"></span></div>
		        <div>
		            <span data-bind="text: $data.City || 'City?'"></span>,
		            <span data-bind="text: $data.State || 'State?'"></span>
		            <span data-bind="text: $data.Zip || 'Zip?'"></span>
		        </div>
		        <div data-bind="if: $data.Email"><a data-bind="attr: { href: 'mailto:' + Email }, text: Email"></a></div>
		        <div data-bind="ifnot: $data.Email"><span>Email?</span></div>
		        <div data-bind="if: $data.Twitter"><a data-bind="attr: { href: 'http://twitter.com/' + Twitter }, text: '@@' + Twitter"></a></div>
		        <div data-bind="ifnot: $data.Twitter"><span>Twitter?</span></div>
		        <p><a data-bind="attr: { href: Self }, click: $root.removeContact" class="removeContact ui-state-default ui-corner-all">Remove</a></p>
		    </li>
		</ul>
		<form id="addContact" data-bind="submit: addContact">
		    <fieldset>
		        <legend>Add New Contact</legend>
		        <ol>
		            <li>
		                <label for="Name">Name</label>
		                <input type="text" name="Name" />
		            </li>
		            <li>
		                <label for="Address">Address</label>
		                <input type="text" name="Address" >
		            </li>
		            <li>
		                <label for="City">City</label>
		                <input type="text" name="City" />
		            </li>
		            <li>
		                <label for="State">State</label>
		                <input type="text" name="State" />
		            </li>
		            <li>
		                <label for="Zip">Zip</label>
		                <input type="text" name="Zip" />
		            </li>
		            <li>
		                <label for="Email">E-mail</label>
		                <input type="text" name="Email" />
		            </li>
		            <li>
		                <label for="Twitter">Twitter</label>
		                <input type="text" name="Twitter" />
		            </li>
		        </ol>
		        <input type="submit" value="Add" />
		    </fieldset>
		</form>

3. Clique com o botão direito do mouse na pasta Content e clique em **Adicionar** e, em seguida, em **Novo Item...**

	![Adicionar folha de estilos no menu de contexto da pasta Content][addcode005]

4. Na caixa de diálogo **Adicionar Novo Item**, insira **Estilo** na caixa de pesquisa na parte superior direita e selecione **Folha de Estilos**. ![Caixa de diálogo Adicionar Novo Item][rxStyle]

5. Nomeie o arquivo *Contacts.css* e clique em **Adicionar**. Substitua o conteúdo do arquivo pelo código a seguir.
    
        .column {
            float: left;
            width: 50%;
            padding: 0;
            margin: 5px 0;
        }
        form ol {
            list-style-type: none;
            padding: 0;
            margin: 0;
        }
        form li {
            padding: 1px;
            margin: 3px;
        }
        form input[type="text"] {
            width: 100%;
        }
        #addContact {
            width: 300px;
            float: left;
            width:30%;
        }
        #contacts {
            list-style-type: none;
            margin: 0;
            padding: 0;
            float:left;
            width: 70%;
        }
        #contacts li {
            margin: 3px 3px 3px 0;
            padding: 1px;
            float: left;
            width: 300px;
            text-align: center;
            background-image: none;
            background-color: #F5F5F5;
        }
        #contacts li h1
        {
            padding: 0;
            margin: 0;
            background-image: none;
            background-color: Orange;
            color: White;
            font-family: Trebuchet MS, Tahoma, Verdana, Arial, sans-serif;
        }
        .removeContact, .viewImage
        {
            padding: 3px;
            text-decoration: none;
        }

	Usaremos esta folha de estilos no layout, nas cores e nos estilos usados no aplicativo do gerenciador de contatos.

6. Abra o arquivo *AppStart\\BundleConfig.cs*.


7. Adicione o código a seguir para registrar o plug-in [Knockout](http://knockoutjs.com/index.html "KO").

		bundles.Add(new ScriptBundle("~/bundles/knockout").Include(
		            "~/Scripts/knockout-{version}.js"));
	Este exemplo usa o knockout para simplificar o código JavaScript dinâmico que lida com os modelos de tela.

8. Modifique a entrada contents/css para registrar a folha de estilos de *contacts.css*. Altere a linha a seguir:

                 bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/site.css"));
Para:

        bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/contacts.css",
                   "~/Content/site.css"));

1. No Console do Gerenciador de Pacotes, execute o comando a seguir para instalar o Knockout.

	Install-Package knockoutjs
## Adicionar um controlador para a interface da API Web Restful

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Controllers e clique em **Adicionar** e, em seguida, em **Controlador...**

1. Na caixa de diálogo **Adicionar Scaffold**, insira **Controlador API 2 Web com ações, usando Entity Framework** e clique em **Adicionar**.

	![Adicionar a API do controlador](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt1.PNG)

4. Na caixa de diálogo **Adicionar Controlador**, insira "ContactsController" como o nome do controlador. Selecione "Contato (ContactManager.Models)" para a **Classe do modelo**. Mantenha o valor padrão para o **Classe de contexto de dados**.

6. Clique em **Adicionar**.

### Executar o aplicativo localmente

1. Pressione CTRL+F5 para executar o aplicativo.

	![Página de índice][intro001]

2. Digite um contato e clique em **Adicionar**. O aplicativo retorna para a home page e exibe o contato que você inseriu.

	![Página de índice com itens da lista de tarefas pendentes][addwebapi004]

3. No navegador, acrescente **/api/contacts** à URL.

	A URL resultante será semelhante a http://localhost:1234/api/contacts. A API Web RESTful que você adicionou retorna os contatos armazenados. O Firefox e o Chrome exibirão os dados em formato XML.

	![Página de índice com itens da lista de tarefas pendentes][rxFFchrome]
	

	O IE solicitará que você abra ou salve os contatos.

	![Caixa de diálogo Salvar API Web][addwebapi006]
	
	
	Você pode abrir os contatos retornados no bloco de notas ou em um navegador.
	
	Essa saída pode ser consumida por outro aplicativo, como um aplicativo ou uma página da web móvel.

	![Caixa de diálogo Salvar API Web][addwebapi007]

	**Aviso de Segurança**: neste ponto, o aplicativo não é seguro e está vulnerável a ataques CSRF. Mais à frente no tutorial, removeremos essa vulnerabilidade. Para obter mais informações, consulte [Evitando ataques de solicitação intersite forjada (CSRF)][prevent-csrf-attacks].
## Adicionar proteção XSRF

A solicitação entre sites forjada (também conhecida como XSRF ou CSRF) é um ataque contra aplicativos web hospedados, no qual um site mal-intencionado pode influenciar a interação entre um navegador cliente e um site confiável para esse navegador. Esses ataques são possibilitados porque os navegadores da web enviarão tokens de autenticação automaticamente com toda solicitação para um site. O exemplo canônico é um cookie de autenticação, como o tíquete de autenticação de formulários do ASP.NET. No entanto, os sites que usam qualquer mecanismo de autenticação persistente (como a autenticação do Windows, Basic e assim por diante) podem ser alvos desses ataques.

Um ataque XSRF é diferente de um ataque de phishing. Os ataques de phishing exigem a interação da vítima. Em um ataque de phishing, um site mal-intencionado imita o site de destino e a vítima é levada a fornecer informações confidenciais ao invasor. Em um ataque XSRF, normalmente não é necessária nenhuma interação da vítima. Em vez disso, o invasor conta com que o navegador envie automaticamente todos os cookies relevantes ao site de destino.

Para obter mais informações, consulte o [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP) [XSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)).

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **ContactManager**, depois clique em **Adicionar** e em **Classe**.

2. Nomeie o arquivo como *ValidateHttpAntiForgeryTokenAttribute.cs* e adicione o seguinte código:

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Net;
        using System.Net.Http;
        using System.Web.Helpers;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using System.Web.Mvc;
        namespace ContactManager.Filters
        {
            public class ValidateHttpAntiForgeryTokenAttribute : AuthorizationFilterAttribute
            {
                public override void OnAuthorization(HttpActionContext actionContext)
                {
                    HttpRequestMessage request = actionContext.ControllerContext.Request;
                    try
                    {
                        if (IsAjaxRequest(request))
                        {
                            ValidateRequestHeader(request);
                        }
                        else
                        {
                            AntiForgery.Validate();
                        }
                    }
                    catch (HttpAntiForgeryException e)
                    {
                        actionContext.Response = request.CreateErrorResponse(HttpStatusCode.Forbidden, e);
                    }
                }
                private bool IsAjaxRequest(HttpRequestMessage request)
                {
                    IEnumerable<string> xRequestedWithHeaders;
                    if (request.Headers.TryGetValues("X-Requested-With", out xRequestedWithHeaders))
                    {
                        string headerValue = xRequestedWithHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(headerValue))
                        {
                            return String.Equals(headerValue, "XMLHttpRequest", StringComparison.OrdinalIgnoreCase);
                        }
                    }
                    return false;
                }
                private void ValidateRequestHeader(HttpRequestMessage request)
                {
                    string cookieToken = String.Empty;
                    string formToken = String.Empty;
					IEnumerable<string> tokenHeaders;
                    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
                    {
                        string tokenValue = tokenHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(tokenValue))
                        {
                            string[] tokens = tokenValue.Split(':');
                            if (tokens.Length == 2)
                            {
                                cookieToken = tokens[0].Trim();
                                formToken = tokens[1].Trim();
                            }
                        }
                    }
                    AntiForgery.Validate(cookieToken, formToken);
                }
            }
        }

1. Adicione a instrução *using* a seguir ao controlador de contratos, de forma que você tenha acesso ao atributo **[ValidateHttpAntiForgeryToken]**.

	using ContactManager.Filters;

1. Adicione o atributo **[ValidateHttpAntiForgeryToken]** aos métodos Post do **ContactsController** para protegê-lo de ameaças XSRF. Você o adicionará aos métodos de ação "PutContact", "PostContact" e **DeleteContact**.

	[ValidateHttpAntiForgeryToken] public IHttpActionResult PutContact(int id, Contact contact) {

1. Atualize a seção *Scripts* do arquivo *Views\\Home\\Index.cshtml* para incluir o código para obtenção de tokens XSRF.

         @section Scripts {
            @Scripts.Render("~/bundles/knockout")
            <script type="text/javascript">
                @functions{
                   public string TokenHeaderValue()
                   {
                      string cookieToken, formToken;
                      AntiForgery.GetTokens(null, out cookieToken, out formToken);
                      return cookieToken + ":" + formToken;                
                   }
                }

               function ContactsViewModel() {
                  var self = this;
                  self.contacts = ko.observableArray([]);
                  self.addContact = function () {

                     $.ajax({
                        type: "post",
                        url: "api/contacts",
                        data: $("#addContact").serialize(),
                        dataType: "json",
                        success: function (value) {
                           self.contacts.push(value);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }
                     });

                  }
                  self.removeContact = function (contact) {
                     $.ajax({
                        type: "DELETE",
                        url: contact.Self,
                        success: function () {
                           self.contacts.remove(contact);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }

                     });
                  }

                  $.getJSON("api/contacts", function (data) {
                     self.contacts(data);
                  });
               }
               ko.applyBindings(new ContactsViewModel());
            </script>


## Publicar a atualização do aplicativo no Azure e no Banco de Dados SQL

Para publicar o aplicativo, repita o procedimento seguido anteriormente.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Publicar**.

	![Publicar][rxP]

5. Clique na guia **Configurações**.
	

1. Em **ContactsManagerContext(ContactsManagerContext)**, clique no ícone **v** para alterar *Cadeia de conexão remota* para a cadeia de conexão do banco de dados de contato. Clique em **ContactDB**.

	![Configurações](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt5.png)

7. Marque a caixa de seleção de **Executar Codificar Migrações Iniciais (executado na inicialização do aplicativo)**.

1. Clique em **Avançar** e, em seguida, em **Visualizar **. O Visual Studio exibe uma lista dos arquivos que serão adicionados ou atualizados.

8. Clique em **Publicar**. Depois que a implantação for concluída, o navegador será aberto na home page do aplicativo.

	![Página de índice sem contatos][intro001]

	O processo de publicação do Visual Studio configurou automaticamente a cadeia de conexão no arquivo *Web.config* implantado para apontar para o banco de dados SQL. Ele também configurou Codificar Migrações Iniciais para atualizar automaticamente o banco de dados para a versão mais recente na primeira vez em que o aplicativo acessar o banco de dados após a implantação.

	Como resultado dessa configuração, o Código Primeiro criou o banco de dados executando o código na classe **Initial** que você criou anteriormente. Ele fez isso na primeira vez que o aplicativo tentou acessar o banco de dados após a implantação.

9. Insira um contato como quando você executou o aplicativo localmente para verificar se a implantação do banco de dados teve êxito.

Ao ver que o item inserido foi salvo e exibido na página do gerenciador de contatos, você sabe que ele foi armazenado no banco de dados.

![Página de índice com contatos][addwebapi004]

O aplicativo agora está sendo executado na nuvem, usando o Banco de dados SQL para armazenar seus dados. Depois de concluir o teste do aplicativo no Azure, exclua-o. O aplicativo é público e não tem um mecanismo para limitar o acesso.

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Próximas etapas

Um aplicativo real exigiria autenticação e autorização, e você usaria esse banco de dados de associação para essa finalidade. O tutorial [Implantar um aplicativo de segurança ASP.NET MVC com OAuth, Associação e banco de dados SQL (a página pode estar em inglês)](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md) é baseado neste tutorial e mostra como implantar um aplicativo web com o banco de dados de associação.

Outra maneira de armazenar dados em um aplicativo do Azure é usar o Armazenamento do Azure, que fornece armazenamento de dados não relacionais na forma de blobs e tabelas. Os links a seguir fornecem mais informações sobre a API Web, o ASP.NET MVC e o Microsoft Azure.
 

* [Introdução ao Entity Framework usando MVC][EFCodeFirstMVCTutorial]
* [Introdução ao ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [Sua primeira API Web ASP.NET](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)
* [Depurando WAWS](web-sites-dotnet-troubleshoot-visual-studio.md)

Este tutorial e o aplicativo de exemplo foram escritos por [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) com a assistência de Tom Dykstra e Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)).

Deixe comentários sobre o que você gostou ou do que você gostaria de ver melhorado, não apenas sobre o próprio tutorial, mas também sobre os produtos que ele demonstra. Seus comentários nos ajudarão a priorizar melhorias. Estamos especialmente interessados em saber quanto há de interesse em mais automação para o processo de configuração e implantação de banco de dados de associação.

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, confira: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Add Roles to the Membership Database]: #mbrDB
[Create a Data Deployment Script]: #ppd
[Update the Membership Database]: #ppd2
[setupdbenv]: #bkmk_setupdevenv
[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[adddb]: #bkmk_addadatabase
[addcontroller]: #bkmk_addcontroller
[addwebapi]: #bkmk_addwebapi
[deploy2]: #bkmk_deploydatabaseupdate

<!-- links -->
[EFCodeFirstMVCTutorial]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[dbcontext-link]: http://msdn.microsoft.com/library/system.data.entity.dbcontext(v=VS.103).aspx


<!-- images-->
[rxE]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxE.png
[rxP]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxP.png
[rx22]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/
[rxb2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxb2.png
[rxz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz.png
[rxzz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxzz.png
[rxz2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz2.png
[rxz3]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz3.png
[rxStyle]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxStyle.png
[rxz4]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz4.png
[rxz44]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz44.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxPrevDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPrevDB.png
[rxOverwrite]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxOverwrite.png
[rxPWS]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPWS.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxAddApiController]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxAddApiController.png
[rxFFchrome]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxFFchrome.png
[intro001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobil-intro-finished-web-app.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxCreateWSwithDB.png
[setup007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-setup-azure-site-004.png
[setup009]: ../Media/dntutmobile-setup-azure-site-006.png
[newapp002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-002.png
[newapp004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-004.png
[firsdeploy007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-005.png
[firsdeploy009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-007.png
[adddb001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-001.png
[adddb002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-context-menu.png
[addcode002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-controller-dialog.png
[addcode004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-index-context.png
[addcode005]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-contents-context-menu.png
[addcode007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-bundleconfig-context.png
[addcode008]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-console.png
[addwebapi004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-added-contact.png
[addwebapi006]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-save-returned-contacts.png
[addwebapi007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-contacts-in-notepad.png
[Add XSRF Protection]: #xsrf
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[Add XSRF Protection]: #xsrf
[ImportPublishSettings]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishSettings.png
[ImportPublishProfile]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishProfile.png
[PublishVSSolution]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/PublishVSSolution.png
[ValidateConnection]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ValidateConnection.png
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[prevent-csrf-attacks]: http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-(csrf)-attacks
 

<!---HONumber=Oct15_HO3-->