<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="Compilar um aplicativo Web com ASP.NET MVC usando o Banco de Dados de Documentos | Azure" description="Learn how to use DocumentDB to build a To Do List web application. You'll store and access data from an ASP.NET MVC web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="ryancraw" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />


<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="ryancraw" />

<a name="_Toc395809351">Build a web application with ASP.NET MVC using DocumentDB</a>
=======================================================================================================
<a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Overview</a>
==============================================================================================

<a name="_Toc395637759">Scenario</a>
------------------------------------

To highlight how customers can efficiently leverage Azure DocumentDB to store and query JSON documents, this document provides an end-to-end walk-through of building a ToDo List web application using Azure Document DB.

This walk-through shows you how to use DocumentDB service provided by Azure to store and access data from an ASP.NET MVC web application hosted on Azure and presumes that you have some prior experience using ASP.NET MVC and Azure Websites.

You will learn:

1\. Creating and provisioning a DocumentDB Account

2\. Creating a ASP.NET MVC Application

3\. Connecting to and using Azure DocumentDB from your web application

4\. Deploying the Web Application to Azure Websites

By following this walk-through, you will build a simple web-based task-management application that allows creating, retrieving and completing of tasks. The tasks will be stored as JSON documents in Azure DocumentDB.

![Alt text](./media/documentdb-dotnet-application/image1.png)


<a name="_Toc395637760">Prerequisites</a>
================================================================

Before following the instructions in this article, you should ensure that you have the following installed:

Git for Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (or [Visual Studio Express][] which is the free version)

Azure SDK for .NET version 2.3 or higher, available through [Microsoft Web Platform Installer][]

All the screen shots in this document have been taken using Visual Studio 2013 with Update 3 applied and Azure SDK for .NET version 2.4. If your system is configured with different versions it is possible that your screens and options won't match entirely, but if you meet the above prerequisites this solution should work.

<a name="_Toc395637761">Create a DocumentDB database account</a>
================================================================

To provision a DocumentDB database account in Azure, open the Azure Management Portal and either Click the Azure Gallery tile on the homepage or click "+" in the lower left hand corner of the screen.

![Alt text](./media/documentdb-dotnet-application/image2.png)


This will open the Azure Gallery, where you can select from the many available Azure services. In the Gallery, select "Data, storage and backup" from the list of categories.

![Alt text](./media/documentdb-dotnet-application/image3.png)

From here, select the option for Azure DocumentDB

![Alt text](./media/documentdb-dotnet-application/image4.png)


Then select "Create" from the bottom of the screen

![Alt text](./media/documentdb-dotnet-application/image5.png)

This will open up the "New DocumentDB" blade where you can specify the name, region, scale, resource group and other settings for your new account.

![Alt text](./media/documentdb-dotnet-application/image6.png)


Once you're done supplying the values for your account, click "Create" and the provisioning process will begin creating your database account.
When the provisioning process is complete, you should see a notification appear in the notifications area of the portal and the tile on your start screen (if you selected to create one) will change to show the completed action.

![Alt text](./media/documentdb-dotnet-application/image7.png)


Once provisioning is complete, clicking the DocumentDB tile from the start screen will bring up the main blade for this newly created DocumentDB account.

![Alt text](./media/documentdb-dotnet-application/image8.png) 
![Alt text](./media/documentdb-dotnet-application/image9.png)


Using the "Keys" button, access your endpoint URL and the Primary Key, copy these to your clipboard and keep them handy as we will use these values in the web application we will use these values in the web application we will create next.

We will now walk through how to create a new ASP.NET MVC application from the ground-up. For your reference the complete solution can be downloaded [here].

<a name="_Toc395637762">Create a new ASP.NET MVC application</a>
================================================================

In Visual Studio, click File - New Project and select the option to create a new ASP.NET MVC Web Application.

![Alt text](./media/documentdb-dotnet-application/image10.png)


Select where you would like to create the project, what you want the project to be called (I'm using "todo") and click Ok.

![Alt text](./media/documentdb-dotnet-application/image11.png)


If you plan on hosting your application in Azure then select the box on the lower right to "Host in the cloud". We've selected to host in the cloud, and run the application hosted in an Azure Website. Selecting this option will pre-provision an Azure Website for you and make life a lot easier when it comes times to deploy the final working application. If you want to host this elsewhere or don't want to configure Azure upfront, then just un-check the "Host in the Cloud" option.

Select OK and let Visual Studio do its thing around scaffolding the empty ASP.NET MVC template. If you chose to host this in the Cloud you will see at least one additional screen asking you to login to your Azure account and provide some values for your new Website. Proceed to supply all these Azure values and continue. 

I haven't chosen a "Database server" here because we're not using an Azure SQL Database Server here, we're going to be creating a new Azure DocumentDB Account later on in the management portal. 

![Alt text](./media/documentdb-dotnet-application/image11_1.png)

Once Visual Studio has finished creating the boilerplate MVC application you have an empty ASP.NET application that you can run locally.

We'll skip running locally because I'm sure we've all seen the ASP.NET "Hello World" application. Let's go straight to adding DocumentDB to this project and building our application.

</h1>
<a name="_Toc395637767">Adding DocumentDB to your project</a>
=============================================================

That takes care of most of the ASP.NET MVC plumbing that we need for
this solution. Now let's get to the real purpose of this tutorial, adding Azure DocumentDB to our web application.

### 

### <a name="_Toc395637764">Installing the NuGet package</a>

The DocumentDB .NET SDK is packaged and distributed as a NuGet package. Using the NuGet package manager in Visual Studio (which you can get to by Right-Clicking on the Project and choosing "Manage NuGet Packages"

![Alt text](./media/documentdb-dotnet-application/image21.png)

Search for Online for "Azure DocumentDB" and install the package. This will download and install the DocumentDB package as well as all dependencies, like Newtonsoft.Json.

**NOTE:** While the service is still in preview, the NuGet package is marked as "Prerelease" so you need to include the option to "Include Prerelease", else you will not find the package. 

Alternatively you could use the Package Command Console to install the package by typing;

    Install-Package Microsoft.Azure.Documents.Client -Pre

Once installed your Visual Studio solution should resemble the following with two new references added;

![Alt text](./media/documentdb-dotnet-application/image22.png)


<a name="_Toc395637763">Setting up the ASP.NET MVC application</a>
==================================================================

### 

### <a name="_Toc395637764">Add a Model</a>

Let's begin by creating the **M** in MVC, the model. In Solution Explorer, right-click the *Models* folder and then click **Add**, then **Class**

![Alt text](./media/documentdb-dotnet-application/image12.png)

Name your new Class, **Item** and then add the following code in to this new Class

    public class Item
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
		
        [JsonProperty(PropertyName="name")]
        public string Name { get; set; }
		
        [JsonProperty(PropertyName = "desc")]
        public string Description { get; set; }
		
        [JsonProperty(PropertyName="isComplete")]
        public bool Completed { get; set; }    
    }

All data in DocumentDB is passed over the wire, and stored, as JSON. To control the way your objects are serialized/deserialized by JSON.NET you can use the JsonProperty attribute as demonstrated in the Item class we just created. You don't **have** to do this but i wanted to ensure that my properties followed the JSON camelCase naming conventions. 

Not only can you control the format of the property name when it goes in to JSON, but you can entirely rename your .NET properties like I did with the Description property. 

You can, if you like, use JsonConverter objects here as well to completely control how serialization is handled.  

In order to get Visual Studio to resolve the "JsonProperty" attribute used here you need to add the following using statement to the using section of your class file;

    using Newtonsoft.Json;

### <a name="_Toc395637765">Add a Controller</a>

That takes care of the M, now let's create the **C** in MVC, a controller class.
In **Solution Explorer**, right-click the *Controllers* folder and then click **Add**, then **Controller**.

![Alt text](./media/documentdb-dotnet-application/image13.png)

![Alt text](./media/documentdb-dotnet-application/image14.png)


In the **Add Scaffold** dialog box, click **MVC 5 Controller - Empty.** Click **Add.**

![Alt text](./media/documentdb-dotnet-application/image15.png)

Name your new Controller, **ItemController.**

Visual Studio will now add the ItemController your Solution Explorer should look like similar to below.

![Alt text](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766">Add Views</a>

And finally, let's create the **V** in MVC, a view.


#### Add Item Index View

Expand the ***Views***  folder in Solution Explorer and location the (empty) Item folder which Visual Studio would've created for you when you added the *ItemController* earlier. Right click on ***Item*** and choose to Add a new View.

![Alt text](./media/documentdb-dotnet-application/image17.png)

In the "Add View" dialog. Call your view "***Index***", use the ***List*** Template, select the ***Item (todo.Models)*** which we created earlier as the class and finally use the ***~Views/Shared/_Layout.cshtml*** in the Solution as the Layout page.

![Alt text](./media/documentdb-dotnet-application/image18.png)


Once all these values are set, click Add and let Visual Studio create your view for you. Visual Studio will create a template view. Once it is done, it will open the cshtml file created. We can close this document in Visual Studio as we will come back to it later.

#### Add New Item View

In a similar fashion to above, create a new View for creating new Items as per the example shown below;

![Alt text](./media/documentdb-dotnet-application/image19.png)

#### Add Edit Item View

<a name="_Toc395888515"></a>
============================

And finally, add one last View for editing an Item in the same way as before;

![Alt text](./media/documentdb-dotnet-application/image20.png)


Once this is done, close the cshtml documents in Visual Studio as we will return to these Views later.

</h1>
<a name="_Toc395637769">Wiring up DocumentDB</a>
------------------------------------------------

### <a name="_Toc395637770">Listing Incomplete Items</a>

Open the **ItemController** and remove all the code within the class (but leave the class) that Visual Studio added. We'll rebuild it piece by piece using DocumentDB.

Add the following code snippet within the now empty ItemController class;

    public ActionResult Index()
    {
        var items = DocumentDBRepository.GetIncompleteItems();
        return View(items);
    }

This code also uses a "pseudo repository" class called DocumentDBRepository which we have yet to create. This is actually just a Helper class that contains all the DocumentDB specific code. For the purposes of this walk-through we aren't going to implement a full data access layer with dependency injection, and factories and repository patterns, as you would probably be doing if you were building a real world application. 
For the purposes of this walk-through we're just going to put all the data access logic directly in to one project to keep things simple and focus on the DocumentDB specific bits.

Add a new Class to your project and call it **DocumentDBRepository.**
Replace the code in the class file with the following;

    public static class DocumentDBRepository
    {
        private static string databaseId;
        private static String DatabaseId
        {
			get
			{
				if (string.IsNullOrEmpty(databaseId))
				{
					databaseId = ConfigurationManager.AppSettings["database"];
				}
			
				return databaseId;
			}
        }
			  
        private static string collectionId;
        private static String CollectionId
        {
			get
			{
				if (string.IsNullOrEmpty(collectionId))
				{
					collectionId = ConfigurationManager.AppSettings["collection"];
				}
			
				return collectionId;
			}
        }
				
        private static Database database;
        private static Database Database
        {
			get
			{
				if (database == null)
				{
					database = ReadOrCreateDatabase();
				}
				
				return database;
			}
        }
		
        private static DocumentCollection collection;
        private static DocumentCollection Collection
        {
			get
			{
				if (collection == null)
				{
					collection = ReadOrCreateCollection(Database.SelfLink);
				}
				
				return collection;
			}
        }
		
        private static DocumentClient client;
        private static DocumentClient Client
        {
            get
            {
                if (client == null)
                {
					string endpoint = ConfigurationManager.AppSettings["endpoint"];
					string authKey = ConfigurationManager.AppSettings["authKey"];
					Uri endpointUri = new Uri(endpoint);
					client = new DocumentClient(endpointUri, authKey);
                }
                
                return client;
            }
        }
    } 

#### 

Spend some time resolving all the namespaces in Visual Studio. All namespaces will be easy to resolve in Visual Studio as long as the NuGet package was installed successfully. The references to the ReadOrCreateDatabase and ReadOrCreateCollection methods will remain unresolved until we have added them, which comes next. 

There are two method calls used here for reading or creating DocumentDB Databases and Document Collections.
So add the following two methods to this class;

    private static DocumentCollection ReadOrCreateCollection(string databaseLink)
    {
        var col = Client.CreateDocumentCollectionQuery(databaseLink)
                          .Where(c => c.Id == CollectionId)
                          .AsEnumerable()
                          .FirstOrDefault();

        if (col == null)
        {
            col = Client.CreateDocumentCollectionAsync(databaseLink, new DocumentCollection { Id = CollectionId }).Result;
        }
		
        return col;
    }

    private static Database ReadOrCreateDatabase()
    {
        var db = Client.CreateDatabaseQuery()
                        .Where(d => d.Id == DatabaseId)
                        .AsEnumerable()
                        .FirstOrDefault();
		
        if (db == null)
        {
            db = Client.CreateDatabaseAsync(new Database { Id = DatabaseId }).Result;
        }
		
        return db;
    }

</h1>

#### 

This takes care setting up the Database, a DocumentCollection, and creating some code to connect to DocumentDB through the DocumentClient. 

We're reading some values from configuration, so open the **web.config** and add the following lines under the
<AppSettings\> section;

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>


</h4>

Agora vamos adicionar alguns códigos para fazer o trabalho. 
A primeira coisa que desejamos fazer com um aplicativo de lista de tarefas é exibir os itens incompletos. O método a seguir faz isso para você, então copie e cole-o em qualquer lugar dentro da classe de repositório com a qual estamos trabalhando;
    
    public static List<Item> GetIncompleteItems()
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => !d.Completed)
                .AsEnumerable()
                .ToList<Item>();
    }
    
A referência para *CreateDocumentQuery* será resolvida depois; você precisa adicionar manualmente o seguinte usando a diretiva;

    using Microsoft.Azure.Documents.Linq;

Neste ponto, sua solução deve ser capaz de compilar sem erros.

Se você executou o aplicativo agora, deve ir para o Controlador Inicial e a exibição Índice desse controlador. Esse é o comportamento padrão para o projeto do modelo MVC que escolhemos no início, mas não queremos isso! Vamos alterar o roteamento neste aplicativo MVC para alterar seu comportamento.

Abra ***App\_Start\RouteConfig.cs*** localize a linha começando com "defaults:" e altere-a para se parecer com o seguinte;

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Agora isso informa ao ASP.NET MVC que se você não especificou um valor na URL para controlar o comportamento de roteamento que, em vez de "Home", usa "Item" como controlador e o usuário Índice como exibição.
Agora, se você executar o aplicativo, ele será chamado para seu **ItemController** e retornará os resultados do método **GetIncompleteItems** para a exibição Views\Item\Index. 

Se você compilar e executar esse projeto agora, deverá ver algo parecido com isto;    

![Alt text](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771">Adicionando itens</a>

Vamos colocar alguns itens em nosso banco de dados; assim, temos algo mais que uma grade vazia para observar.

Já temos uma Exibição para Criar, e um Botão na Exibição de Índice que levará o usuário para a exibição de criação. Vamos adicionar algum código ao Controlador e ao Repositório para persistir no registro no Banco de Dados de Documentos.

Abra o ***ItemController.cs*** e adicione o seguinte trecho de código que é como o ASP.NET MVC sabe o que fazer para a ação Criar, neste caso, basta renderizar a exibição Create.cshtml associada criada anteriormente.

    public ActionResult Create()
    { 
	return View(); 
    }

Agora precisamos de mais algum código nesse controlador que aceitará o envio por meio da exibição de criação.

Adicione o próximo bloco de código que diz ao ASP.NET MVC o que fazer com um formulário POST para esse controlador.
	
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Create([Bind(Include = "Id,Name,Description,Completed")] Item item)  
    {
		if (ModelState.IsValid)  
		{  
		    await repo.CreateDocument(item);  
		    return RedirectToAction("Index");  
		}   
    	return View(item);   
    }

**Observação de segurança**: O atributo [ValidateAntiForgeryToken] é usado aqui para ajudar a proteger este aplicativo contra ataques de solicitação intersite forjada. Há mais do que apenas adicionar esse atributo, as exibições precisam trabalhar com esse token antifalsificação também. Para saber mais sobre o assunto e ver exemplos de como implementar isso corretamente, consulte [Prevenindo solicitação intersite forjada][]. O código-fonte no link de download no final do artigo possui a implementação completa em vigor.

**Observação de segurança**: Também usamos o atributo [Bind] no parâmetro de método para ajudar a proteger contra ataques de overposting. Para obter mais detalhes, consulte [Operações CRUD básicas no ASP.NET MVC][]

Agora que isso está em vigor, o controlador de itens passará  (com segurança) o Item, do formulário, para o método CreateDocument do repositório da classe, então, adicione o seguinte método à classe DocumentDBRepository.

    public static async Task<Document> CreateItemAsync(Item item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item);
    }

Este método simplesmente pega algum objeto passado para ele e persiste-o no Banco de Dados de Documentos.
Isso conclui o código exigido para adicionar novos itens ao nosso banco de dados.


### <a name="_Toc395637772">Editando itens</a>

Existe uma última ação para realizarmos, e esta é a capacidade de editar itens no banco de dados e marcá-los como concluídos à medida que concluímos as tarefas. Assim como na adição, a exibição para edição já foi adicionada, desse modo, basta adicionar algum código adicional ao nosso Controlador e à classe DocumentDBRepository novamente.

Adicione o seguinte à classe ItemController;

    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Edit([Bind(Include = "Id,Name,Description,Completed")] Item item)
    {
        if (ModelState.IsValid)
        {
            await DocumentDBRepository.UpdateItemAsync(item);
            return RedirectToAction("Index");
        }

        return View(item);
    }
	
    public ActionResult Edit(string id)
    {
        if (id == null)
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
        }

        Item item = (Item)DocumentDBRepository.GetItem(id);
        if (item == null)
        {
            return HttpNotFound();
        }

        return View(item);
    }

O primeiro método lida com o Http Get que ocorrerá quando o usuário clicar no link "Editar" na exibição Índice. Esse método busca um Documento do Banco de Dados de Documentos e o transfere para a exibição Editar.

Em seguida, a exibição Editar fará um Http Post para o IndexController. 
O segundo método que adicionamos lida com isso transferindo objeto atualizado por meio do Banco de Dados de Documentos para que seja persistido no banco de dados.

Adicione o seguinte à classe DocumentDBRepository;

    public static Item GetItem(string id)
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => d.Id == id)
                    .AsEnumerable()
                    .FirstOrDefault();
    }
	
    public static async Task<Document> UpdateItemAsync(Item item)
    {
        Document doc = Client.CreateDocumentQuery(Collection.DocumentsLink)
                            .Where(d => d.Id == item.Id)
                            .AsEnumerable()
                            .FirstOrDefault();

        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

O primeiro desses dois métodos busca um Item do Banco de Dados de Documentos que é transferido de volta para o ItemController e, em seguida, para a exibição Editar.

O segundo dos dois métodos que acabamos de adicionar substitui o documento no Banco de Dados de Documentos pela versão do documento transferido do ItemController.

Ou seja, isso é tudo que precisamos para executar nosso aplicativo, Listar itens incompletos, Adicionar novos itens e, por fim, Editar itens.

</h3>
<a name="_Toc395637773">Execute seu aplicativo localmente</a>
=========================================================

### 

Para testar o aplicativo no seu computador local, pressione F5 no Visual Studio e ele deve compilar o aplicativo e iniciar um navegador com a página de grade vazia que vimos anteriormente:

![Alt text](./media/documentdb-dotnet-application/image24.png)

1\.Use os campos fornecidos para Item, Nome do Item e Categoria para inserir informações, clique no link **"Criar Novo"** e forneça alguns valores. Deixe a caixa de seleção "Concluído" desmarcada, caso contrário, o novo item será adicionado em um estado concluído e não aparecerá na lista inicial.

![Alt text](./media/documentdb-dotnet-application/image25.png)

Se clicar em Criar, você será redirecionado de volta para a página Índice e espera-se que seu Item seja mostrado na Lista.

![Alt text](./media/documentdb-dotnet-application/image26.png)

Fique à vontade para adicionar mais alguns itens à sua lista de Tarefas Pendentes.

2\.Clique em "Editar" perto de um Item na lista e você será levado para a exibição Editar, onde poderá atualizar qualquer propriedade do objeto, incluindo a marcação "Concluído. Isso marca efetivamente o item como concluído e o removerá da Lista de tarefas incompletas.

![Alt text](./media/documentdb-dotnet-application/image27.png)

3\.Para concluir uma tarefa, basta marcar a caixa de seleção e clicar em **Salvar**. Você será redirecionado de volta para a página de lista onde agora o item não aparecerá mais na lista.

</h3>
<a name="_Toc395637774">Implante o aplicativo nos sites do Azure</a>
================================================================

### 

Agora que você possui o aplicativo completo funcionando corretamente no Banco de Dados de Documentos, vamos implantá-lo nos sites do Azure.

Se você selecionou "Hospedar na nuvem" quando criamos o projeto ASP.NET MVC vazio, o Visual Studio tornará essa ação muito fácil e fará a maior parte do trabalho para nós. Para Publicar este aplicativo, basta clicar com o botão direito do mouse no Projeto no Gerenciador de Soluções (certifique-se de que você ainda não o esteja executando localmente) e selecionar Publicar

![Alt text](./media/documentdb-dotnet-application/image28.png)

Tudo já deve estar configurado de acordo com suas credenciais; na verdade, o site já foi criado no Azure para você na "URL de Destino" mostrada. Basta clicar em **Publicar**

![Alt text](./media/documentdb-dotnet-application/image29.png)

Em poucos segundos, o Visual Studio terminará de publicar seu aplicativo Web e iniciará um navegador onde você poderá ver seu trabalho sendo executado no Azure!

</h3>



<a name="_Toc395637775">Conclusão</a>
======================================

### 

Parabéns! Você acabou de criar seu primeiro Aplicativo ASP.NET MVC usando o Banco de Dados de Documentos do Azure e publicá-lo nos sites do Azure. O código-fonte para o aplicativo completo, incluindo a funcionalidade Detalhes e exclusão ignorada aqui, pode ser baixado [aqui][].


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[aqui]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[Prevenindo solicitação intersite forjada]: http://go.microsoft.com/fwlink/?LinkID=517254
[Operações CRUD básicas no ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598
