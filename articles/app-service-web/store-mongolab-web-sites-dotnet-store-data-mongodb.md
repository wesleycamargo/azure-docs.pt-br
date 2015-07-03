<properties 
	pageTitle="Criar um aplicativo Web do .NET no Azure com o MongoDB usando o complemento MongoLab" 
	description="Aprenda a criar um aplicativo Web ASP.NET no Serviço de Aplicativo do Azure que armazena dados no MongoDB hospedado pelo MongoLab." 
	tags="azure-classic-portal"
	services="app-service\web" 
	documentationCenter=".net" 
	authors="chrisckchang" 
	manager="partners@mongolab.com" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="chris@mongolab.com"/>



# Criar um aplicativo Web do .NET no Azure com o MongoDB usando o complemento MongoLab

  	<!-- The MongoLab workflow is not yet supported in the Preview Portal -->

<p><em>Por Eric Sedor, MongoLab</em></p>

Saudações, aventureiros! Bem-vindos ao MongoDB como serviço. Neste tutorial, você irá:

1. [Provisionar o Banco de dados][provision] - O complemento Azure Marketplace [MongoLab](http://mongolab.com) fornecerá um banco de dados MongoDB hospedado na nuvem do Azure e gerenciado pela plataforma de banco de dados em nuvem do MongoLab.
1. [Criar o aplicativo][create] -será um simples aplicativo C# ASP.NET MVC para fazer anotações.
1. [Implantar o aplicativo][deploy] - Vinculando alguns ganchos de configuração, enviaremos o nosso código por push para o [Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) rapidamente.
1. [Gerenciar o banco de dados][manage] - finalmente, mostraremos o portal de gerenciamento de banco de dados baseado na web do MongoLab, onde você poderá pesquisar, visualizar e modificar dados facilmente.

A qualquer momento durante este tutorial, fique à vontade para mandar um e-mail para [support@mongolab.com](mailto:support@mongolab.com) em caso de dúvidas.

## Início rápido
Se você já tiver um aplicativo Web no Serviço de Aplicativo do Azure com o qual deseja trabalhar ou se tiver alguma familiaridade com o Azure Marketplace, use esta seção para obter um início rápido. Caso contrário, vá para [Provisionar o banco de dados][provision] abaixo.
 
1. Abra o Azure Marketplace clicando em **Novo** > **Marketplace**. 
	<!-- ![Store][button-store] -->

1. Adquira o complemento MongoLab. 
	![MongoLab][entry-mongolab]

1. Clique no complemento MongoLab na lista Complementos e, em seguida, clique em **Informações de Conexão**. 
	![ConnectionInfoButton][button-connectioninfo]

1. Copie o MONGOLAB_URI para a sua área de transferência. 
	![ConnectionInfoScreen][screen-connectioninfo] 
	**Esse URI contém seu nome de usuário do banco de dados e senha. Tratá-lo como informações confidenciais e não a compartilhe.**

1. Adicione o valor à lista Cadeias de Conexão no menu Configuração do seu aplicativo Web do Azure: 
	![WebSiteConnectionStrings][focus-website-connectinfo]

1. Para **nome**, digite MONGOLAB\_URI.

1. Para **valor**, cole a cadeia de conexão que obtivemos na seção anterior.

1. Selecione **Personalizado** na caixa suspensa Tipo (em vez do padrão **SQLAzure**).

1. No Visual Studio, instale o Driver C# do Mongo, selecionando **Ferramentas > Gerenciador de Pacotes da Biblioteca > Console do Gerenciador de Pacotes**. No Console do PM, digite **Install-Package mongocsharpdriver** e pressione **Enter**.

1. Configure um gancho no código para obter o URI de conexão do MongoLab de uma variável de ambiente:

        using MongoDB.Driver;  
        ...
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        ...
        MongoUrl url = new MongoUrl(connectionString);
        MongoClient client = new MongoClient(url);

> **Observação:** o Azure adiciona o prefixo **CUSTOMCONNSTR\_** à cadeia de conexão declarada originalmente e é por isso que o código faz referência a **CUSTOMCONNSTR_MONGOLAB\_URI.** em vez de a **MONGOLAB\_URI**.

Agora, vamos para o tutorial completo...

<a name="provision"></a>
## Provisionar o banco de dados

[AZURE.INCLUDE [howto-provision-mongolab](../../includes/howto-provision-mongolab.md)]

<a name="create"></a>
## Crie o aplicativo

Nesta seção, você entenderá a criação de um projeto C# ASP.NET do Visual Studio e examinará o uso do driver C# do MongoDB para criar um aplicativo simples de Nota. Você deseja poder visitar seu aplicativo Web, escrever uma nota e exibir todas as notas que restaram.

Você executará esse desenvolvimento no Visual Studio Express 2013 para Web.

### Criar o projeto
O aplicativo de amostra usará um modelo do Visual Studio para começar. Certifique-se de usar o .NET Framework 4.5.

1. Selecione **Arquivo > Novo Projeto**. A caixa de diálogo Novo Projeto é exibida:    
	![NewProject][dialog-mongolab-csharp-newproject]

1. Selecione **Instalado > Modelos > Visual C# > Web**.

1. Selecione **.NET Framework 4.5** no menu suspenso de versão do .NET.

1. Escolha **Aplicativo MVC**.

1. Digite _mongoNotes_ como o **Nome do Projeto**. Se escolher um nome diferente, você precisará modificar o código fornecido em todo o tutorial.

1. Selecione **Ferramentas > Gerenciador de Pacotes da Biblioteca > Console do Gerenciador de Pacotes**. No Console do PM, digite **Install-Package mongocsharpdriver** e pressione **Enter**.  
	![PMConsole][focus-mongolab-csharp-pmconsole] O Driver C# do MongoDB é integrado ao projeto, e a linha a seguir é adicionada automaticamente ao arquivo _packages.config_:

        < package id="mongocsharpdriver" version="1.9.2" targetFramework="net45" / >

### Adicionar um modelo de nota
Primeiro, estabeleça um modelo para Notas, com apenas uma data e um conteúdo de texto.

1. Clique com o botão direito do mouse em **Modelos** no Gerenciador de Soluções e selecione **Adicionar > Classe**. Chame essa nova classe *Note.cs*.

1. Substitua o código gerado automaticamente para essa classe pelo seguinte:

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using MongoDB.Bson.Serialization.Attributes;
        using MongoDB.Bson.Serialization.IdGenerators;
        using MongoDB.Bson;
                
        namespace mongoNotes.Models
        {
            public class Note
            {
                public Note()
                {
                    Date = DateTime.UtcNow;
                }
                
                private DateTime date;
        
                [BsonId(IdGenerator = typeof(CombGuidGenerator))]
                public Guid Id { get; set; }
        
                [BsonElement("Note")]
                public string Text { get; set; }
        
                [BsonElement("Date")]
                public DateTime Date {
                    get { return date.ToLocalTime(); }
                    set { date = value;}
                }
            }
        }

### Adicionar uma camada de acesso aos dados
É importante que você estabeleça um meio de acessar MongoDB para recuperar e salvar as notas. Sua camada de acesso a dados usará o modelo de Nota e será vinculada ao seu HomeController posteriormente.

1. Clique com o botão direito do mouse no projeto **mongoNotes** no Gerenciador de Soluções e selecione **Adicionar > Nova Pasta**. Chame a pasta **DAL**.

1. Clique com o botão direito do mouse em **DAL** no Gerenciador de Soluções e selecione **Adicionar > Classe**. Chame essa nova classe *Dal.cs*.

1. Substitua o código gerado automaticamente para essa classe pelo seguinte:

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using mongoNotes.Models;
        using MongoDB.Driver;
        using System.Configuration;

        namespace mongoNotes
        {
            public class Dal : IDisposable
            {
                private MongoServer mongoServer = null;
                private bool disposed = false;
        
                private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
                MongoUrl url;
        
                private string dbName = "myMongoApp";
                private string collectionName = "Notes";
        
                // Default constructor.        
                public Dal()
                {
                    url = new MongoUrl(connectionString);
                }
           
                public List<Note> GetAllNotes()
                {
                    try
                    {
                        MongoCollection<Note> collection = GetNotesCollection();
                        return collection.FindAll().ToList<Note>();
                    }
                    catch (MongoConnectionException)
                    {
                        return new List<Note>();
                    }
                }
        
                // Creates a Note and inserts it into the collection in MongoDB.
                public void CreateNote(Note note)
                {
                    MongoCollection<Note> collection = getNotesCollectionForEdit();
                    try
                    {
                        collection.Insert(note);
                    }
                    catch (MongoCommandException ex)
                    {
                        string msg = ex.Message;
                    }
                }
        
                private MongoCollection<Note> GetNotesCollection()
                {
                    MongoClient client = new MongoClient(url);
                    mongoServer = client.GetServer();
                    MongoDatabase database = mongoServer.GetDatabase(dbName);
                    MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
                    return noteCollection;
                }
        
                private MongoCollection<Note> getNotesCollectionForEdit()
                {
                    MongoClient client = new MongoClient(url);
                    mongoServer = client.GetServer();
                    MongoDatabase database = mongoServer.GetDatabase(dbName);
                    MongoCollection<Note> notesCollection = database.GetCollection<Note>(collectionName);
                    return notesCollection;
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

1. Observe o código acima:
            
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        private string dbName = "myMongoApp";  
Aqui, você acessará uma variável de ambiente que configurará posteriormente. Se você tiver uma instância do mongo local em execução para fins de desenvolvimento, convém definir temporariamente esse valor para "localhost".
  
  Defina também o nome do banco de dados. Especificamente, defina o valor **dbName** para o nome que você inseriu ao provisionar o complemento MongoLab.

1. Finalmente, examine o código a seguir em **GetNotesCollection()**:  

        MongoClient client = new MongoClient(url);
        mongoServer = client.GetServer();
        MongoDatabase database = mongoServer.GetDatabase(dbName);
        MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
  Não há nada para alterar aqui> Lembre-se apenas de que é assim que você obtém um objeto MongoCollection para executar inserções, atualizações e consultas, como o seguinte em **GetAllNotes()**:

        collection.FindAll().ToList<Note>();

Para obter mais informações sobre como utilizar o driver C# do MongoDB, consulte [CSharp Driver QuickStart](http://www.mongodb.org/display/DOCS/CSharp+Driver+Quickstart "Início rápido do Driver CSharp") em mongodb.org.

### Adicionar uma exibição de criação
Agora, você adicionará um modo de exibição para criar uma nova nota.

1. Clique com o botão direito do mouse na entrada **Modos de Exibição > Início** no Gerenciador de Soluções e selecione **Adicionar > Modo de Exibição**. Chame esse novo modo de exibição **Criar** e clique em **Adicionar**.

1. Substitua o código gerado automaticamente para essa exibição (**Create.cshtml**) pelo seguinte:

        @model mongoNotes.Models.Note
        
        <script src="@Url.Content("~/Scripts/jquery-1.5.1.min.js")" type="text/javascript"></script>
        <script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
        <script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
        
        @using (Html.BeginForm("Create", "Home")) {
            @Html.ValidationSummary(true)
            <fieldset>
                <legend>New Note</legend>
                <h3>New Note</h3>       
                <div class="editor-label">
                    @Html.LabelFor(model => model.Text)
                </div>
                <div class="editor-field">
                    @Html.EditorFor(model => model.Text)
                </div>
               <p>
                    <input type="submit" value="Create" />
               </p>
           </fieldset>
        }

### Modificar index.cshtml
Em seguida, inclua um layout simples para exibir e criar notas no seu aplicativo Web.

1. Abra **Index.cshtml** em **Exibições > Início** e substitua seu conteúdo pelo seguinte:  

        @model IEnumerable<mongoNotes.Models.Note>
        
        @{
            ViewBag.Title = "Notes";
        }
        
        <h2>My Notes</h2>

        <table border="1">
            <tr>
                <th>Date</th>
                <th>Note Text</th>      
            </tr>
        
        @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Date)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Text)
                </td>       
            </tr>
        }
        
        </table>
        <div>  @Html.Partial("Create", new mongoNotes.Models.Note())</div>

### Modificar HomeController.cs
Finalmente, seu HomeController precisa criar uma instância de sua camada de acesso a dados e aplicá-la aos seus modos de exibição.

1. Abra **HomeController.cs** em **Controladores** no Gerenciador de Soluções e substitua seu conteúdo pelo seguinte:  

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using System.Web.Mvc;
        using mongoNotes.Models;
        using System.Configuration;
        
        namespace mongoNotes.Controllers
        {
            public class HomeController : Controller, IDisposable
            {
                private Dal dal = new Dal();
                private bool disposed = false;
                //
                // GET: /Task/
        
                public ActionResult Index()
                {
                    return View(dal.GetAllNotes());
                }
        
                //
                // GET: /Task/Create
        
                public ActionResult Create()
                {
                    return View();
                }
        
                //
                // POST: /Task/Create
        
                [HttpPost]
                public ActionResult Create(Note note)
                {
                    try
                    {
                        dal.CreateNote(note);
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
    
<a name="deploy"></a>
## Implantar o aplicativo

Agora que o aplicativo foi desenvolvido, é hora de criar um aplicativo Web no Serviço de Aplicativo do Azure para hospedá-lo, configurar esse aplicativo Web e implantar o código. O principal desta seção é o uso da cadeia de conexão do MongoDB (URI). Você vai configurar uma variável de ambiente no seu aplicativo Web com esse URI para manter o URI separado do seu código. Você deve tratar o URI como informação confidencial, pois ele contém credenciais para se conectar ao banco de dados.

### Criar um novo aplicativo Web e obter o arquivo Publicar Configurações
A criação de um aplicativo Web no Serviço de Aplicativo do Azure é muito fácil, especialmente porque o Azure gera automaticamente um perfil de publicação para o Visual Studio.

1. No portal do Azure, clique em **Novo**.  
	![Novo][button-new]

1. Selecione **Computação > Aplicativo Web > Criação Rápida**.  
	<!-- ![CreateWebApp][screen-mongolab-newwebsite] -->

1. Digite um prefixo de URL. Escolha um nome de sua preferência, mas lembre-se de que ele deve ser exclusivo ('mongoNotes' provavelmente não estará disponível).

1. Clique em **Criar aplicativo Web**.

1. Quando a criação do aplicativo Web for concluída, clique no nome do aplicativo Web na lista de aplicativos Web. O painel de Aplicativos Web é exibido.  
	![WebAppDashboard][screen-mongolab-websitedashboard]

1. Clique em **Baixar perfil de publicação** em **visão rápida** e salve o arquivo .PublishSettings em um diretório de sua escolha.  
![DownloadPublishProfile][button-website-downloadpublishprofile]

Como opção, você também pode configurar um aplicativo Web diretamente do Visual Studio. Ao vincular sua conta do Azure ao Visual Studio, siga os prompts para configurar um aplicativo Web dali. Quando tiver concluído, simplesmente clique com o botão direito no nome do projeto no Gerenciador de Soluções para implantar no Azure. Ainda será preciso configurar a cadeia de conexão do MongoLab, como detalhado nas etapas a seguir.

### Obter a cadeia de conexão do MongoLab

[AZURE.INCLUDE [howto-get-connectioninfo-mongolab](../../includes/howto-get-connectioninfo-mongolab.md)]

### Adicionar a cadeia de conexão às variáveis de ambiente do aplicativo Web

[AZURE.INCLUDE [howto-save-connectioninfo-mongolab](../../includes/howto-save-connectioninfo-mongolab.md)]

### Publicar o aplicativo Web
1. No Visual Studio, clique com o botão direito do mouse no projeto **mongoNotes** no Gerenciador de Soluções e selecione **Publicar**. A caixa de diálogo Publicar é exibida:  
	<!-- ![Publish][dialog-mongolab-vspublish] -->

1. Clique em **Importar** e selecione o arquivo .PublishSettings no diretório de download escolhido. Esse arquivo preencherá automaticamente os valores na caixa de diálogo Publicar.

1. Clique em **Validar Conexão** para testar o arquivo.

1. Quando a validação for bem-sucedida, clique em **Publicar**. Quando a publicação estiver concluída, uma nova guia do navegador será aberta e o aplicativo Web será exibido.

1. Insira um texto de nota, clique em **Criar** e veja os resultados!  
	![HelloMongoAzure][screen-mongolab-sampleapp]

<a name="manage"></a>
## Gerenciar o banco de dados

[AZURE.INCLUDE [howto-access-mongolab-ui](../../includes/howto-access-mongolab-ui.md)]

Parabéns! Você acabou de iniciar um aplicativo C# ASP.NET com suporte de um banco de dados MongoDB hospedado por MongoLab! Agora que o banco de dados MongoLab foi criado, você poderá entrar em contato com [support@mongolab.com](mailto:support@mongolab.com)com perguntas ou dúvidas sobre seu banco de dados ou para obter ajuda para o MongoDB ou para o próprio driver C#. Boa sorte!

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

[screen-mongolab-sampleapp]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-sampleapp.png
[dialog-mongolab-vspublish]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-vspublish.png
[button-website-downloadpublishprofile]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-website-downloadpublishprofile.png
[screen-mongolab-websitedashboard]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-newwebsite.png
[button-new]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-new.png
[dialog-mongolab-csharp-newproject]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-csharp-newproject.png
[dialog-mongolab-csharp-projecttemplate]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-csharp-projecttemplate.png
[focus-mongolab-csharp-pmconsole]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/focus-mongolab-csharp-pmconsole.png
[button-store]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-store.png
[entry-mongolab]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/entry-mongolab.png
[button-connectioninfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-connectioninfo.png
[screen-connectioninfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage


<!---HONumber=54--> 