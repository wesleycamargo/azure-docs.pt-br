<properties linkid="develop-net-tutorials-website-with-mongodb-mongolab" urlDisplayName="Website with MongoDB on MongoLab" pageTitle="Create a Website that uses MongoDB on MongoLab (.NET)" metaKeywords="" description="Learn how to create an Azure website that stores data in MongoDB hosted by MongoLab." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create a C# ASP.NET Application on Azure with MongoDB using the MongoLab Add-On" authors="eric@mongolab.com" solutions="" manager="" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="eric@mongolab.com" />

# Criar um aplicativo C# ASP.NET no Azure com MongoDB usando o complemento MongoLab

*Por Eric Sedor, MongoLab*

Saudações, aventureiros! Bem-vindos ao MongoDB como serviço. Neste tutorial, você irá:

1.  [Provisionar o Banco de dados][Provisionar o Banco de dados] O complemento [MongoLab][MongoLab] da Azure Store fornecerá um banco de dados MongoDB hospedado na nuvem do Azure e gerenciado pela plataforma de banco de dados em nuvem do MongoLab.
2.  [Criar o aplicativo][Criar o aplicativo] -será um simples aplicativo C# ASP.NET MVC para fazer anotações.
3.  [Implantar o aplicativo][Implantar o aplicativo] - com a reunião de alguns ganchos de configuração, a implantação do nosso código será moleza.
4.  [Gerenciar o banco de dados][Gerenciar o banco de dados] - finalmente, mostraremos o portal de gerenciamento de banco de dados baseado na web do MongoLab, onde você poderá pesquisar, visualizar e modificar dados facilmente.

A qualquer momento durante este tutorial, fique à vontade para mandar um e-mail para [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>][\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>] em caso de dúvidas.

## Início rápido

Se você já tiver um aplicativo do Azure e o site com o qual deseja trabalhar ou se estiver um pouco familiarizado com a Azure Store, use esta seção como um início rápido. Caso contrário, vá para [Provisionar o banco de dados][Provisionar o Banco de dados] abaixo.

1.  Abra a Azure Store.
    ![Store][Store]
2.  Adquira o complemento MongoLab.
    ![MongoLab][1]
3.  Clique no complemento MongoLab na lista Complementos e, em seguida, clique em **Informações de Conexão**.
    ![ConnectionInfoButton][ConnectionInfoButton]
4.  Copie o MONGOLAB\_URI para a sua área de transferência.
    ![ConnectionInfoScreen][ConnectionInfoScreen]
    **Esse URI contém seu nome de usuário do banco de dados e senha. Tratá-lo como informações confidenciais e não a compartilhe.**
5.  Adicione o valor à lista Cadeias de Conexão no menu Configuração do seu aplicativo Web do Azure:
    ![WebSiteConnectionStrings][WebSiteConnectionStrings]
6.  Para **nome**, digite MONGOLAB\_URI.
7.  Para **valor**, cole a cadeia de conexão que obtivemos na seção anterior.
8.  Selecione **Personalizado** na caixa suspensa Tipo (em vez do padrão **SQLAzure**).
9.  No Visual Studio, instale o Driver C# do Mongo, selecionando **Ferramentas \> Gerenciador de Pacotes da Biblioteca \> Console do Gerenciador de Pacotes**. No Console do PM, digite **Install-Package mongocsharpdriver** e pressione **Enter**.
10. Configure um gancho no código para obter o URI de conexão do MongoLab de uma variável de ambiente:

        using MongoDB.Driver;  
        ...
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        ...
        MongoUrl url = new MongoUrl(connectionString);
        MongoClient client = new MongoClient(url);

    Observação: O Azure adiciona o prefixo **CUSTOMCONNSTR\_** à cadeia de conexão declarada originalmente e é por isso que o código faz referência a **CUSTOMCONNSTR\_MONGOLAB\_URI.** em vez de a **MONGOLAB\_URI**.

Agora, vamos para o tutorial completo...

## <a name="provision"></a>Provisionar o banco de dados

[WACOM.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

## <a name="create"></a>Crie o aplicativo

Nesta seção, você entenderá a criação de um projeto C# ASP.NET do Visual Studio e examinará o uso do driver C# do MongoDB para criar um aplicativo simples de Nota. Você deseja poder visitar seu site, escrever uma nota e exibir todas as notas que restaram.

Você executará esse desenvolvimento no Visual Studio Express 2012 para Web.

### Criar o projeto

O aplicativo de amostra usará um modelo do Visual Studio para começar. Certifique-se de usar o .NET Framework 4.0.

1.  Selecione **Arquivo \> Novo Projeto**. A caixa de diálogo Novo Projeto exibe:
    ![NewProject][NewProject]
2.  Selecione **Instalado \> Modelos \> Visual C# \> Web**.
3.  Selecione **.NET Framework 4** no menu suspenso de versão do .NET (*observação: O Framework 4.5 não funcionará neste momento*).

    ![ProjectFramework][ProjectFramework]

4.  Escolha **Aplicativo Web ASP.NET MVC 4**
    .
5.  Digite *mongoNotes* como o **Nome do Projeto**. Se escolher um nome diferente, você precisará modificar o código fornecido em todo o tutorial.
6.  Clique em **OK**. A caixa de diálogo Modelo de Projeto é exibida:
    ![ProjectTemplate][ProjectTemplate]
7.  Selecione **Aplicativo da Internet** e clique em **OK**. O projeto é compilado.
8.  Selecione **Ferramentas \> Gerenciador de Pacotes da Biblioteca \> Console do Gerenciador de Pacotes**. No Console do PM, digite **Install-Package mongocsharpdriver** e pressione **Enter**.
    ![PMConsole][PMConsole]
     O Driver C# do MongoDB é integrado ao projeto, e a linha a seguir é adicionada automaticamente ao arquivo *packages.config*:

        < package id="mongocsharpdriver" version="1.8" targetFramework="net40" / >

### Adicionar um modelo de nota

Primeiro, estabeleça um modelo para Notas, com apenas uma data e um conteúdo de texto.

1.  Clique com o botão direito do mouse em **Modelos** no Gerenciador de Soluções e selecione **Adicionar \> Classe**. Chame essa nova classe *Note.cs*.
2.  Substitua o código gerado automaticamente para essa classe pelo seguinte:

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

1.  Clique com o botão direito do mouse no projeto **mongoNotes** no Gerenciador de Soluções e selecione **Adicionar \> Nova Pasta**. Chame a pasta **DAL**.
2.  Clique com o botão direito do mouse em **DAL** no Gerenciador de Soluções e selecione **Adicionar \> Classe**. Chame essa nova classe *Dal.cs*.
3.  Substitua o código gerado automaticamente para essa classe pelo seguinte:

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
                MongoUrl url = new MongoUrl(connectionString);

                private string dbName = "myMongoApp";
                private string collectionName = "Notes";

                // Default constructor.        
                public Dal()
                {
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

4.  Observe o código acima:

        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        private string dbName = "myMongoApp";  

    Aqui, você acessará uma variável de ambiente que configurará posteriormente. Se você tiver uma instância do mongo local em execução para fins de desenvolvimento, convém definir temporariamente esse valor para "localhost".

Defina também o nome do banco de dados. Especificamente, defina o valor **dbName** para o nome que você inseriu ao provisionar o complemento MongoLab.

1.  Finalmente, examine o código a seguir em **GetNotesCollection()**:

        MongoClient client = new MongoClient(url);
        mongoServer = client.GetServer();
        MongoDatabase database = mongoServer.GetDatabase(dbName);
        MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);

    Não há nada para alterar aqui\> Lembre-se apenas de que é assim que você obtém um objeto MongoCollection para executar inserções, atualizações e consultas, como o seguinte em **GetAllNotes()**:

        collection.FindAll().ToList<Note>();

Para obter mais informações sobre como aproveitar o driver C# do MongoDB, verifique o [CSharp Driver QuickStart][CSharp Driver QuickStart] em mongodb.org.

### Adicionar uma exibição de criação

Agora, você adicionará um modo de exibição para criar uma nova nota.

1.  Clique com o botão direito do mouse na entrada **Modos de Exibição \> Início** no Gerenciador de Soluções e selecione **Adicionar \> Modo de Exibição**. Chame esse novo modo de exibição **Criar** e clique em **Adicionar**.
2.  Substitua o código gerado automaticamente para essa exibição (**Create.cshtml**) pelo seguinte:

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

Em seguida, inclua um layout simples para exibir e criar notas no seu site.

1.  Abra **Index.cshtml** em **Exibições \> Início** e substitua seu conteúdo pelo seguinte:

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

1.  Abra **HomeController.cs** em **Controladores** no Gerenciador de Soluções e substitua seu conteúdo pelo seguinte:

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

## <a name="deploy"></a>Implantar o aplicativo

Agora que o aplicativo foi desenvolvido, é hora de criar um Site do Azure para hospedá-lo, configurar esse site e implantar o código. O principal desta seção é o uso da cadeia de conexão do MongoDB (URI). Você vai configurar uma variável de ambiente no seu site com esse URI para manter o URI separado do seu código. Você deve tratar o URI como informação confidencial, pois ele contém credenciais para se conectar ao banco de dados.

### Crie um novo site e obtenha o arquivo de configurações de publicação

A criação de um site no Azure é muito fácil, especialmente porque o Azure gera automaticamente um perfil de publicação para o Visual Studio.

1.  No portal do Azure, clique em **Novo**.
    ![Novo][Novo]
2.  Selecione **Computação \> Site \> Criação Rápida**.
    ![CreateSite][CreateSite]
3.  Digite um prefixo de URL. Escolha um nome de sua preferência, mas lembre-se de que ele deve ser exclusivo ('mongoNotes' provavelmente não estará disponível).
4.  Clique em **Criar Site**.
5.  Quando a criação do site for concluída, clique no nome do site na lista de sites. O painel do site é exibido.
    ![WebSiteDashboard][WebSiteDashboard]
6.  Clique em **Baixar perfil de publicação** em **visão rápida** e salve o arquivo .PublishSettings em um diretório de sua escolha.
    ![DownloadPublishProfile][DownloadPublishProfile]

### Obter a cadeia de conexão do MongoLab

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

### Adicionar a cadeia de conexão às variáveis de ambiente do site

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

### Publicar o site

1.  No Visual Studio, clique com o botão direito do mouse no projeto **mongoNotes** no Gerenciador de Soluções e selecione **Publicar**. A caixa de diálogo Publicar exibe:
    ![Publicar][Publicar]
2.  Clique em **Importar** e selecione o arquivo .PublishSettings no diretório de download escolhido. Esse arquivo preencherá automaticamente os valores na caixa de diálogo Publicar.
3.  Clique em **Validar Conexão** para testar o arquivo.
4.  Quando a validação for bem-sucedida, clique em **Publicar**. Quando a publicação estiver concluída, uma nova guia do navegador será aberta e o site será exibido.
5.  Insira um texto de nota, clique em **Criar** e veja os resultados!
    ![HelloMongoAzure][HelloMongoAzure]

## <a name="manage"></a>Gerenciar o banco de dados

[WACOM.INCLUDE [howto-access-mongolab-ui](../includes/howto-access-mongolab-ui.md)]

Parabéns! Você acabou de iniciar um aplicativo C# ASP.NET com suporte de um banco de dados MongoDB hospedado por MongoLab! Agora que o banco de dados MongoLab foi criado, você poderá entrar em contato com [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>][\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>]com perguntas ou dúvidas sobre seu banco de dados ou para obter ajuda para o MongoDB ou para o próprio driver C#. Boa sorte!

  [Provisionar o Banco de dados]: #provision
  [MongoLab]: http://mongolab.com
  [Criar o aplicativo]: #create
  [Implantar o aplicativo]: #deploy
  [Gerenciar o banco de dados]: #manage
  [Store]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-store.png
  [1]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/entry-mongolab.png
  [ConnectionInfoButton]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-connectioninfo.png
  [ConnectionInfoScreen]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab_connectioninfo.png
  [WebSiteConnectionStrings]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-mongolab-websiteconnectionstring.png
  [NewProject]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-csharp-newproject.png
  [ProjectFramework]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-dotNet-Framework4-mongolab.png
  [ProjectTemplate]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-csharp-projecttemplate.png
  [PMConsole]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-mongolab-csharp-pmconsole.png
  [CSharp Driver QuickStart]: http://www.mongodb.org/display/DOCS/CSharp+Driver+Quickstart "Início Rápido do Driver CSharp"
  [Novo]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-new.png
  [CreateSite]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-newwebsite.png
  [WebSiteDashboard]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-websitedashboard.png
  [DownloadPublishProfile]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-website-downloadpublishprofile.png
  [Publicar]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-vspublish.png
  [HelloMongoAzure]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-sampleapp.png
