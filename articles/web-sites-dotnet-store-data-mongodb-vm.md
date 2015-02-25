<properties 
	pageTitle="Site .NET com MongoDB em uma máquina virtual - Azure" 
	description="Um tutorial que ensina como usar o Git para implantar um aplicativo ASP.NET em um site do Azure conectado ao MongoDB em uma máquina virtual." 
	services="web-sites, virtual-machines" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 	
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>


# Criar um site do Azure que se conecte a MongoDB em execução em uma máquina virtual no Azure

Usando Git, é possível implantar um aplicativo do ASP.NET em um site do Azure. Neste tutorial, você compilará um aplicativo de lista de tarefas MVC do ASP.NET de front-end simples que se conecta a um banco de dados MongoDB em execução em uma máquina virtual no Azure.  [O MongoDB][MongoDB] é um banco de dados NoSQL de código-fonte aberto e com alto desempenho. Depois de executar e testar o aplicativo do ASP.NET no computador de desenvolvimento, você carregará o aplicativo em um site do Azure usando Git.

[AZURE.INCLUDE [create-account-and-websites-and-vms-note](../includes/create-account-and-websites-and-vms-note.md)]



##Visão geral##

Neste tutorial, você vai:

- [Criar uma máquina virtual e instalar o MongoDB](#virtualmachine)
- [Criar e executar o aplicativo Minha Lista de Tarefas do ASP.NET no computador de desenvolvimento](#createapp)
- [Criar um site do Azure](#createwebsite)
- [Implantar o aplicativo do ASP.NET no site usando Git](#deployapp)


##Conhecimento prévio##

O conhecimento dos seguintes itens é útil para este tutorial, embora não seja obrigatório:

* O driver do C# para MongoDB. Para obter mais informações sobre como desenvolver aplicativos do C# para MongoDB, consulte o [Central da linguagem CSharp][MongoC#LangCenter] do MongoDB. 
* A estrutura do aplicativo Web do ASP.NET. É possível aprender tudo sobre ela no [Site do ASP.NET][ASP.NET].
* A estrutura do aplicativo Web MVC do ASP.NET. É possível aprender tudo sobre ela no [Site do MVC do ASP.NET][MVCWebSite].
* Azure. Você pode começar lendo em [Azure][WindowsAzure].


##Preparação##

Nesta seção, você aprenderá a criar uma máquina virtual no Azure e a instalar MongoDB, além de configurar o ambiente de desenvolvimento.

<a id="virtualmachine"></a> 
###Criar uma máquina virtual e instalar o MongoDB###

Este tutorial supõe que você tenha criado uma máquina virtual no Azure. Depois de criar a máquina virtual, você precisa instalar o MongoDB na máquina virtual:

* Para criar uma máquina virtual do Windows e instalar MongoDB, consulte [Instalar MongoDB em uma máquina virtual executando o Windows Server no Azure][InstallMongoOnWindowsVM].
* Como opção, para criar uma máquina virtual do Linux e instalar MongoDB, consulte [Instalar MongoDB em uma máquina virtual executando o CentOS Linux no Azure][InstallMongoOnCentOSLinuxVM].

Depois de criar a máquina virtual no Azure e instalar MongoDB, não se esqueça do nome DNS da máquina virtual ("testlinuxvm.cloudapp.net", por exemplo) e da porta externa de MongoDB que você especificou no ponto de extremidade.  Você precisará dessas informações mais tarde no tutorial.

### Instalar Visual Studio###

Começar pela instalação e pela execução [Visual Studio Express 2013 para Web][VSEWeb] ou [Visual Studio 2013][VSUlt].

Visual Studio é um IDE ou ambiente de desenvolvimento integrado. Assim como usa o Microsoft Word para escrever documentos, você usará um IDE para criar aplicativos. Este tutorial usa o Microsoft Visual Studio 2013, mas é possível usar o Microsoft Visual Studio Express 2013, que é uma versão gratuita do Microsoft Visual Studio.

<a id="createapp"></a>
##Criar e executar o aplicativo Minha Lista de Tarefas do ASP.NET no computador de desenvolvimento##

Nesta seção, você criará um aplicativo do ASP.NET chamado "Minha Lista de Tarefas" usando o Visual Studio.  Você executará o aplicativo localmente, mas ele se conectará à máquina virtual no Azure e usará a instância de MongoDB que você criou aqui.

###Criar o aplicativo###
No Visual Studio, clique em **Novo Projeto**.

![Start Page New Project][StartPageNewProject]

Na janela **Novo Projeto**, no painel à esquerda, selecione **Visual C#**e **Web**. No painel intermediário, selecione **Aplicativo Web do ASP.NET**. Na parte inferior, nomeie o projeto "MyTaskListApp" e clique em **OK**.

![New Project Dialog][NewProjectMyTaskListApp]

Na caixa de diálogo **Novo Projeto do ASP .NET**, selecione **MVC** e clique em **OK**.

![Select MVC Template][VS2013SelectMVCTemplate]

Depois que o projeto for concluído, a página padrão criada pelo modelo será exibida.

![Default ASP.NET MVC Application][VS2013DefaultMVCApplication]

###Instalar o driver do C# para MongoDB

O MongoDB dá suporte do lado do cliente para aplicativos do C# por meio de um driver, que você precisa instalar no computador de desenvolvimento local. O driver do C# está disponível por meio de NuGet.

Para instalar o driver do C# para MongoDB:

1. No **Gerenciador de Soluções**, no projeto **MyTaskListApp**, clique com o botão direito do mouse em **Referências** e selecione **Gerenciar Pacotes NuGet**.

	![Manage NuGet Packages][VS2013ManageNuGetPackages]

2. Na janela **Gerenciar Pacotes NuGet**, no painel à esquerda, clique em **Online**. Na caixa **Pesquisar Online** à direita, digite "mongocsharpdriver".  Clique em **Instalar** para instalar o driver.

	![Search for MongoDB C# Driver][SearchforMongoDBCSharpDriver]

3. Clique em **Aceito** para aceitar os termos de licença da 10gen, Inc.

4. Clique em **Fechar** depois que o driver for instalado.
	![MongoDB C# Driver Installed][MongoDBCsharpDriverInstalled]


O driver do C# para MongoDB agora está instalado.  Referências às bibliotecas **MongoDB.Driver.dll** e **MongoDB.Bson.dll** foram adicionadas ao projeto.

![MongoDB C# Driver References][MongoDBCSharpDriverReferences]

###Adicionar um modelo###
No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Models* e **Adicione** uma nova **classe** e nomeie-a *TaskModel.cs*.  Em *TaskModel.cs*, substitua o código existente pelo código a seguir:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MongoDB.Bson.Serialization.Attributes;
	using MongoDB.Bson.Serialization.IdGenerators;
	using MongoDB.Bson;
	
	namespace MyTaskListApp.Models
	{
	    public class MyTask
	    {
	        [BsonId(IdGenerator = typeof(CombGuidGenerator))]
	        public Guid Id { get; set; }
	
	        [BsonElement("Name")]
	        public string Name { get; set; }
	
	        [BsonElement("Category")]
	        public string Category { get; set; }
	
	        [BsonElement("Date")]
	        public DateTime Date { get; set; }
	
	        [BsonElement("CreatedDate")]
	        public DateTime CreatedDate { get; set; }
	
	    }
	}

###Adicionar a camada de acesso aos dados###
No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto *MyTaskListApp* e **adicione** uma **nova pasta** chamada *DAL*.  Clique com o botão direito do mouse na pasta *DAL* e **adicione** uma nova **classe**. Nomeie o arquivo de classe *Dal.cs*.  Em *Dal.cs*, substitua o código existente pelo código a seguir:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MyTaskListApp.Models;
	using MongoDB.Driver;
	using System.Configuration;
	
	namespace MyTaskListApp
	{
	    public class Dal : IDisposable
	    {
	        private MongoServer mongoServer = null;
	        private bool disposed = false;
	
	        // To do: update the connection string with the DNS name
			// or IP address of your server. 
			//For example, "mongodb://testlinux.cloudapp.net"
	        private string connectionString = "mongodb://<vm-dns-name>";
	
	        // This sample uses a database named "Tasks" and a 
			//collection named "TasksList".  The database and collection 
			//will be automatically created if they don't already exist.
	        private string dbName = "Tasks";
	        private string collectionName = "TasksList";
	
	        // Default constructor.        
	        public Dal()
	        {
	        }        
	
	        // Gets all Task items from the MongoDB server.        
	        public List<MyTask> GetAllTasks()
	        {
	            try
	            {
	                MongoCollection<MyTask> collection = GetTasksCollection();
	                return collection.FindAll().ToList<MyTask>();
	            }
	            catch (MongoConnectionException)
	            {
	                return new List<MyTask >();
	            }
	        }
	
	        // Creates a Task and inserts it into the collection in MongoDB.
	        public void CreateTask(MyTask task)
	        {
	            MongoCollection<MyTask> collection = GetTasksCollectionForEdit();
	            try
	            {
	                collection.Insert(task, SafeMode.True);
	            }
	            catch (MongoCommandException ex)
	            {
	                string msg = ex.Message;
	            }
	        }
	
	        private MongoCollection<MyTask> GetTasksCollection()
	        {
	            MongoServer server = MongoServer.Create(connectionString);
	            MongoDatabase database = server[dbName];
	            MongoCollection<MyTask> todoTaskCollection = database.GetCollection<MyTask>(collectionName);
	            return todoTaskCollection;
	        }
	
	        private MongoCollection<MyTask> GetTasksCollectionForEdit()
	        {
	            MongoServer server = MongoServer.Create(connectionString);
	            MongoDatabase database = server[dbName];
	            MongoCollection<MyTask> todoTaskCollection = database.GetCollection<MyTask>(collectionName);
	            return todoTaskCollection;
	        }
	
	        # region IDisposable
	
	        public void Dispose()
	        {
	            this.Dispose(true);
	            GC.SuppressFinalize(this);
	        }
	
	        protected virtual void Dispose(bool disposing)
	        {
	            if (!this.disposed)
	            {
	                if (disposing)
	                {
	                    if (mongoServer != null)
	                    {
	                        this.mongoServer.Disconnect();
	                    }
	                }
	            }
	
	            this.disposed = true;
	        }
	
	        # endregion
	    }
	}

###Adicionar um controlador###
Abra o arquivo *Controllers\HomeController.cs* no **Gerenciador de Soluções** e substitua o código existente pelo seguinte:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.Web.Mvc;
	using MyTaskListApp.Models;
	using System.Configuration;
	
	namespace MyTaskListApp.Controllers
	{
	    public class HomeController : Controller, IDisposable
	    {
	        private Dal dal = new Dal();
	        private bool disposed = false;
	        //
	        // GET: /MyTask/
	
	        public ActionResult Index()
	        {
	            return View(dal.GetAllTasks());
	        }
	
	        //
	        // GET: /MyTask/Create
	
	        public ActionResult Create()
	        {
	            return View();
	        }
	
	        //
	        // POST: /MyTask/Create
	
	        [HttpPost]
	        public ActionResult Create(MyTask task)
	        {
	            try
	            {
	                dal.CreateTask(task);
	                return RedirectToAction("Index");
	            }
	            catch
	            {
	                return View();
	            }
	        }
	
	        public ActionResult About()
	        {
	            return View();
	        }
	
	        # region IDisposable
	
	        new protected void Dispose()
	        {
	            this.Dispose(true);
	            GC.SuppressFinalize(this);
	        }
	
	        new protected virtual void Dispose(bool disposing)
	        {
	            if (!this.disposed)
	            {
	                if (disposing)
	                {
	                    this.dal.Dispose();
	                }
	            }
	
	            this.disposed = true;
	        }
	
	        # endregion
	
	    }
	}

###Configure o estilo do site###
Para alterar o título na parte superior da página, abra o arquivo  *Views\Shared\\_Layout.cshtml* no **Gerenciador de Soluções** e substitua "Nome do aplicativo" no cabeçalho da barra de navegação por "Aplicativos de Minha Lista de Tarefas" de forma que ele seja semelhante a:

 	@Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

Para configurar o menu Lista de Tarefas, abra o arquivo *\Views\Home\Index.cshtml* e substitua o código existente pelo seguinte código:
	
	@model IEnumerable<MyTaskListApp.Models.MyTask>
	
	@{
	    ViewBag.Title = "My Task List";
	}
	
	<h2>My Task List</h2>
	
	<table border="1">
	    <tr>
	        <th>Task</th>
	        <th>Category</th>
	        <th>Date</th>
	        
	    </tr>
	
	@foreach (var item in Model) {
	    <tr>
	        <td>
	            @Html.DisplayFor(modelItem => item.Name)
	        </td>
	        <td>
	            @Html.DisplayFor(modelItem => item.Category)
	        </td>
	        <td>
	            @Html.DisplayFor(modelItem => item.Date)
	        </td>
	        
	    </tr>
	}
	
	</table>
	<div>  @Html.Partial("Create", new MyTaskListApp.Models.MyTask())</div>


Para adicionar a capacidade de criar uma nova tarefa, clique com botão direito do mouse na pasta  *Views\Home\\* e clique em **Adicionar** uma **Exibição**.  Nomeie a exibição *Create*. Substitua o código pelo seguinte código:

	@model MyTaskListApp.Models.MyTask
	
	<script src="@Url.Content("~/Scripts/jquery-1.10.2.min.js")" type="text/javascript"></script>
	<script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
	<script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
	
	@using (Html.BeginForm("Create", "Home")) {
	    @Html.ValidationSummary(true)
	    <fieldset>
	        <legend>New Task</legend>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Name)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Name)
	            @Html.ValidationMessageFor(model => model.Name)
	        </div>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Category)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Category)
	            @Html.ValidationMessageFor(model => model.Category)
	        </div>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Date)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Date)
	            @Html.ValidationMessageFor(model => model.Date)
	        </div>
	
	        <p>
	            <input type="submit" value="Create" />
	        </p>
	    </fieldset>
	}

O **Gerenciador de Soluções** deve ser assim:

![Solution Explorer][SolutionExplorerMyTaskListApp]

###Defina a cadeia de conexão MongoDB###
No **Gerenciador de Soluções**, abra o arquivo *DAL/Dal.cs*. Localize a seguinte linha de código:

	private string connectionString = "mongodb://<vm-dns-name>";

Substitua `<vm-dns-name>` pelo nome DNS da máquina virtual executando MongoDB criado na etapa [Criar uma máquina virtual e instalar MongoDB][] deste tutorial.  Para localizar o nome DNS da máquina virtual, vá até o Portal de Gerenciamento do Azure, selecione **Máquinas Virtuais**e **Nome DNS**.

Se o nome DNS da máquina virtual for "testlinuxvm.cloudapp.net" e MongoDB estiver escutando na porta padrão 27017, a linha da cadeia de conexão do código será assim:

	private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

Se o ponto de extremidade da máquina virtual especificar uma porta externa diferente para MongoDB, você poderá especificar a porta na cadeia de conexão:

 	private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

Para obter mais informações sobre cadeias de conexão de MongoDB, consulte [Conexões][MongoConnectionStrings].

###Testar a implantação local###

Para executar o aplicativo no computador de desenvolvimento, selecione **Iniciar Depuração** no menu **Depurar** ou pressione **F5**. O IIS Express é iniciado, e um navegador abre e inicia a página inicial do aplicativo.  É possível adicionar uma nova tarefa, que será adicionada ao banco de dados MongoDB em execução na máquina virtual do Azure.

![My Task List Application][TaskListAppBlank]

<h2>Implantar o aplicativo ASP.NET em um Site do Azure</h2>

Nesta seção, você criará um site e implantará o aplicativo Minha Lista de Tarefas do ASP.NET usando Git.

<a id="createwebsite"></a> 
###Criar um site do Azure###
Nesta seção você criará um site do Azure.

1. Abra um navegador da Web e navegue até o [Portal de Gerenciamento do Azure][AzurePortal]. Faça logon usando sua conta do Azure. 
2. Na parte inferior da página, clique em **+Novo**, **Site** e, por fim, **Criação Rápida**.
3. Insira um prefixo exclusivo para a URL do aplicativo.
4. Selecione uma região.
5. Clique em **Criar Site**.

![Create a new web site][WAWSCreateWebSite]

6. Seu site será criado rapidamente e será listado em **Sites**.

![WAWSDashboardMyTaskListApp][WAWSDashboardMyTaskListApp]

<a id="deployapp"></a> 
###Implantar o aplicativo do ASP.NET no site usando Git
Nesta seção você implantará o aplicativo Minha Lista de Tarefas usando o Git.

1. Clique no nome do site em **Sites** e, em seguida, clique em **Painel**.  No lado direito, em Visão Rápida, clique em **Configurar implantação com base no controle de origem**.
2. Na página **Onde está o código-fonte?**, escolha **Repositório Git Local** e clique na seta **Avançar**. 
3. O repositório Git deve ser criado rapidamente. Observe as instruções na página resultante à medida que serão usadas na próxima seção.

	![Git Repository is Ready][Image9]

4. Em **Enviar meus arquivos locais por push para o Azure** existem instruções sobre como enviar o código para o Azure por push. As instruções serão semelhantes às seguintes:

	![Push local files to Azure][Image10]
	
5. Se você não tiver o Git instalado, instale-o usando o link **Obtenha-o aqui** na etapa 1.
6. Seguindo as instruções na etapa 2, confirme os arquivos locais.  
7. Adicione o repositório do Azure remoto e envie os arquivos por push para o site do Azure seguindo as instruções na etapa 3.
8. Quando a implantação for concluída, você verá a seguinte confirmação:

	![Deployment Complete][Image11]

9. Agora o site do Azure está disponível.  Consulte a página **Painel** do site e o campo **URL do Site** campo para localizar a URL do site. Seguindo os procedimentos neste tutorial, o site deveria estar disponível na seguinte URL: http://mytasklistapp.azurewebsites.net.

##Resumo##

Você já implantou o aplicativo do ASP.NET com êxito em um site do Azure.  Para exibir o site, clique no link do campo **URL do Site** da página **Painel**. Para obter mais informações sobre como desenvolver aplicativos do C# para MongoDB, consulte a [Central da linguagem CSharp][MongoC#LangCenter]. 


<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnCentOSLinuxVM]: /pt-br/manage/linux/common-tasks/mongodb-on-a-linux-vm/
[InstallMongoOnWindowsVM]: /pt-br/manage/windows/common-tasks/install-mongodb/
[VSEWeb]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express
[VSUlt]: http://www.microsoft.com/visualstudio/eng/2013-downloads

<!-- IMAGES -->


[StartPageNewProject]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png
[NewProjectMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png
[VS2013SelectMVCTemplate]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png
[VS2013DefaultMVCApplication]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png
[VS2013ManageNuGetPackages]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png
[SearchforMongoDBCSharpDriver]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png
[MongoDBCsharpDriverInstalled]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png
[MongoDBCSharpDriverReferences]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png
[SolutionExplorerMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png
[TaskListAppBlank]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png
[WAWSCreateWebSite]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png
[WAWSDashboardMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png
[Image9]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png
[Image10]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png
[Image11]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png

<!-- TOC BOOKMARKS -->
[Criar uma máquina virtual e instalar o MongoDB]: #virtualmachine
[Criar e executar o aplicativo Minha Lista de Tarefas do ASP.NET no computador de desenvolvimento]: #createapp
[Criar um site do Azure]: #createwebsite
[Implantar o aplicativo do ASP.NET no site usando Git]: #deployapp


<!--HONumber=42-->
