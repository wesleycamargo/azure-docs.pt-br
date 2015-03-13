<properties 
	pageTitle="Serviço REST .NET usando a API da Web API - Tutorial do Azure" 
	description="Um tutorial que ensina como implantar um aplicativo que usa a API Web ASP.NET em um site do Azure usando o Visual Studio." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="riande" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/06/2014" 
	ms.author="riande"/>

# Serviço REST usando a API Web ASP.NET e o Banco de Dados SQL 

***Por [Rick Anderson](https://twitter.com/RickAndMSFT) e Tom Dykstra.***

Este tutorial mostra como implantar um aplicativo Web ASP.NET em um site do Azure utilizando o assistente Publicar Web no Visual Studio 2013 ou no Visual Studio 2013 para Web Express. 

É possível abrir uma conta do Azure gratuitamente e, se você ainda não tiver o Visual Studio 2013, o SDK instalará automaticamente o Visual Studio 2013 para o Web Express. Portanto, você pode começar a desenvolver para o Azure de maneira totalmente gratuita.

Este tutorial pressupõe que você não tem nenhuma experiência anterior com o Azure. Ao concluir este tutorial, você terá um aplicativo Web simples em funcionamento na nuvem.
 
Você aprenderá a:

* Como habilitar seu computador para desenvolvimento do Azure ao instalar o SDK do Azure.
* Como criar um projeto MVC 5 do Visual Studio ASP.NET e publicá-lo em um site do Azure.
* Como usar a API Web ASP.NET para habilitar chamadas à API Restful.
* Como usar um banco de dados SQL para armazenar dados no Azure.
* Como publicar atualizações de aplicativo no Azure.

Você criará um aplicativo Web de lista de contatos simples criado no ASP.NET MVC 5 e que usa o ADO.NET Entity Framework para acesso ao banco de dados. A ilustração a seguir mostra o aplicativo concluído:

![screenshot of web site][intro001]

Neste tutorial:

* [Configurar o ambiente de desenvolvimento][setupdbenv]
* [Configurar o ambiente do Azure][setupwindowsazureenv]
* [Criar um aplicativo ASP.NET MVC 5][createapplication]
* [Implantar o aplicativo no Azure][deployapp1]
* [Adicionar um banco de dados ao aplicativo][adddb]
* [Adicionar um controlador e uma exibição para os dados][addcontroller]
* [Adicionar uma interface de API Restful de Web ][addwebapi]
* [Adicionar proteção XSRF][]
* [Publicar a atualização do aplicativo no Azure e no banco de dados SQL][deploy2]

<a name="bkmk_setupdevenv"></a>
<!-- the next line produces the "Set up the development environment" section as see at http://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/ -->
[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

<h2><a name="bkmk_setupwindowsazure"></a>Configurar o ambiente do Azure</h2>

Em seguida, configure o ambiente do Azure criando um site do Azure e um banco de dados SQL.

### Criar um site e um banco de dados SQL no Azure

A próxima etapa é criar o site do Azure e o banco de dados SQL que o aplicativo usará.

O seu site do Azure será executado em um ambiente de hospedagem compartilhado, o que significa que ele é executado em máquinas virtuais (VMs) compartilhadas com outros clientes do Azure. Um ambiente de hospedagem compartilhado é uma maneira de começar na nuvem a um baixo custo. Mais tarde, se seu tráfego da Web aumentar, o aplicativo poderá ser dimensionado para atender à necessidade executando em VMs dedicadas. Se precisar de uma arquitetura mais complexa, você poderá migrar para um Serviço de Nuvem do Azure. Os serviços de nuvem são executados em VMs dedicadas que podem ser configuradas de acordo com suas necessidades.

O Banco de dados SQL é um serviço de banco de dados relacional baseado em nuvem que é fundamentado em tecnologias do SQL Server. As ferramentas e os aplicativos que funcionam com o SQL Server também funcionam com o Banco de Dados SQL.

1. No [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), clique em **Sites** na guia esquerda, em seguida, clique em **Novo**.

2. Clique em **CRIAÇÃO PERSONALIZADA**.

	[Criar com link de banco de dados no Portal de Gerenciamento](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr6.PNG)

	O assistente **Novo site - Criação personalizada** é aberta. 

3. Na etapa **Criar Site** do assistente, insira uma sequência de caracteres na caixa **URL** a ser usada como a URL exclusiva de seu aplicativo. A URL completa consistirá no que você digitar aqui mais o sufixo que você vê abaixo da caixa de texto. A ilustração mostra "contactmgr11", mas essa URL provavelmente já é utilizada e, portanto, você precisa escolher outra.

1. Na lista suspensa **Região**, escolha a região mais próxima de você.

1. Na lista suspensa **Banco de Dados**, escolha **Criar um banco de dados SQL de 20 MB gratuito**.

	![Criar uma nova etapa do site de Novo Site - Criar com o assistente de banco de dados](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrCWS.png)

6. Clique na seta que aponta para a direita na parte inferior da caixa.

	O assistente avança para a etapa **Configurações do Banco de Dados**.

7. Na caixa **Nome**, digite *ContactDB*.

8. Na caixa **Servidor**, selecione **Novo Servidor do Banco de Dados SQL**. Como alternativa, se tiver criado um banco de dados SQL Server, você poderá selecionar esse SQL Server no controle suspenso.

9. Clique na seta que aponta para a direita na parte inferior da caixa.

10. Insira o **NOME DE LOGON** e a **SENHA** de um administrador. Se selecionou **Novo servidor do Banco de Dados SQL**, você não irá digitar um nome e uma senha existentes aqui; digite um novo nome e uma senha que você está definindo agora para usar mais tarde ao acessar o banco de dados. Se você tiver selecionado um SQL Server criado anteriormente, será solicitada a senha para o nome da conta do SQL Server anterior que você criou. Neste tutorial, não selecionaremos a caixa **Avançado**. A caixa **Avançado** permite que você defina o tamanho do banco de dados (o padrão é 1 GB, mas você pode aumentar esse tamanho para 150 GB) e o agrupamento.

11. Clique na marca de seleção na parte inferior da caixa para indicar que você concluiu.

	![Configurações do banco de dados do Novo site - Criar com o assistente de banco de dados][setup007]

	 A imagem a seguir mostra o uso de um SQL Server e logon existentes.
	
	![Configurações do banco de dados do Novo site - Criar com o assistente de banco de dados][rxPrevDB]

	O Portal de Gerenciamento do Azure retorna para a página Sites e a coluna **Status** mostra que o site está sendo criado. Depois de um tempo (normalmente menos de um minuto), a coluna **Status** mostra que o site foi criado com êxito. Na barra de navegação à esquerda, o número de sites presentes na sua conta é exibido próximo ao ícone **Sites**, enquanto o número de bancos de dados aparece ao lado do ícone **Bancos de Dados SQL**.

<!-- [Websites page of Management Portal, website created][setup009] -->

<h2><a name="bkmk_createmvc4app"></a>Criar um aplicativo ASP.NET MVC 5</h2>

Você criou um Site do Azure, mas ainda não há conteúdo nele. A próxima etapa é criar o projeto de aplicativo Web do Visual Studio que será publicado no Azure.

### Criar o projeto

1. Inicie o Visual Studio 2013.
1. No menu **Arquivo**, clique em **Novo Projeto**.
3. Na caixa de diálogo **Novo Projeto**, expanda **Visual C#** e selecione **Web** e **Aplicativo Web MVC 5 do ASP.NET**. Mantenha o padrão **.NET Framework 4.5**. Nomeie o aplicativo **ContactManager** e clique em **OK**.
	[Caixa de diálogo Novo Projeto](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr4.PNG)]
1. Na caixa de diálogo **Novo Projeto ASP.NET**, selecione o modelo **MVC**, marque **API Web** e clique em **Alterar Autenticação**.

	![Caixa de diálogo Novo Projeto ASP .NET](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt3.PNG)

1. Na caixa de diálogo **Alterar Autenticação**, clique em **Sem Autenticação** e clique em **OK**.

	![Nenhuma autenticação](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)

	O aplicativo de exemplo que você está criando não possui recursos que exijam que os usuários façam logon. Para obter informações sobre como implementar recursos de autenticação e autorização, consulte a seção [Próximas etapas](#nextsteps) no final deste tutorial. 

1. Na caixa de diálogo **Novo Projeto ASP .NET**, clique em **OK**.

	![Caixa de diálogo Novo Projeto ASP .NET](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)

### Configurar o cabeçalho e o rodapé da página


1. No **Gerenciador de Soluções**, expanda a pasta *Views\Shared* e abra o arquivo *_Layout.cshtml*.

	![_Layout.cshtml no Gerenciador de Soluções][newapp004]

1. Substitua o conteúdo do arquivo *_Layout.cshtml* pelo código a seguir:


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

1. Pressione CTRL+F5 para executar o aplicativo.
A home page do aplicativo é exibida no navegador padrão.
	![Lista de tarefas pendentes HomePage](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr5.PNG)

Isso é tudo que necessita ser feito, por enquanto, para criar o aplicativo que você implantará no Azure. Posteriormente, você adicionará a funcionalidade do banco de dados.

<h2><a name="bkmk_deploytowindowsazure1"></a>Implantar o aplicativo no Azure</h2>

1. No Visual Studio, clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

	![Publicar no menu de contexto do projeto][PublishVSSolution]

	O assistente de **Publicar Web** é aberto.

2. Na guia **Perfil** do assistente de **Publicar Web**, clique em **Importar**.

	![Import publish settings][ImportPublishSettings]

	A caixa de diálogo **Importar Perfil de Publicação** é aberta.

 3.	Selecione Importar de um Site do Azure. Você deverá primeiro entrar, se ainda não tiver feito isso. Clique em **Entrar**. Insira o usuário associado à assinatura e siga as etapas para entrar.

	![sign in](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr7.png)

	Selecione o site na lista suspensa e clique em **OK**.

	![Import Publish Profile](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr8.png)

8. Na guia **Conexão**, clique em **Validar Conexão** para garantir que as configurações estão corretas.

	![Validate connection][ValidateConnection]

9. Quando a conexão tiver sido validada, uma marca de seleção verde será mostrada ao lado do botão **Validar Conexão**.

	![connection successful icon and Next button in Connection tab][firsdeploy007]

1. Clique em **Próximo**.

	![Settings tab](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)

	Você pode aceitar as configurações padrão nesta guia.  Você está implantando uma configuração da compilação de versão e não precisa excluir arquivos no servidor de destino, pré-compilar o aplicativo ou excluir arquivos na pasta App_Data. Se quiser depurar o site do Azure dinâmico, você precisará implantar uma configuração de depuração (e não de versão). Consulte as [Próximas etapas](#nextsteps) no final deste tutorial.

12. Na guia **visualização**, clique em **Iniciar Visualização**.

	A guia exibe uma lista dos arquivos que serão copiados no servidor. A exibição da visualização não é obrigatória para publicar o aplicativo, mas é uma função reconhecidamente útil. Nesse caso, você não precisa fazer nada com a lista de arquivos exibida. Na próxima vez que você publicar, apenas os arquivos que foram alterados estarão na lista de visualização.

	![StartPreview button in the Preview tab][firsdeploy009]

12. Clique em **Publicar**.

	O Visual Studio inicia o processo de cópia dos arquivos no servidor do Azure. A janela **Saída** mostra quais ações de implantação foram executadas e os relatórios da conclusão com êxito da implantação.

14. O navegador padrão abre automaticamente a URL do site implantado.

	O aplicativo que você criou agora está em execução na nuvem.
	
	![To Do List home page running in Azure][rxz2]

<h2><a name="bkmk_addadatabase"></a>Adicionar um banco de dados ao aplicativo</h2>

Em seguida, você atualizará o aplicativo MVC para adicionar a habilidade de exibir e atualizar contatos e armazenar os dados em um banco de dados. O aplicativo usará o Entity Framework para criar o banco de dados e ler e atualizar dados no banco de dados.

### Adicionar classes de modelo de dados aos contatos

Você começa criando um modelo de dados simples no código.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Modelos, clique em **Adicionar**e, em seguida, em **Classe**.

	![Add Class in Models folder context menu][adddb001]

2. Na caixa de diálogo **Adicionar Novo Item**, nomeie o novo arquivo de classe  *Contact.cs* e clique em **Adicionar**.

	![Add New Item dialog box][adddb002]

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

A classe **Contatos** define os dados que você armazenará para cada contato, além de uma chave primária, ContactID, exigida pelo banco de dados. É possível obter mais informações sobre modelos de dados em [Próximas etapas](#nextsteps) no final deste tutorial.

### Criar as páginas da web que permitem que os usuários do aplicativo trabalhem com os contatos

No ASP.NET MVC, o recurso de scaffolding pode gerar automaticamente o código que executa as ações CRUD (criar, ler, atualizar e excluir).

<h2><a name="bkmk_addcontroller"></a>Adicionar um controlador e uma visualização para os dados</h2>

1. No **Gerenciador de Soluções**, expanda a pasta Controladores.

3. Compile o projeto **(Ctrl + Shift + B)**. (Você deve compilar o projeto antes de usar o mecanismo de scaffolding.) 

4. Clique com o botão direito do mouse na pasta Controladores e clique em **Adicionar** e em **Controlador**.

	![Add Controller in Controllers folder context menu][addcode001]

1. Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC com exibições, usando Entity Framework** e, clique em **Adicionar**.

 ![Add controller](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrAC.PNG)

6. Defina o nome do controlador como **HomeController**. Selecione **Contato** como a classe de modelo. Clique no botão **Novo contexto de dados** e aceite o padrão "ContactManager.Models.ContactManagerContext" para o **Novo tipo de contexto de dados**. Clique em **Adicionar**.

	![Add Controller dialog box](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr9.PNG)

	Uma caixa de diálogo avisará: "Já existe um arquivo com o nome HomeController. Deseja substituí-lo?". Clique em **Sim**. Estamos substituindo o controlador inicial que foi criado com o novo projeto. Usaremos o novo controlador inicial para nossa lista de contatos.

	O Visual Studio cria métodos do controlador e modos de exibição para as operações de banco de dados CRUD para objetos **Contact**.

## Habilitar migrações, criar o banco de dados, adicionar dados de exemplo e um inicializador de dados ##

A próxima tarefa é habilitar o recurso [Codificar Primeiras Migrações](http://curah.microsoft.com/55220) para criar o banco de dados com base no modelo de dados criado.

1. No menu **Ferramentas**, selecione **Gerenciador de Pacotes da Biblioteca** e **Console do Gerenciador de Pacotes**.

	![Package Manager Console in Tools menu][addcode008]

2. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

		enable-migrations 
  
	O comando **enable-migrations** cria uma pasta *Migrations* e coloca nessa pasta um arquivo *Configuration.cs* que pode ser editado para configurar migrações. 

2. Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

		add-migration Initial

	O comando **add-migration inicial** gera uma classe chamada **&lt;date_stamp&gt;inicial** que cria o banco de dados. O primeiro parâmetro ( *Initial*) é arbitrário e é usado para criar o nome do arquivo. Você pode ver os novos arquivos de classe no **Gerenciador de Soluções**.

	Na classe **Initial**, o método **Up** cria a tabela Contatos, e o método **Down** (usado quando você deseja retornar ao estado anterior) a descarta.

3. Abra o arquivo  *Migrations\Configuration.cs*. 

4. Adicione os seguintes namespaces. 

    	 using ContactManager.Models;

5. Substitua o método  *Seed* pelo seguinte código:
		
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

	![Package Manager Console commands][addcode009]

	O **update-database** executa a primeira migração que cria o banco de dados. Por padrão, o banco de dados é criado como um banco de dados LocalDB do SQL Server Express.

1. Pressione CTRL+F5 para executar o aplicativo. 

O aplicativo mostra os dados de semente e fornece links de edição, detalhes e exclusão.

![MVC view of data][rxz3]

<h2><a name="bkmk_addview"></a>Editar a exibição</h2>

1. Abra o arquivo  *Views\Home\Index.cshtml*. Na próxima etapa, substituiremos a marcação gerada pelo código que usa [jQuery](http://jquery.com/) e [Knockout.js](http://knockoutjs.com/). Esse novo código recupera a lista de contatos do uso da API Web e do JSON e associa os dados de contato à interface do usuário usando knockout.js. Para obter mais informações, consulte a seção [Próximas etapas](#nextsteps) no final deste tutorial. 


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

3. Clique com o botão direito do mouse na pasta Conteúdo e clique em **Adicionar** e em **Novo Item...**.

	![Add style sheet in Content folder context menu][addcode005]

4. Na caixa de diálogo **Adicionar Novo Item**, digite **Estilo** na caixa de pesquisa na parte superior direita e selecione **Folha de Estilos**.
	![Add New Item dialog box][rxStyle]

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

6. Abra o arquivo  *App_Start\BundleConfig.cs*.


7. Adicione o código a seguir para registrar o plug-in [Knockout](http://knockoutjs.com/index.html "KO").

		bundles.Add(new ScriptBundle("~/bundles/knockout").Include(
		            "~/Scripts/knockout-{version}.js"));
	Este exemplo usa o knockout para simplificar o código JavaScript dinâmico que lida com os modelos de tela.

8. Modifique a entrada contents/css para registrar a folha de estilos de  *contacts.css*. Altere a linha a seguir:

                 bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/site.css"));
To:

        bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/contacts.css",
                   "~/Content/site.css"));

1. No Console do Gerenciador de Pacotes, execute o comando a seguir para instalar o Knockout.

	Install-Package knockoutjs

<h2><a name="bkmk_addwebapi"></a>Adicionar um controlador para a interface da API Web Restful</h2>

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Controladores e clique em **Adicionar** e em **Controlador....** 

1. Na caixa de diálogo **Adicionar Scaffold**, insira **Controlador API 2 Web com ações, usando Entity Framework** e clique em **Adicionar**.

	![Add API controller](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt1.PNG)

4. Na caixa de diálogo **Adicionar Controlador**, insira "ContactsController" como o nome do controlador. Selecione "Contato (ContactManager.Models)" para a **Classe do modelo**.  Mantenha o valor padrão para o **Classe de contexto de dados**. 

6. Clique em **Adicionar**.

### Executar o aplicativo localmente

1. Pressione CTRL+F5 para executar o aplicativo.

	![Index page][intro001]

2. Digite um contato e clique em **Adicionar**. O aplicativo retorna para a home page e exibe o contato que você inseriu.

	![Index page with to-do list items][addwebapi004]

3. No navegador, acrescente **/api/contacts** à URL.

	A URL resultante será semelhante a http://localhost:1234/api/contacts. A API Web RESTful que você adicionou retorna os contatos armazenados. O Firefox e o Chrome exibirão os dados em formato XML.

	![Index page with to-do list items][rxFFchrome]
	

	O IE solicitará que você abra ou salve os contatos.

	![Web API save dialog][addwebapi006]
	
	
	Você pode abrir os contatos retornados no bloco de notas ou em um navegador.
	
	Essa saída pode ser consumida por outro aplicativo, como um aplicativo ou uma página da Web móvel.

	![Web API save dialog][addwebapi007]

	**Aviso de segurança**: Neste momento, o aplicativo é inseguro e vulnerável a ataques CSRF. Mais à frente no tutorial, removeremos essa vulnerabilidade. Para obter mais informações, consulte [Evitando ataques de solicitação intersite forjada (CSRF)][prevent-csrf-attacks].

<h2><a name="xsrf"></a>Adicionar proteção XSRF</h2>

A solicitação entre sites forjada (também conhecida como XSRF ou CSRF) é um ataque contra aplicativos web hospedados, no qual um site mal-intencionado pode influenciar a interação entre um navegador cliente e um site confiável para esse navegador. Esses ataques são possibilitados porque os navegadores da web enviarão tokens de autenticação automaticamente com cada solicitação para um site. O exemplo canônico é um cookie de autenticação, como o tíquete de autenticação de formulários do ASP.NET. No entanto, os sites que usam qualquer mecanismo de autenticação persistente (como a autenticação do Windows, Basic e assim por diante) podem ser direcionados por esses ataques.

Um ataque XSRF é diferente de um ataque de phishing. Os ataques de phishing exigem a interação da vítima. Em um ataque de phishing, um site mal-intencionado imita o site de destino e a vítima é levada a fornecer informações confidenciais ao invasor. Em um ataque XSRF, normalmente não é necessária nenhuma interação da vítima. Em vez disso, o invasor conta com que o navegador envie automaticamente todos os cookies relevantes ao site de destino.

Para obter mais informações, consulte o [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP) [XSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)).

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **ContactManager** e clique em **Adicionar** e em **Classe**.

2. Nomeie o arquivo *ValidateHttpAntiForgeryTokenAttribute.cs* e adicione o seguinte código:

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

1. Adicione a instrução  *using* seguinte ao controlador de contratos, de forma que você tenha acesso ao atributo **[ValidateHttpAntiForgeryToken]**.

	using ContactManager.Filters;

1. Adicione o atributo **[ValidateHttpAntiForgeryToken]** aos métodos Post do **ContactsController** para protegê-lo de ameaças XSRF. Você o adicionará aos métodos de ação "PutContact", "PostContact" e **DeleteContact**.

	[ValidateHttpAntiForgeryToken]
        public IHttpActionResult PutContact(int id, Contact contact)
        {

1. Atualize a seção *Scripts* do arquivo *Views\Home\Index.cshtml* para incluir o código para obter os tokens XSRF.

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


<h2><a name="bkmk_deploydatabaseupdate"></a>Publicar a atualização do aplicativo no Azure e no Banco de Dados SQL</h2>

Para publicar o aplicativo, repita o procedimento seguido anteriormente.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Publicar**.

	![Publish][rxP]

5. Clique na guia **Configurações**.
	

1. Em **ContactsManagerContext(ContactsManagerContext)**, clique no ícone **v** para alterar a  *Remote connection string* para a cadeia de conexão do banco de dados de contato. Clique em **ContactDB**.

	![Settings](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt5.png)

7. Marque a caixa de seleção de **Executar Codificar Migrações Iniciais (executado na inicialização do aplicativo)**.

1. Clique em **Avançar** e, em seguida, em **Visualizar **. O Visual Studio exibe uma lista dos arquivos que serão adicionados ou atualizados.

8. Clique em **Publicar**.
Depois que a implantação for concluída, o navegador será aberto na home page do aplicativo.

	![Index page with no contacts][intro001]

	O processo de publicação do Visual Studio configurou automaticamente a cadeia de conexão no arquivo *Web.config* implantado *Web.config* para apontar para o banco de dados SQL. Ele também configurou Codificar Migrações Iniciais para atualizar automaticamente o banco de dados para a versão mais recente na primeira vez em que o aplicativo acessar o banco de dados após a implantação.

	Como resultado dessa configuração, o Código Primeiro criou o banco de dados executando o código na classe **Initial** que você criou anteriormente. Ele fez isso na primeira vez que o aplicativo tentou acessar o banco de dados após a implantação.

9. Insira um contato como quando você executou o aplicativo localmente para verificar se a implantação do banco de dados teve êxito.

Ao ver que o item inserido foi salvo e exibido na página do gerenciador de contatos, você sabe que ele foi armazenado no banco de dados.

![Página de índice com contatos][addwebapi004]

O aplicativo agora está sendo executado na nuvem, utilizando o banco de dados SQL para armazenar seus dados. Depois de concluir o teste do aplicativo no Azure, exclua-o. O aplicativo é público e não tem um mecanismo para limitar o acesso.

<h2><a name="nextsteps"></a>Próximas etapas</h2>

Um aplicativo real exigiria autenticação e autorização, e você usaria esse banco de dados de associação para essa finalidade. O tutorial [Implantar um aplicativo de segurança ASP.NET MVC com OAuth, Associação e banco de dados SQL (a página pode estar em inglês)](http://azure.microsoft.com/develop/net/tutorials/web-site-with-sql-database/) é baseado neste tutorial e mostra como implantar um aplicativo web com o banco de dados de associação.

Outra maneira de armazenar dados em um aplicativo do Azure é usar o Armazenamento do Azure, que fornece armazenamento de dados não relacionais na forma de blobs e tabelas. Os links a seguir fornecem mais informações sobre a API Web, o ASP.NET MVC e o Microsoft Azure.
 

* [Introdução ao Entity Framework utilizando MVC][EFCodeFirstMVCTutorial]
* [Introdução ao ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [Sua primeira API Web ASP.NET](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)
* [Depurando WAWS](http://azure.microsoft.com/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/)

Este tutorial e o aplicativo de exemplo foram escritos por [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) com a assistência de Tom Dykstra e Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)). 

Deixe comentários sobre o que você gostou ou sobre o que você gostaria de ver melhorado, não apenas sobre o próprio tutorial, mas também sobre os produtos que ele demonstra. Seus comentários nos ajudarão a priorizar melhorias. Estamos especialmente interessados em saber quanto há de interesse em mais automação para o processo de configuração e implantação de banco de dados de associação. 

<!-- bookmarks -->
[Adicionar um provedor de OAuth]: #addOauth
[Adicionar funções ao banco de dados de associação]:#mbrDB
[Criar um Script de implantação de dados]:#ppd
[Atualizar o banco de dados de associação]:#ppd2
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


<!--HONumber=42-->
