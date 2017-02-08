---
title: "Criar um aplicativo Web do Azure que se conecte ao MongoDB em execução em uma máquina virtual"
description: "Um tutorial que ensina como usar o Git para implantar um aplicativo ASP.NET no Serviço de Aplicativo do Azure, conectado ao MongoDB em uma Máquina Virtual do Azure."
tags: azure-portal
services: app-service\web, virtual-machines
documentationcenter: .net
author: cephalin
manager: wpickett
editor: 
ms.assetid: adf7a472-ae00-45a8-aec4-06247e21318b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/29/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 5ce82ddd2f58fed338bd061fa963978aa63e5fdc


---
# <a name="create-a-web-app-in-azure-that-connects-to-mongodb-running-on-a-virtual-machine"></a>Criar um aplicativo Web do Azure que se conecte ao MongoDB em execução em uma máquina virtual
Usando Git, é possível implantar um aplicativo ASP.NET em Aplicativos Web do Serviço de Aplicativo do Azure. Neste tutorial, você compilará um aplicativo de lista de tarefas MVC do ASP.NET de front-end simples que se conecta a um banco de dados MongoDB em execução em uma máquina virtual no Azure.  [O MongoDB][MongoDB] é um popular banco de dados NoSQL de código-fonte aberto e de alto desempenho. Depois de executar e testar o aplicativo ASP.NET no computador de desenvolvimento, você carregará o aplicativo nos Aplicativos Web do Serviço de Aplicativo usando Git.

> [!NOTE]
> Se desejar começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

## <a name="background-knowledge"></a>Conhecimento prévio
O conhecimento dos seguintes itens é útil para este tutorial, embora não seja obrigatório:

* O driver do C# para MongoDB. Para obter mais informações sobre como desenvolver aplicativos do C# para MongoDB, consulte o MongoDB [Central da linguagem CSharp][MongoC#LangCenter]. 
* A estrutura do aplicativo Web do ASP.NET. É possível aprender tudo sobre ela no [Site do ASP.NET][ASP.NET].
* A estrutura do aplicativo Web MVC do ASP.NET. É possível aprender tudo sobre ela no [Site do ASP.NET MVC][MVCWebSite].
* Azure. Você pode começar lendo em [Azure][WindowsAzure].

## <a name="prerequisites"></a>Pré-requisitos
* [Visual Studio Express 2013 para Web][VSEWeb] ou [Visual Studio 2013][VSUlt]
* [SDK do Azure para .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)
* Uma assinatura ativa do Microsoft Azure

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<a id="virtualmachine"></a> 

## <a name="create-a-virtual-machine-and-install-mongodb"></a>Criar uma máquina virtual e instalar MongoDB
Este tutorial supõe que você tenha criado uma máquina virtual no Azure. Depois de criar a máquina virtual, você precisa instalar o MongoDB na máquina virtual:

* Para criar uma máquina virtual do Windows e instalar MongoDB, consulte [Instalar MongoDB em uma máquina virtual executando o Windows Server no Azure][InstallMongoOnWindowsVM].

Depois de criar a máquina virtual no Azure e instalar MongoDB, não se esqueça do nome DNS da máquina virtual ("testlinuxvm.cloudapp.net", por exemplo) e da porta externa de MongoDB que você especificou no ponto de extremidade.  Você precisará dessas informações mais tarde no tutorial.

<a id="createapp"></a>

## <a name="create-the-application"></a>Criar o aplicativo
Nesta seção, você criará um aplicativo ASP.NET chamado "Minha Lista de Tarefas" usando o Visual Studio e realizará uma implantação inicial nos Aplicativos Web do Serviço de Aplicativo do Azure. Você executará o aplicativo localmente, mas ele se conectará à máquina virtual no Azure e usará a instância de MongoDB que você criou aqui.

1. No Visual Studio, clique em **Novo Projeto**.
   
    ![Iniciar página Novo Projeto][StartPageNewProject]
2. Na janela **Novo Projeto**, no painel à esquerda, escolha **Visual C#** e, em seguida, **Web**. No painel do meio, escolha **Aplicativo Web ASP.NET**. Na parte inferior, dê ao projeto o nome de "MyTaskListApp" e clique em **OK**.
   
    ![Caixa de diálogo Novo Projeto][NewProjectMyTaskListApp]
3. Na caixa de diálogo **Novo Projeto ASP.NET**, escolha **MVC** e clique em **OK**.
   
    ![Selecionar modelo do MVC][VS2013SelectMVCTemplate]
4. Se você ainda não tiver entrado no Microsoft Azure, será solicitada a sua conexão. Siga os prompts para entrar no Azure.
5. Depois de se conectar, você poderá começar a configurar seu aplicativo Web do Serviço de Aplicativo. Especifique o **Nome do Aplicativo Web**, o **plano do Serviço de Aplicativo**, o **Grupo de recursos** e a **Região** e clique em **Criar**.
   
    ![](./media/web-sites-dotnet-store-data-mongodb-vm/VSConfigureWebAppSettings.png)
6. Após a conclusão da criação do projeto, espere até que o aplicativo Web seja criado no Serviço de Aplicativo do Azure, conforme indicado na janela **Atividade do Serviço de Aplicativo do Azure** . Em seguida, clique em **Publicar MyTaskListApp para este Aplicativo Web agora**.
7. Clique em **Publicar**.
   
    ![](./media/web-sites-dotnet-store-data-mongodb-vm/VSPublishWeb.png)
   
    Depois que o aplicativo ASP.NET padrão for publicado nos Aplicativos Web do Serviço de Aplicativo do Azure, ele será iniciado no navegador.

## <a name="install-the-mongodb-c-driver"></a>Instalar o driver do C# para MongoDB
O MongoDB dá suporte do lado do cliente para aplicativos do C# por meio de um driver, que você precisa instalar no computador de desenvolvimento local. O driver do C# está disponível por meio de NuGet.

Para instalar o driver do C# para MongoDB:

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **MyTaskListApp** e escolha **Gerenciar NuGetPackages**.
   
    ![Gerenciar Pacotes NuGet][VS2013ManageNuGetPackages]
2. Na janela **Gerenciar Pacotes NuGet**, no painel à esquerda, clique em **Online**. Na caixa **Pesquisar Online** à direita, digite "mongodb.driver".  Clique em **Instalar** para instalar o driver.
   
    ![Procurar o driver do C# para MongoDB][SearchforMongoDBCSharpDriver]
3. Clique em **Aceito** para aceitar os termos de licença da 10gen, Inc.
4. Clique em **Fechar** depois que o driver for instalado.
    ![Driver do C# para MongoDB instalado][MongoDBCsharpDriverInstalled]

O driver do C# para MongoDB agora está instalado.  Referências às bibliotecas **MongoDB.Bson**, **MongoDB.Driver** e **MongoDB.Driver.Core** foram adicionadas ao projeto.

![Referências do driver do C# para MongoDB][MongoDBCSharpDriverReferences]

## <a name="add-a-model"></a>Adicionar um modelo
No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Modelos* e **adicione** uma nova **Classe**, dando a ela o nome de *TaskModel.cs*.  Em *TaskModel.cs*, substitua o código existente pelo seguinte código:

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

## <a name="add-the-data-access-layer"></a>Adicionar a camada de acesso aos dados
No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto *MyTaskListApp* e **adicione** uma **Nova Pasta** chamada *DAL*.  Clique com botão direito do mouse na pasta *DAL* e **adicione** uma nova **Classe**. Nomeie o arquivo de classe *Dal.cs*.  Em *Dal.cs*, substitua o código existente pelo seguinte código:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using MongoDB.Bson;
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
            private string connectionString = "mongodb://mongodbsrv20151211.cloudapp.net";

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
                    var collection = GetTasksCollection();
                    return collection.Find(new BsonDocument()).ToList();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask>();
                }
            }

            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                var collection = GetTasksCollectionForEdit();
                try
                {
                    collection.InsertOne(task);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }

            private IMongoCollection<MyTask> GetTasksCollection()
            {
                MongoClient client = new MongoClient(connectionString);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }

            private IMongoCollection<MyTask> GetTasksCollectionForEdit()
            {
                MongoClient client = new MongoClient(connectionString);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
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

## <a name="add-a-controller"></a>Adicionar um controlador
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

## <a name="set-up-the-styles"></a>Configurar os estilos
Para alterar o título na parte superior da página, abra o arquivo *Views\Shared\\_Layout.cshtml* no **Gerenciador de Soluções** e substitua "Nome do aplicativo" no cabeçalho da barra de navegação por "Aplicativo Minha Lista de Tarefas" para que se pareça com este exemplo:

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


Para adicionar a capacidade de criar uma nova tarefa, clique com botão direito do mouse na pasta *Views\Home\\\* e **adicione** um **Modo de exibição**.  Dê ao modo de exibição o nome *Criar*. Substitua o código pelo seguinte código:

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

**Gerenciador de soluções** deve ser assim:

![Gerenciador de soluções][SolutionExplorerMyTaskListApp]

## <a name="set-the-mongodb-connection-string"></a>Defina a cadeia de conexão MongoDB.
No **Gerenciador de soluções**, abra o arquivo *DAL/Dal.cs* . Localize a seguinte linha de código:

    private string connectionString = "mongodb://<vm-dns-name>";

Substitua `<vm-dns-name>` pelo nome DNS da máquina virtual executando MongoDB criado na etapa [Criar uma máquina virtual e instalar o MongoDB][Create a virtual machine and install MongoDB] deste tutorial.  Para encontrar o nome DNS da máquina virtual, vá até o portal do Azure, escolha **Máquinas Virtuais** e localize **Nome DNS**.

Se o nome DNS da máquina virtual for "testlinuxvm.cloudapp.net" e MongoDB estiver escutando na porta padrão 27017, a linha da cadeia de conexão do código será assim:

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

Se o ponto de extremidade da máquina virtual especificar uma porta externa diferente para MongoDB, você poderá especificar a porta na cadeia de conexão:

     private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

Para obter mais informações sobre cadeias de conexão de MongoDB, consulte [Conexões][MongoConnectionStrings].

## <a name="test-the-local-deployment"></a>Testar a implantação local
Para executar o aplicativo no computador de desenvolvimento, escolha **Iniciar Depuração** no menu **Depurar** ou pressione **F5**. O IIS Express é iniciado, e um navegador abre e inicia a página inicial do aplicativo.  É possível adicionar uma nova tarefa, que será adicionada ao banco de dados MongoDB em execução na máquina virtual do Azure.

![Aplicativo Minha Lista de Tarefas][TaskListAppBlank]

## <a name="publish-to-azure-app-service-web-apps"></a>Publicar para Aplicativos Web do Serviço de Aplicativo do Azure
Nesta seção, você publicará as alterações feitas nos Aplicativos Web do Serviço de Aplicativo do Azure.

1. No Gerenciador de Soluções, clique novamente com o botão direito do mouse em **MyTaskListApp** e clique em **Publicar**.
2. Clique em **Publicar**.
   
    Agora você já deve conseguir ver seu aplicativo Web em execução no Serviço de Aplicativo do Azure e acessar o banco de dados do MongoDB em Máquinas Virtuais do Azure.

## <a name="summary"></a>Resumo
Você já implantou o aplicativo ASP.NET com êxito para os Aplicativos Web do Serviço de Aplicativo do Azure. Para exibir o aplicativo Web:

1. Faça logon no Portal do Azure.
2. Clique em **Aplicativos Web**. 
3. Selecione seu aplicativo Web na lista **Aplicativos Web** .

Para obter mais informações sobre como desenvolver aplicativos do C# para MongoDB, consulte a [Central da linguagem CSharp][MongoC#LangCenter]. 

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnWindowsVM]: ../virtual-machines/virtual-machines-windows-classic-install-mongodb.md
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
[Create a virtual machine and install MongoDB]: #virtualmachine
[Create and run the My Task List ASP.NET application on your development computer]: #createapp
[Create an Azure web site]: #createwebsite
[Deploy the ASP.NET application to the web site using Git]: #deployapp



<!--HONumber=Jan17_HO3-->


