<properties 
	pageTitle="Criar um aplicativo Web do Azure que se conecte ao MongoDB em execução em uma máquina virtual" 
	description="Um tutorial que ensina como usar o Git para implantar um aplicativo ASP.NET no Serviço de Aplicativo do Azure, conectado ao MongoDB em uma Máquina Virtual do Azure."
	tags="azure-portal" 
	services="app-service\web, virtual-machines" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="cephalin"/>


# Criar um aplicativo Web do Azure que se conecte ao MongoDB em execução em uma máquina virtual

Usando Git, é possível implantar um aplicativo ASP.NET em Aplicativos Web do Serviço de Aplicativo do Azure. Neste tutorial, você compilará um aplicativo de lista de tarefas MVC do ASP.NET de front-end simples que se conecta a um banco de dados MongoDB em execução em uma máquina virtual no Azure. [O MongoDB][MongoDB] é um banco de dados NoSQL de código-fonte aberto e com alto desempenho. Depois de executar e testar o aplicativo ASP.NET no computador de desenvolvimento, você carregará o aplicativo nos Aplicativos Web do Serviço de Aplicativo usando Git.

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.


## Conhecimento prévio ##

O conhecimento dos seguintes itens é útil para este tutorial, embora não seja obrigatório:

* O driver do C# para MongoDB. Para obter mais informações sobre como desenvolver aplicativos do C# para MongoDB, consulte o MongoDB [Central da linguagem CSharp][MongoC#LangCenter] 
* A estrutura do aplicativo Web do ASP.NET. É possível aprender tudo sobre ela no [Site do ASP.NET][ASP.NET].
* A estrutura do aplicativo Web MVC do ASP.NET. É possível aprender tudo sobre ela no [Site do ASP.NET MVC][MVCWebSite].
* Azure. Você pode começar lendo em [Azure][WindowsAzure]

## Pré-requisitos ##

- [Visual Studio Express 2013 para Web][VSEWeb] ou [Visual Studio 2013][VSUlt]
- [SDK do Azure para .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)
- Uma assinatura ativa do Microsoft Azure

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<a id="virtualmachine"></a>
## Criar uma máquina virtual e instalar MongoDB ##

Este tutorial supõe que você tenha criado uma máquina virtual no Azure. Depois de criar a máquina virtual, você precisa instalar o MongoDB na máquina virtual:

* Para criar uma máquina virtual do Windows e instalar MongoDB, consulte [Instalar MongoDB em uma máquina virtual executando o Windows Server no Azure][InstallMongoOnWindowsVM].
* Como opção, para criar uma máquina virtual do Linux e instalar MongoDB, consulte [Instalar MongoDB em uma máquina virtual executando o CentOS Linux no Azure][InstallMongoOnCentOSLinuxVM].

Depois de criar a máquina virtual no Azure e instalar MongoDB, não se esqueça do nome DNS da máquina virtual ("testlinuxvm.cloudapp.net", por exemplo) e da porta externa de MongoDB que você especificou no ponto de extremidade. Você precisará dessas informações mais tarde no tutorial.

<a id="createapp"></a>
## Criar o aplicativo ##

Nesta seção, você criará um aplicativo ASP.NET chamado "Minha Lista de Tarefas" usando o Visual Studio e realizará uma implantação inicial nos Aplicativos Web do Serviço de Aplicativo do Azure. Você executará o aplicativo localmente, mas ele se conectará à máquina virtual no Azure e usará a instância de MongoDB que você criou aqui.

1. No Visual Studio, clique em **Novo Projeto**.

	![Iniciar página Novo Projeto][StartPageNewProject]

1. Na janela **Novo Projeto**, no painel à esquerda, selecione **Visual C#**e **Web**. No painel intermediário, selecione **Aplicativo Web do ASP.NET** Na parte inferior, nomeie o projeto "MyTaskListApp" e clique em **OK**.

	![Caixa de diálogo Novo Projeto][NewProjectMyTaskListApp]

1. Na caixa de diálogo **Novo Projeto do ASP .NET**, selecione **MVC** e clique em **OK**.

	![Selecionar modelo do MVC][VS2013SelectMVCTemplate]

1. Se você ainda não tiver entrado no Microsoft Azure, será solicitada a sua conexão. Siga os prompts para entrar no Azure.
2. Depois de se conectar, você poderá começar a configurar seu aplicativo Web do Serviço de Aplicativo. Especifique o **Nome do Aplicativo Web**, o **Plano Serviço do Aplicativo**, o **Grupo de recursos** e a **Região** e, em seguida, clique em **OK**.

	![](./media/web-sites-dotnet-store-data-mongodb-vm/VSConfigureWebAppSettings.png)

1. Após a conclusão da criação do projeto, espere até que o aplicativo Web seja criado no Serviço de Aplicativo do Azure, conforme indicado na janela **Atividade do Serviço de Aplicativo do Azure**. Em seguida, clique em **Publicar MyTaskListApp para este Aplicativo Web agora**.

1. Clique em **Publicar**.

	![](./media/web-sites-dotnet-store-data-mongodb-vm/VSPublishWeb.png)

	Depois que o aplicativo ASP.NET padrão for publicado nos Aplicativos Web do Serviço de Aplicativo do Azure, ele será iniciado no navegador.

## Instalar o driver do C# para MongoDB

O MongoDB dá suporte do lado do cliente para aplicativos do C# por meio de um driver, que você precisa instalar no computador de desenvolvimento local. O driver do C# está disponível por meio de NuGet.

Para instalar o driver do C# para MongoDB:

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **MyTaskListApp** e selecione **Gerenciar NuGetPackages**.

	![Gerenciar Pacotes NuGet][VS2013ManageNuGetPackages]

2. Na janela **Gerenciar Pacotes NuGet**, no painel à esquerda, clique em **Online**. Na caixa **Pesquisar Online** à direita, digite "mongocsharpdriver". Clique em **Instalar** para instalar o driver.

	![Procurar o driver do C# para MongoDB][SearchforMongoDBCSharpDriver]

3. Clique em **Aceito** para aceitar os termos de licença da 10gen, Inc.

4. Clique em **Fechar** depois que o driver for instalado. ![Driver do C# para MongoDB instalado][MongoDBCsharpDriverInstalled]


O driver do C# para MongoDB agora está instalado. Referências às bibliotecas **MongoDB.Driver.dll** e **MongoDB.Bson.dll** foram adicionadas ao projeto.

![Referências do driver do C# para MongoDB][MongoDBCSharpDriverReferences]

## Adicionar um modelo ##
No **Gerenciador de soluções**, clique com o botão direito do mouse na pasta *Modelos* e **Adicione** uma nova **Classe** e nomeie-a *TaskModel.cs*. Em *TaskModel.cs*, substitua o código existente pelo seguinte código:

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

## Adicionar a camada de acesso aos dados ##
No **Gerenciador de soluções**, clique com o botão direito do mouse no projeto *MyTaskListApp* e **Adicione** uma **Nova Pasta** chamada *DAL*. Clique com botão direito do mouse na pasta *DAL* e clique em **Adicionar** uma nova **Classe**. Nomeie o arquivo de classe *Dal.cs*. Em *Dal.cs*, substitua o código existente pelo seguinte código:

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

## Adicionar um controlador ##
abra o arquivo * Controllers\\HomeController.cs* no **Gerenciador de soluções** e substitua o código existente com o seguinte:

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

## Configurar os estilos ##
Para alterar o título na parte superior da página, abra o arquivo *Views\\Shared\\_Layout.cshtml* no **Gerenciador de soluções** e substitua "Nome do aplicativo" no cabeçalho da barra de navegação por "Aplicativo Minha Lista de Tarefas" de forma que ele seja semelhante a:

 	@Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

Para configurar o menu Lista de Tarefas, abra o arquivo *\\Views\\Home\\Index.cshtml* e substitua o código existente pelo seguinte código:
	
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


Para adicionar a capacidade de criar uma nova tarefa, clique com botão direito do mouse na pasta *Views\\Home\* e clique em **Adicionar** uma **Exibição**. Dê o nome *Criar* à exibição. Substitua o código pelo seguinte código:

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

![Gerenciador de Soluções][SolutionExplorerMyTaskListApp]

## Defina a cadeia de conexão MongoDB. ##
No **Gerenciador de soluções**, abra o arquivo *DAL/Dal.cs*. Localize a seguinte linha de código:

	private string connectionString = "mongodb://<vm-dns-name>";

Substitua `<vm-dns-name>` pelo nome DNS da máquina virtual executando MongoDB criado na etapa [Criar uma máquina virtual e instalar o MongoDB][] deste tutorial. Para localizar o nome DNS da máquina virtual, vá até o Portal do Azure, selecione **Máquinas Virtuais** e localize **Nome DNS**.

Se o nome DNS da máquina virtual for "testlinuxvm.cloudapp.net" e MongoDB estiver escutando na porta padrão 27017, a linha da cadeia de conexão do código será assim:

	private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

Se o ponto de extremidade da máquina virtual especificar uma porta externa diferente para MongoDB, você poderá especificar a porta na cadeia de conexão:

 	private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

Para obter mais informações sobre cadeias de conexão de MongoDB, consulte [Conexões][MongoConnectionStrings].

## Testar a implantação local ##

Para executar o aplicativo no computador de desenvolvimento, selecione **Iniciar Depuração** no menu **Depurar** ou pressione **F5**. O IIS Express é iniciado, e um navegador abre e inicia a página inicial do aplicativo. É possível adicionar uma nova tarefa, que será adicionada ao banco de dados MongoDB em execução na máquina virtual do Azure.

![Aplicativo Minha Lista de Tarefas][TaskListAppBlank]

## Publicar para Aplicativos Web do Serviço de Aplicativo do Azure

Nesta seção, você publicará as alterações feitas nos Aplicativos Web do Serviço de Aplicativo do Azure.

1. No Gerenciador de Soluções, clique novamente com o botão direito do mouse em **MyTaskListApp** e clique em **Publicar**.
2. Clique em **Publicar**.

	Agora você já deve conseguir ver seu aplicativo Web em execução no Serviço de Aplicativo do Azure e acessar o banco de dados do MongoDB em Máquinas Virtuais do Azure.

## Resumo ##

Você já implantou o aplicativo ASP.NET com êxito para os Aplicativos Web do Serviço de Aplicativo do Azure. Para exibir o aplicativo Web:

1. Faça logon no portal do Azure.
2. Clique em **Aplicativos Web**. 
3. Selecione seu aplicativo Web na lista **Aplicativos Web**.

Para obter mais informações sobre como desenvolver aplicativos do C# para MongoDB, consulte a [Central da linguagem CSharp][MongoC#LangCenter].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 

<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnCentOSLinuxVM]: /manage/linux/common-tasks/mongodb-on-a-linux-vm/
[InstallMongoOnWindowsVM]: /manage/windows/common-tasks/install-mongodb/
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
[Create and run the My Task List ASP.NET application on your development computer]: #createapp
[Create an Azure web site]: #createwebsite
[Deploy the ASP.NET application to the web site using Git]: #deployapp
 

<!---HONumber=July15_HO3-->