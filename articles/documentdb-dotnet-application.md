<properties 
	pageTitle="Compilar um aplicativo Web com ASP.NET MVC usando o Banco de Dados de Documentos | Azure" 
	description="Saiba como usar o Banco de Dados de Documentos com .NET para compilar um aplicativo Web de lista de tarefas pendentes. Você vai armazenar e acessar dados de um aplicativo Web ASP.NET MVC hospedado em Sites do Azure." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="03/23/2015" 
	ms.author="ryancraw"/>

#<a name="_Toc395809351"></a>Compilar um aplicativo Web com ASP.NET MVC usando o Banco de Dados de Documentos

Para destacar como você pode aproveitar com eficiência o Banco de Dados de Documentos do Azure para armazenar e consultar documentos JSON, este artigo fornece um passo a passo completo sobre como compilar um aplicativo Web de lista de tarefas pendentes usando o Banco de Dados de Documentos do Azure. As tarefas serão armazenadas como documentos JSON no Banco de Dados de Documentos do Azure.

![Screen shot of the todo list web application created by this tutorial](./media/documentdb-dotnet-application/image1.png)

Este passo a passo mostra como usar o serviço do Banco de Dados de Documentos fornecido pelo Azure para armazenar e acessar dados em um aplicativo Web ASP.NET MVC hospedado no Azure.

> [AZURE.TIP] Este tutorial presume que você tenha experiência anterior no uso do ASP.NET MVC e dos Sites do Azure. Se você for iniciante no ASP.NET ou nas [ferramentas de pré-requisito](#_Toc395637760), recomendamos baixar o projeto do tutorial [todo](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/todo) completo do [GitHub](https://github.com/Azure/azure-documentdb-net) e compilá-lo usando as [instruções no fim deste artigo](#GetProject). Depois que você o tiver criado, poderá ler o artigo para obter informações sobre o código no contexto do projeto.

## <a name="_Toc395637760"></a>Pré-requisitos

Antes de seguir as instruções deste artigo, verifique se você possui o seguinte:

- Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure](../../pricing/free-trial/).
- [Visual Studio 2013](http://www.visualstudio.com/) ou superior, ou [Visual Studio Express], que é a versão gratuita.
- SDK do Azure para .NET versão 2.3 ou superior, disponível no [Microsoft Web Platform Installer][].

Todas as capturas de tela neste artigo foram feitas usando o Visual Studio 2013 com Atualização 3 aplicada e o SDK do Azure para .NET versão 2.4. Se o seu sistema estiver configurado com versões diferentes, será possível que suas telas e opções não correspondam totalmente, mas se você cumprir os pré-requisitos acima, esta solução deverá funcionar.

## <a name="_Toc395637761"></a>Etapa 1: Criar uma conta de banco de dados do Banco de Dados de Documentos

Vamos começar criando uma conta do Banco de Dados de Documentos. Se você já tiver uma conta, poderá pular para [Criar um novo aplicativo ASP.NET MVC](#_Toc395637762).

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../includes/documentdb-keys.md)]

<br/>
Agora vamos abordar como criar um novo aplicativo ASP.NET MVC desde o início. 

## <a name="_Toc395637762"></a>Etapa 2: Criar um novo aplicativo ASP.NET MVC

Agora vamos criar nosso novo projeto ASP.NET.

1. No Visual Studio, no menu **Arquivo**, vá para **Novo** e clique **Projeto**.

   	A caixa de diálogo **Novo Projeto** é exibida.
2. No painel **Tipos de projeto**, expanda **Modelos**, **Visual C#**, **Web** e selecione **Aplicativo Web ASP.NET**.

  	![Screen shot of the New Project dialog box with the ASP.NET Web Application project type highlighted](./media/documentdb-dotnet-application/image10.png)

3. Na caixa **Nome**, digite o nome do projeto. Este tutorial usa o nome "todo". 
4. Clique em **Procurar** para navegar até a pasta onde você deseja criar o projeto e, em seguida, clique em **OK**.

  	A caixa de diálogo **Novo Projeto ASP.NET** é exibida.

  	![Screen shot of the New ASP.NET Project dialog box with the MVC template highlighted and the Host in the cloud box checked](./media/documentdb-dotnet-application/image11.png)

5. No painel de modelos, selecione **MVC**.
6. Se você planeja hospedar seu aplicativo no Azure, **Hospedar na nuvem** na parte inferior direita. Selecionamos para hospedar na nuvem e executar o aplicativo hospedado no site do Azure. Selecionar essa opção provisiona previamente um Site do Azure para você e torna mais fácil a implantação do aplicativo de trabalho final. Se desejar hospedá-lo em outro local ou se não desejar configurar o Azure com antecedência, apenas desmarque **Hospedar na Nuvem**.
7. Clique em **OK** e deixe o Visual Studio fazer isso realizando scaffolding do modelo ASP.NET MVC vazio. 
8. Se você optar por hospedá-lo na Nuvem, verá pelo menos uma tela adicional pedindo para você fazer logon na conta do Azure e fornecer alguns valores para o novo site. Forneça os valores adicionais e continue. 

  	Não escolhi "Servidor de banco de dados" aqui porque não estamos usando o Servidor do Banco de Dados SQL do Azure; vamos criar uma nova Conta de Banco de Dados de Documentos do Azure posteriormente no portal de Visualização do Azure. 

  	![Screen shot of the Configure Microsoft Azure Website dialog box](./media/documentdb-dotnet-application/image11_1.png)

9. Depois que o Visual Studio concluir a criação do aplicativo MVC de texto clichê, você terá um aplicativo ASP.NET vazio que poderá ser executado localmente.

Vamos ignorar a execução local do projeto porque tenho certeza de que todos já vimos o aplicativo "Hello World" do ASP.NET. Vamos passar direto para a adição do Banco de Dados de Documentos a este projeto e a criação de nosso aplicativo.

## <a name="_Toc395637767"></a>Etapa 3: Adicionar o Banco de Dados de Documentos ao seu projeto

Isso cuida da maioria das conexões ASP.NET MVC necessárias para
essa solução. Agora vamos abordar a finalidade real deste tutorial, adicionando o Banco de Dados de Documentos do Azure ao seu aplicativo Web.

1. O SDK .NET do Banco de Dados de Documentos é distribuído como um pacote NuGet. Para obter o pacote NuGet no Visual Studio, use o Gerenciador de Pacotes NuGet no Visual Studio clicando com o botão direito no projeto no **Gerenciador de Soluções** e, em seguida, clicando em **Gerenciar Pacotes NuGet**.

  	A caixa de diálogo **Gerenciar pacotes NuGet** será exibida.

  	![Sreen shot of the right-click options for the project in Solution Explorer, with Manage NuGet Packages highlighted.](./media/documentdb-dotnet-application/image21.png)

2. Na caixa **Pesquisar Online**, digite "Banco de Dados de Documentos do Azure". Nos resultados, instale o pacote **Bibliotecas de cliente do Banco de Dados de Documentos do Microsoft Azure**. Essa ação baixará e instalará o pacote do Banco de Dados de Documentos, bem como todas as dependências, como Newtonsoft.Json.

  	> [AZURE.NOTE] Enquanto o serviço ainda estiver em visualização, o pacote NuGet ficará marcado como "Pré-lançamento". Por isso, você precisará incluir a opção "Incluir Pré-lançamento" ou não encontrará o pacote. 

  	Se preferir, poderá usar o Console de Comando do Pacote para instalar o pacote digitando o seguinte.

    	Install-Package Microsoft.Azure.Documents.Client -Pre

3. Depois de instalada, sua solução do Visual Studio deverá se parecer com a imagem a seguir, com duas novas referências adicionadas: Microsoft.Azure.Documents.Client e Newtonsoft.Json.

  	![Sreen shot of the two references added to the project in Solution Explorer.](./media/documentdb-dotnet-application/image22.png)


##<a name="_Toc395637763"></a>Etapa 4: Configurar o aplicativo ASP.NET MVC
 
Agora vamos adicionar os modelos, exibições e controladores a este aplicativo MVC:

- [Adicionar um modelo](#_Toc395637764).
- [Adicionar um controlador](#_Toc395637765).
- [Adicionar exibições](#_Toc395637766).


### <a name="_Toc395637764"></a>Adicionar um modelo

Vamos começar criando o **M** no MVC, o modelo. 

1. No Gerenciador de Soluções, clique com o botão direito do mouse na pasta **Modelos**, clique em **Adicionar** e, em seguida, em **Classe**.

  	A caixa de diálogo **Adicionar Novo Item** é exibida.

2. Nomeie a nova classe **Item** e adicione o seguinte código ao novo arquivo Item.cs.

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

	Todos os dados no Banco de Dados de Documentos são transferidos e armazenados como JSON. Para controlar a forma como seus objetos são serializados/desserializados pelo JSON.NET, você pode usar o atributo **JsonProperty**, como demonstrado na classe **Item** criada há pouco. Você não é **obrigado** a fazer isso, mas quero garantir que minhas propriedades sigam as convenções de nomenclatura camelCase de JSON. 

	Além de poder controlar o formato do nome da propriedade quando ele entra no JSON, você também pode renomear totalmente as propriedades .NET, como fiz com a propriedade **Descrição**. 

	Se desejar, você também pode usar objetos **JsonConverter** aqui para controlar totalmente como a serialização é manipulada.  

3. Para que o Visual Studio resolva o atributo **JsonProperty** usado aqui, é preciso adicionar a instrução using a seguir na seção using de seu arquivo de classe.

    	using Newtonsoft.Json;

### <a name="_Toc395637765"></a>Adicionar um controlador

Isso cuida do **M**. Agora, vamos criar o **C** no MVC, uma classe de controlador.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Controladores** e clique em **Adicionar** e, em seguida, em **Controlador**.

    A caixa de diálogo **Adicionar Scaffold** é exibida.

2. Selecione **Controlador MVC 5 - Vazio** e, em seguida, clique em **Adicionar**.

	![Screen shot of the Add Scaffold dialog box with the MVC 5 Controller - Empty option highlighted](./media/documentdb-dotnet-application/image14.png)

3. Nomeie o novo Controlador, **ItemController.**

	![Screen shot of the Add Controller dialog box](./media/documentdb-dotnet-application/image15.png)

Depois de o arquivo ser criado, sua solução do Visual Studio deverá se parecer com a imagem a seguir, com o novo arquivo ItemController.cs no **Gerenciador de Soluções**.

![Screen shot of Solution Explorer with the new ItemController.cs file and Item.cs file highlighted](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766"></a>Adicionar exibições

E, por fim, vamos criar o **V** no MVC, as exibições:

- [Adicionar uma exibição de Índice do Item](#AddItemIndexView).
- [Adicionar uma exibição de Novo Item](#AddNewIndexView).
- [Adicionar uma exibição de Editar Item](#_Toc395888515).


#### <a name="AddItemIndexView"></a>Adicionar uma exibição de Índice do Item

1. No **Gerenciador de Soluções**, expanda a pasta ***Exibições***, clique com o botão direito na pasta vazia **Item** que o Visual Studio criou quando você adicionou o **ItemController** anteriormente, clique em **Adicionar** e, em seguida, clique em **Exibição**.

![Screen shot of Solution Explorer showing the Item folder with the Add View commands highlighted](./media/documentdb-dotnet-application/image17.png)

2. Na caixa de diálogo **Adicionar Exibição**, faça o seguinte:
	- Na caixa **Nome de Exibição**, digite ***Índice***.
	- Na caixa **Modelo**, selecione ****List****.
	- Na caixa **Classe do modelo**, selecione ***Item (todo.Models)***.
	- Na caixa de página de layout, digite ***~/Views/Shared/_Layout.cshtml***.
	- Clique em **Adicionar**.
	
![Screen shot showing the Add View dialog box](./media/documentdb-dotnet-application/image18.png)

3. Depois de definir todos esses valores, clique em **Adicionar** e deixe o Visual Studio criar sua exibição para você. O Visual Studio criará uma exibição do modelo. Feito isso, ele abrirá o arquivo cshtml criado. Podemos fechar esse arquivo no Visual Studio, pois voltaremos a ele mais tarde.

#### <a name="AddNewIndexView"></a>Adicionar uma exibição de Novo Item

Semelhante a como criamos uma exibição de **Índice do Item**, vamos agora criar uma nova exibição para criar novos **Itens**.

Na caixa de diálogo **Adicionar Exibição**, faça o seguinte:

- Na caixa **Nome de Exibição**, digite ***Create***.
- Na caixa **Modelo**, selecione ***Create***.
- Na caixa **Classe do modelo**, selecione ***Item (todo.Models)***.
- Na caixa de página de layout, digite ***~/Views/Shared/_Layout.cshtml***.
- Clique em **Adicionar**.

![Screen shot showing the Add View dialog box](./media/documentdb-dotnet-application/image19.png)

#### <a name="_Toc395888515"></a>Adicionar uma exibição de Editar Item

E, por fim, adicione uma última exibição para editar um **Item** da mesma maneira que foi feito antes.


Na caixa de diálogo **Adicionar Exibição**, faça o seguinte:

- Na caixa **Nome de exibição**, digite ***Editar***.
- Na caixa **Modelo**, selecione ***Editar***.
- Na caixa **Classe do modelo**, selecione ***Item (todo.Models)***.
- Selecione **Criar como uma exibição parcial**.
- Clique em **Adicionar**.

![Screen shot showing the Add View dialog box](./media/documentdb-dotnet-application/image20.png)

Feito isso, feche os documentos cshtml no Visual Studio, pois voltaremos a essas exibições mais tarde.

## <a name="_Toc395637769"></a>Etapa 5: Conectando o Banco de Dados de Documentos

Nesta seção, vamos adicionar código para tratar o seguinte:

- [Listando itens incompletos](#_Toc395637770).
- [Adicionando itens](#_Toc395637771).
- [Editando itens](#_Toc395637772).

### <a name="_Toc395637770"></a>Listando itens incompletos

1. Abra o **ItemController** e remova todo o código dentro da classe (mas deixe a classe) que o Visual Studio adicionou. Vamos recompilá-lo parte por parte usando o Banco de Dados de Documentos.

2. Adicione o seguinte trecho de código à classe de **ItemController** que agora está vazia.

    	public ActionResult Index()
    	{
			var items = DocumentDBRepository<Item>.GetItems(d => !d.Completed);
			return View(items);
    	}

	Esse código também usa uma classe de "pseudorrepositório" chamada **DocumentDBRepository**, que ainda temos que criar. Na verdade, isso é apenas uma classe Auxiliar que contém todos os códigos específicos do Banco de Dados de Documentos. Para as finalidades deste passo a passo, não vamos implementar uma camada completa de acesso a dados com injeção de dependência, fábricas e padrões de repositório, como você provavelmente faria se estivesse criando um aplicativo real. 
	Para as finalidades deste passo a passo, vamos apenas colocar toda a lógica de acesso a dados diretamente em um projeto para manter a simplicidade e o foco nos bits específicos do Banco de Dados de Documentos.

3. Adicione uma nova classe ao seu projeto e chame-a de **DocumentDBRepository**. 
4. Substitua o código na classe **DocumentDBRepository** pelo seguinte.

    	public static class DocumentDBRepository<T>
    	{
	       using Microsoft.Azure.Documents; 
	       using Microsoft.Azure.Documents.Client; 
	       using Microsoft.Azure.Documents.Linq; 
		
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

5. Passe algum tempo corrigindo todos os namespaces no Visual Studio, que deve incluir adicionar as seguinte diretivas using ao arquivo DocumentDBRepository.cs.

		using Microsoft.Azure.Documents;
		using Microsoft.Azure.Documents.Client;
		using Microsoft.Azure.Documents.Linq;
		using System.Configuration;

 	Todos os namespaces serão fáceis de resolver no Visual Studio desde que o pacote NuGet tenha sido instalado com êxito. As referências aos métodos **ReadOrCreateDatabase** e **ReadOrCreateCollection** continuarão sem solução até que eles sejam adicionados, o que faremos em seguida.
 
6. Há duas chamadas de método usadas aqui para leitura ou criação de Bancos de Dados do Banco de Dados de Documentos e de Coleções de Documentos. Por isso, adicione os dois métodos a seguir à classe **DocumentDBRepository**.

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

	Esse código toma conta da instalação do banco de dados, um [**DocumentCollection**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.documentcollection.aspx), e da criação de alguns códigos para se conectar ao Banco de Dados de Documentos por meio de [**DocumentClient**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx). 

7. Estamos lendo alguns valores por meio da configuração, por isso abra o arquivo **Web.config** e adicione as linhas a seguir na seção `<AppSettings>`.
	
    	<add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    	<add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    	<add key="database" value="ToDoList"/>
    	<add key="collection" value="Items"/>
	
8. Agora, atualize esses valores usando a folha de Chaves do Portal de Gerenciamento do Azure. Use o valor de **URI** da folha de chaves como o valor da chave do ponto de extremidade e use o valor de **PRIMARY KEY** da folha Chaves como o valor da chave authKey.
	
	Agora vamos adicionar alguns códigos para fazer o trabalho. 	

9. A primeira coisa que desejamos fazer com um aplicativo de lista de tarefas é exibir os itens incompletos.  O método abaixo faz isso para você, então copie e cole-o em qualquer lugar dentro da classe **DocumentDBRepository**.

	    public static IEnumerable<T> GetItems(Expression<Func<T, bool>> predicate) 
	    {
			return Client.CreateDocumentQuery<T>(Collection.DocumentsLink) 
				.Where(predicate) 
				.AsEnumerable(); 
		} 
	    
	Neste ponto, sua solução deve ser capaz de compilar sem erros.

	Se você executar o aplicativo agora, deverá ir para o **Controlador Inicial** e a exibição **Índice** desse controlador. Esse é o comportamento padrão para o projeto do modelo MVC que escolhemos no início, mas não queremos isso! Vamos alterar o roteamento neste aplicativo MVC para alterar seu comportamento.

11. Abra ***App\_Start\RouteConfig.cs*** e localize a linha que começa com "defaults:" e altere-a para que se pareça com o seguinte;

    	defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

	Agora isso informa ao ASP.NET MVC que se você não especificou um valor na URL para controlar o comportamento de roteamento que, em vez de **Home**, usa **Item** como controlador e o usuário **Índice** como exibição.
	Agora, se você executar o aplicativo, ele será chamado para seu **ItemController** e retornará os resultados do método **GetIncompleteItems** para a exibição **Views**\\**Item**\\**Index**. 

12. Se você compilar e executar esse projeto agora, deverá ver algo parecido com isto.    

	![Screen shot of the todo list web application created by this tutorial](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771"></a>Adicionando itens

Vamos colocar alguns itens em nosso banco de dados; assim, temos algo mais que uma grade vazia para observar.

Já temos uma exibição para **Criar** e um botão na exibição de **Índice** que levará o usuário para a exibição **Criar**. Vamos adicionar algum código ao controlador e ao repositório para persistir no registro no Banco de Dados de Documentos.

1. Abra o arquivo ItemController.cs e adicione o seguinte trecho de código, que é como o ASP.NET MVC sabe o que fazer para a ação **Criar**. Nesse caso, basta renderizar a exibição de Create.cshtml associada criada anteriormente.

    	public ActionResult Create()
    	{ 
			return View(); 
   		}

	Agora precisamos de mais algum código nesse controlador que aceitará o envio por meio da exibição **Criar**.

2. Adicione o próximo bloco de código que diz ao ASP.NET MVC o que fazer com um formulário POST para esse controlador.
	
    	[HttpPost]
    	[ValidateAntiForgeryToken]
   	 	public async Task<ActionResult> Create([Bind(Include = 	"Id,Name,Description,Completed")] Item item)  
  	  	{
			if (ModelState.IsValid)  
			{  
			    await DocumentDBRepository<Item>.CreateItemAsync(item);
			    return RedirectToAction("Index");  
			}   
			return View(item);   
		}

	**Observação de segurança**: O atributo **ValidateAntiForgeryToken** é usado aqui para ajudar a proteger esse aplicativo contra ataques de solicitação entre sites forjada. Há mais do que apenas adicionar esse atributo, as exibições precisam trabalhar com esse token antifalsificação também. Para saber mais sobre o assunto e ver exemplos de como implementar isso corretamente, consulte [Prevenindo solicitação intersite forjada][]. O código-fonte fornecido no [GitHub][] já tem a implementação completa.

	**Observação de segurança**: Também usamos o atributo **Bind** no parâmetro de método para ajudar na proteção contra ataques de overposting. Para obter mais detalhes, consulte [Basic CRUD Operations in ASP.NET MVC (Operações CRUD básicas no ASP.NET MVC)][].

3. Agora que o método **Criar** está em vigor, o **ItemController** passará o objeto **Item** do formulário para o método **CreateDocument**. Agora, adicione o seguinte método à sua classe **DocumentDBRepository**.

    	public static async Task<Document> CreateItemAsync(T item)
   	 	{
   	   		return await Client.CreateDocumentAsync(Collection.SelfLink, item);
   		}

	Este método simplesmente pega algum objeto passado para ele e persiste-o no Banco de Dados de Documentos.

Isso conclui o código exigido para adicionar novos itens ao nosso banco de dados.


### <a name="_Toc395637772"></a>Editando itens

Existe uma última ação para realizarmos, e esta é adicionar a capacidade de editar **itens** no banco de dados e marcá-los como concluídos. A exibição de edição já foi adicionada ao projeto, portanto precisamos adicionar um código ao nosso controlador e à classe **DocumentDBRepository** novamente.

1. Adicione o seguinte à classe **ItemController**.

    	[HttpPost]
   		[ValidateAntiForgeryToken]
    	public async Task<ActionResult> Edit([Bind(Include = "Id,Name,Description,Completed")] Item item)
    	{
     	   if (ModelState.IsValid)
    	    {
    	        await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
    	        return RedirectToAction("Index");
    	    }

  	      return View(item);
 	   	}
		
		public ActionResult Edit(string id)
		{
		    if (string.IsNullOrEmpty(id))
		    {
		        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
		    }
					 
		    Item item = (Item)DocumentDBRepository<Item>.GetItem(d => d.Id == id);
		 	
		    if (item == null)
		    {
		        return HttpNotFound();
		    }
		 	
		    return View(item);
		}
		
	
	O primeiro método lida com o Http GET que ocorrerá quando o usuário clicar no link **Editar** na exibição **Índice**. Esse método busca um [**Documento**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) do Banco de Dados de Documentos e o transfere para a exibição **Editar**.

	Em seguida, a exibição **Editar** fará um Http POST para o **IndexController**. 
	
	O segundo método que adicionamos lida com isso transferindo objeto atualizado por meio do Banco de Dados de Documentos para que seja persistido no banco de dados.

2. Adicione a seguinte diretiva using ao arquivo ItemController.cs.

		using todo.Models;
		using System.Threading.Tasks;
		using System.Net;

3. Adicione o seguinte à classe **DocumentDBRepository**.

    	public static T GetItem(Expression<Func<T, bool>> predicate)
    	{
        	return Client.CreateDocumentQuery<T>(Collection.DocumentsLink)
                    	.Where(predicate)
                    	.AsEnumerable()
                    	.FirstOrDefault();
    	}	
		
		public static Document GetDocument(string id) 
		{ 
			return Client.CreateDocumentQuery(Collection.DocumentsLink) 
				.Where(d => d.Id == id) 
				.AsEnumerable() 
				.FirstOrDefault(); 
		} 

    	public static async Task<Document> UpdateItemAsync(string id, T item)
    	{
        	Document doc = GetDocument(id);	
        	return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    	}

	O primeiro desses métodos busca um **Item** do Banco de Dados de Documentos que é transferido de volta para o **ItemController** e, em seguida, para a exibição **Editar**.
	
	O segundo dos dois métodos que acabamos de adicionar substitui o **Documento** no Banco de Dados de Documentos pela versão do **Documento** transferido do**ItemController**.

4. Adicione a seguinte diretiva using ao arquivo DocumentDBRepository.cs.

		using System.Threading.Tasks;

	Isso é tudo que precisamos para executar nosso aplicativo, listar **Itens** incompletos, adicionar novos **Itens** e, por fim, editar **Itens**.

## <a name="_Toc395637773"></a>Etapa 6: Executar o aplicativo localmente

Execute as etapas a seguir para testar o aplicativo em seu computador local:

1. Pressione F5 no Visual Studio para compilar o aplicativo no modo de depuração. Ele deve compilar o aplicativo e iniciar um navegador com a página de grade vazia que vimos antes:

	![Screen shot of the todo list web application created by this tutorial](./media/documentdb-dotnet-application/image24.png)

	Se você encontrar erros neste ponto, poderá comparar seu código com o tutorial de tarefas no [GitHub][].

2. Clique no link **Criar Novo** e adicione valores aos campos **Nome** e **Descrição**. Deixe a caixa de seleção **Concluído** desmarcada, caso contrário, o novo **Item** será adicionado em um estado concluído e não aparecerá na lista inicial.

	![Screen shot of the Create view](./media/documentdb-dotnet-application/image25.png)

3. Clique em **Criar** e você será redirecionado para a exibição **Índice** e seu **Item** aparece na lista.

	![Screen shot of the Index view](./media/documentdb-dotnet-application/image26.png)

	Fique à vontade para adicionar mais alguns **Itens** à sua lista de Tarefas Pendentes.

3. Clique em **Editar** próximo a um **Item** na lista e você será direcionado para a exibição **Editar**, onde poderá atualizar qualquer propriedade do objeto, incluindo o sinalizador **Concluído**. Se você marcar o sinalizador **Concluir** e clicar em **Salvar**, o **Item** é removido da lista de tarefas não concluídas.

	![Screen shot of the Index view with the Completed box checked](./media/documentdb-dotnet-application/image27.png)

4. Após testar o aplicativo, pressione Ctrl+F5 para parar a depuração do aplicativo. Você está pronto para implantar!

##<a name="_Toc395637774"></a>Etapa 7: Implantar o aplicativo nos Sites do Azure

Agora que você tem o aplicativo completo funcionando corretamente no Banco de Dados de Documentos, vamos implantar este aplicativo da Web nos Sites do Azure. Se você selecionou **Hospedar na nuvem** quando criamos o projeto ASP.NET MVC vazio, o Visual Studio tornará essa ação muito fácil e fará a maior parte do trabalho para nós. 

Para publicar este aplicativo, tudo o que você precisa fazer é clicar com o botão direito no projeto no **Gerenciador de Soluções** e clicar em **Publicar**.

![Screen shot of the Publish option in Solution Explorer](./media/documentdb-dotnet-application/image28.png)

Tudo já deve estar configurado de acordo com suas credenciais; na verdade, o site já foi criado no Azure para você na **URL de Destino** mostrada, basta clicar em **Publicar**.

![Screen shot of the Publish Web dialog box](./media/documentdb-dotnet-application/image29.png)

Em poucos segundos, o Visual Studio terminará de publicar seu aplicativo Web e iniciará um navegador onde você poderá ver seu trabalho sendo executado no Azure!

##<a name="_Toc395637775"></a>Próximas etapas

Parabéns! Você acabou de criar seu primeiro Aplicativo ASP.NET MVC usando o Banco de Dados de Documentos do Azure e publicá-lo nos sites do Azure. O código-fonte para o aplicativo completo, incluindo as funcionalidade de detalhes e exclusão que não foram incluídas neste tutorial, pode ser baixado ou clonado do [GitHub][]. Então, se você estiver interessado em adicioná-lo ao seu aplicativo, pegue o código e adicione-o a esse aplicativo.

Para adicionar funcionalidades adicionais ao seu aplicativo, veja as APIs disponíveis na [Biblioteca do Banco de Dados de Documentos .NET](http://msdn.microsoft.com/library/azure/dn783362.aspx) e fique à vontade para contribuir com a biblioteca no [GitHub][]. 

##<a id="GetProject"></a>Obtenha a solução do GitHub

Se estiver buscando economizar tempo e deseja apenas compilar a solução completa de tarefas e não adicionar o código por conta própria, você está com sorte. A solução completa está disponível no GitHub e você pode compilá-la e implantá-la em poucos minutos usando as instruções a seguir.

1. Verifique se você tem o [software de pré-requisito](#_Toc395637760) instalado, o que inclui o Visual Studio e o SDK do Azure para .NET versão 2.3 ou posterior.

2. Clone o repositório azure-documentdb-net usando o Git para Windows ([http://www.git-scm.com/](http://www.git-scm.com/)) ou baixe o arquivo zip do [GitHub](https://github.com/Azure/azure-documentdb-net/).

2. No Visual Studio, abra o arquivo todo.sln do diretório azure-documentdb-net/tutorials/todo.

3. Para restaurar as referências do Banco de Dados de Documentos do SDK do .NET no Visual Studio 2013, clique com o botão direito na solução de todo no **Gerenciador de Soluções** e, em seguida, clique em **Habilitar pacote de restauração NuGet**, que restaurará as referências. 

4. Recupere os valores **URI** e **PRIMARY KEY** ou **SECONDARY KEY** da folha **Chaves** da sua conta do Banco de Dados de Documentos no [portal de Visualização do Azure](https://portal.azure.com/). 

	
	Se você não tiver uma conta, consulte [Criar uma conta de banco de dados](documentdb-create-account.md) para configurá-la.

	![Screen shot of the Azure Preview portal, showing a DocumentDB account, with the ACTIVE hub highlighted, the Keys button highlighted on the DocumentDB account blade, and the URI, PRIMARY KEY and SECONDARY KEY values highlighted on the Keys blade](../includes/media/documentdb-keys/keys.png)

5. No arquivo Web.config, atualize os valores padrão para as chaves **endpoint** e **authKey**.

    	<add key="endpoint" value="~enter URI for your DocumentDB Account, from Azure Management Portal~" /> 
		<add key="authKey" value="~enter either Primary or Secondary key for your DocumentDB Account, from Azure Management Portal~" /> 

	- Copie o valor de **URI** do valor da folha Chaves e cole-o no valor da propriedade **endpoint**. 
	- Copie o valor de **PRIMARY KEY** ou **SECONDARY KEY** da folha **Chaves** e cole-o no valor da propriedade **authKey**.
	


7. Agora você pode [executar o aplicativo localmente](#_Toc395637773) e [implantá-lo em sites do Azure](#_Toc395637774).


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[GitHub]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[Impedindo a falsificação de solicitação entre sites]: http://go.microsoft.com/fwlink/?LinkID=517254
[Operações CRUD Básicas no ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598

<!--HONumber=49-->