<properties
	pageTitle="Tutorial do NoSQL: SDK do .NET do Banco de Dados de Documentos | Microsoft Azure"
	description="Um tutorial do NoSQL que cria um banco de dados online e um aplicativo de console C# usando o SDK do .NET do Banco de Dados de Documentos. O Banco de Dados de Documentos é um banco de dados NoSQL para JSON."
	keywords="tutorial do nosql, banco de dados online, aplicativo de console c#"
	services="documentdb"
	documentationCenter=".net"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/30/2016"
	ms.author="anhoh"/>

# Tutorial do NoSQL: criar um aplicativo de console em C# do Banco de Dados de Documentos

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

Bem-vindo ao tutorial do NoSQL para o SDK do .NET do Banco de Dados de Documentos! Após seguir este tutorial, você terá um aplicativo de console que cria e consulta recursos de Banco de Dados de Documentos.

Abordaremos:

- Criando e se conectando a uma conta do Banco de Dados de Documentos
- Configurar a sua Solução do Visual Studio
- Criando um banco de dados online
- Criar uma coleção
- Criando documentos JSON
- Consultar a coleção
- Excluir o banco de dados

Você não tem tempo? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). Consulte [Obter a solução completa](#GetSolution) para as instruções rápidas.

Em seguida, use os botões de votação na parte superior ou inferior desta página para nos enviar comentários. Se quiser que entremos em contato com você diretamente, inclua seu endereço de email em seu comentário.

Agora vamos começar!

## Pré-requisitos

Certifique-se que você tem o seguinte:

- Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [Avaliação Gratuita](https://azure.microsoft.com/pricing/free-trial/).
- [Visual Studio 2013/Visual Studio 2015](http://www.visualstudio.com/).

## Etapa 1: Criar uma conta do Banco de Dados de Documentos

Vamos criar uma conta de Banco de Dados de Documentos. Se você já tem uma conta que deseja usar, você pode pular para [Configurar sua solução do Visual Studio](#SetupVS).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a> Etapa 2: Configurar a sua solução do Visual Studio

1. Abra o **Visual Studio** em seu computador.
2. No menu **Arquivo**, selecione **Novo** e depois **Projeto**.
3. Na caixa de diálogo **Novo Projeto**, selecione **Modelos**/**Visual C#**/**Aplicativo de Console**, nomeie o projeto e clique em **OK**.
4. No **Gerenciador de Soluções**, clique com o botão direito do mouse no seu novo aplicativo de console, que está em sua solução do Visual Studio.
5. Sem sair do menu, clique em **Gerenciar Pacotes NuGet...**
6. No canto esquerdo do painel, na janela **Gerenciar Pacotes NuGet**, clique em **Online** / **nuget.org**.
7. Na caixa de entrada **Pesquisar Online**, procure a **Biblioteca do cliente do Banco de Dados de Documentos**.
8. Nos resultados, encontre **Biblioteca do cliente do Banco de Dados de Documentos do Microsoft Azure** e clique em **Instalar**. A ID do pacote da Biblioteca de cliente do Banco de Dados de Documentos é [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)

Ótimo! Agora que a instalação está concluída, vamos começar a escrever algum código. Você pode encontrar um projeto de código completo deste tutorial no [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

##<a id="Connect"></a> Etapa 3: Conectar-se a uma conta do Banco de Dados de Documentos

Primeiro, adicione essas referências para o início de seu aplicativo C#, no arquivo Program.cs:

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

> [AZURE.IMPORTANT] Para concluir este tutorial do NoSQL, adicione as dependências acima.

Em seguida, salve o ponto de extremidade de conta do Banco de Dados de Documentos e uma das duas chaves de acesso, a primária ou a secundária, que pode ser encontrada no [Portal do Azure](https://portal.azure.com).

![Captura de tela do Portal do Azure usado pelo tutorial do NoSQL para criar um aplicativo de console em C#. Mostra uma conta do Banco de Dados de Documentos com o hub ATIVO realçado, o botão CHAVES realçado na folha da conta do Banco de Dados de Documentos e os valores de URI, de CHAVE PRIMÁRIA e de CHAVE SECUNDÁRIA realçados na folha Chaves][keys]

    private const string EndpointUrl = "<your endpoint URI>";
    private const string AuthorizationKey = "<your key>";

Iniciaremos o aplicativo de demonstração de introdução criando uma nova instância do **DocumentClient**. Crie uma nova tarefa assíncrona chamada **GetStartedDemo** e crie uma instância do nosso novo **DocumentClient**.

	private static async Task GetStartedDemo()
	{
		// Create a new instance of the DocumentClient
		DocumentClient client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
	}

Chame a tarefa assíncrona por meio do seu método **Main**, semelhante ao código abaixo.

	public static void Main(string[] args)
	{
		try
		{
			GetStartedDemo().Wait();
		}
		catch (Exception e)
		{
			Exception baseException = e.GetBaseException();
			Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
		}
	}

Agora que você sabe como conectar uma conta do Banco de Dados de Documentos e criar uma instância da classe **DocumentClient**, vejamos como trabalhar com os recursos do Banco de Dados de Documentos.

## Etapa 4: Criar um banco de dados online
Seu [banco de dados](documentdb-resources.md#databases) do Banco de Dados de Documentos pode ser criado usando o método [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) da classe **DocumentClient**. Um banco de dados é o contêiner lógico de armazenamento de documentos JSON particionado em coleções. Criar um novo banco de dados no seu método **GetStartedDemo** após a criação do **DocumentClient**.

	// Check if the database FamilyDB does not exist
	string databaseName = "FamilyDB";
	
	try
	{
		await this.client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(familyName));
	}
	catch (DocumentClientException de)
	{
		// If the database does not exist, create a new database
		if (de.StatusCode == HttpStatusCode.NotFound)
		{
			await this.client.CreateDatabaseAsync(new Database { Id = familyName });
			this.WriteToConsoleAndPromptToContinue("Created {0}", databaseName);
		}
		else
		{
			throw;
		}
	}
	
##<a id="CreateColl"></a>Etapa 5: Criar uma coleção  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** criará uma nova coleção com taxa de transferência reservada, que tem implicações de preços. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).

É possível criar uma [coleção](documentdb-resources.md#collections) usando o método [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) da classe **DocumentClient**. Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada. Crie uma nova coleção chamada **FamilyCollection** após a criação de seu banco de dados no método **GetStartedDemo**.

	string collectionName = "FamilyCollection";
	try
	{
		await this.client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName));
	}
	catch (DocumentClientException de)
	{
		// If the document collection does not exist, create a new collection
		if (de.StatusCode == HttpStatusCode.NotFound)
		{
			// Configure the collection. Optionally, you can configure partitioning and indexing behavior of the collection here.
			DocumentCollection collectionInfo = new DocumentCollection();
			collectionInfo.Id = collectionName;

			// DocumentDB collections can be reserved with throughput specified in request units/second. 1 RU is a normalized request equivalent to the read
			// of a 1KB document.  Here we create a collection with 400 RU/s. 
			await this.client.CreateDocumentCollectionAsync(
				UriFactory.CreateDatabaseUri(databaseName),
				new DocumentCollection { Id = collectionName },
				new RequestOptions { OfferThroughput = 400 });

			this.WriteToConsoleAndPromptToContinue("Created {0}", collectionName);
		}
		else
		{
			throw;
		}
	}

##<a id="CreateDoc"></a>Etapa 6: Criar documentos JSON
Um [documento](documentdb-resources.md#documents) pode ser criado usando o método [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) da classe **DocumentClient**. Os documentos são conteúdo JSON (arbitrário) definido pelo usuário. Agora podemos inserir um ou mais documentos. Se já tiver dados que gostaria de armazenar em seu banco de dados, você pode usar a[ferramenta de Migração de Dados](documentdb-import-data.md) do Banco de Dados de Documentos.

Primeiro, precisamos criar uma classe **Family** que representará objetos armazenados no Banco de Dados de Documentos neste exemplo. Também criaremos subclasses **Parent**, **Child**, **Pet** e **Address** que são usadas em **Family**. Observe que os documentos devem ter uma propriedade **Id** serializada como **id** em JSON. Crie essas classes, adicionando as seguintes subclasses internas após o método **GetStartedDemo**.

	public class Family
	{
		[JsonProperty(PropertyName = "id")]
		public string Id { get; set; }
		public string LastName { get; set; }
		string string District { get; set; }
		public Parent[] Parents { get; set; }
		public Child[] Children { get; set; }
		public Address Address { get; set; }
		public bool IsRegistered { get; set; }
	}
	
	public class Parent
	{
		public string FamilyName { get; set; }
		public string FirstName { get; set; }
	}

	public class Child
	{
		public string FamilyName { get; set; }
		public string FirstName { get; set; }
		public string Gender { get; set; }
		public int Grade { get; set; }
		public Pet[] Pets { get; set; }
	}

	public class Pet
	{
		public string GivenName { get; set; }
	}

	public class Address
	{
		public string State { get; set; }
		public string County { get; set; }
		public string City { get; set; }
	}


Em seguida, crie os documentos dentro de seu método assíncrono **GetStartedDemo**. Primeiro, crie um método **CreateFamilyDocumentIfNotExists** conforme mostrado abaixo:

	private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
	{
		try
		{
			await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
		}
		catch (DocumentClientException de)
		{
			if (de.StatusCode == HttpStatusCode.NotFound)
			{
				await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
				this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
			}
			else
			{
				throw;
			}
		}
	}

Insira dois documentos, um para a Família Martins e um para a Família Barros, conforme mostrado abaixo:

	// Insert a document, here we create a Family object
	Family andersenFamily = new Family
	{
		Id = "Andersen.1",
		LastName = "Andersen",
		Parents = new Parent[] 
		{
			new Parent { FirstName = "Thomas" },
			new Parent { FirstName = "Mary Kay" }
		},
		Children = new Child[] 
		{
			new Child
			{
				FirstName = "Henriette Thaulow",
				Gender = "female",
				Grade = 5,
				Pets = new Pet[] 
				{
					new Pet { GivenName = "Fluffy" }
				}
			}
		},
		District = "WA5",
		Address = new Address { State = "WA", County = "King", City = "Seattle" },
		IsRegistered = true
	};

	await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

	Family wakefieldFamily = new Family
	{
		Id = "Wakefield.7",
		LastName = "Wakefield",
		Parents = new Parent[]
		{
			new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
			new Parent { FamilyName = "Miller", FirstName = "Ben" }
		},
		Children = new Child[]
		{
			new Child
			{
				FamilyName = "Merriam",
				FirstName = "Jesse",
				Gender = "female",
				Grade = 8,
				Pets = new Pet[]
				{
					new Pet { GivenName = "Goofy" },
					new Pet { GivenName = "Shadow" }
				}
			},
			new Child
			{
				FamilyName = "Miller",
				FirstName = "Lisa",
				Gender = "female",
				Grade = 1
			}
		},
		District = "NY23",
		Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
		IsRegistered = false
	};

	await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

Agora, você criou o seguinte banco de dados, a coleta e os documentos em sua conta do Banco de Dados de Documentos.

![Diagrama que ilustra a relação hierárquica entre a conta, o banco de dados online, a coleção e os documentos usados pelo tutorial do NoSQL para criar um aplicativo de console em C#](./media/documentdb-get-started/nosql-tutorial-account-database.png)

##<a id="Query"></a>Etapa 7: Recursos de consulta do Banco de Dados de Documentos

O Banco de Dados de Documentos tem suporte para [consultas](documentdb-sql-query.md) avançadas de documentos JSON armazenados em cada coleção. O exemplo de código a seguir mostra diversas consultas - usando a sintaxe SQL do Banco de Dados de Documentos bem como o LINQ - que podem ser realizadas nos documentos que inserimos na etapa anterior. Adicione essas consultas ao método assíncrono **GetStartedDemo**.

	// Run a simple query via LINQ. DocumentDB indexes all properties, so queries can be completed efficiently and with low latency.
	// Here we find the Andersen family via its LastName
	IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
		UriFactory.CreateDocumentCollectionUri(databaseName, collectionName))
		.Where(f => f.LastName == "Andersen");

	// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
	Console.WriteLine("Running LINQ query...");
	foreach (Family family in familyQuery)
	{
			Console.WriteLine("\tRead {0}", family);
	}

	// Now execute the same query via direct SQL
	IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
		UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
		"SELECT * FROM Family WHERE Family.lastName = 'Andersen'");

	Console.WriteLine("Running direct SQL query...");
	foreach (Family family in familyQuery)
	{
		Console.WriteLine("\tRead {0}", family);
	}

O diagrama a seguir ilustra como a sintaxe de consulta do SQL do Banco de Dados de Documentos é chamada em relação à coleção que você criou e a mesma lógica se aplica à consulta LINQ.

![Diagrama que ilustra o escopo e o significado da consulta usada pelo tutorial do NoSQL para criar um aplicativo de console em C#](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

A palavra-chave [FROM](documentdb-sql-query.md#from-clause) é opcional na consulta, pois as consultas do Banco de Dados de Documentos já têm o escopo para uma única coleção. Portanto, "FROM Families f" pode ser trocado por "FROM root r" ou qualquer outra variável de nome que você escolher. O Banco de Dados de Documentos fará com que Families, root ou o nome de variável escolhido por você faça referência à coleção atual, por padrão.

##<a id="DeleteDatabase"></a>Etapa 8: excluir o banco de dados online

Excluir o banco de dados criado removerá o banco de dados e todos os recursos filhos (coleções, documentos, etc.). Você pode excluir o banco de dados e o cliente do documento, adicionando o seguinte trecho de código ao final do seu método assíncrono **GetStartedDemo**.

	// Clean up/delete the database
	await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));

##<a id="Run"></a>Etapa 9: Executar o aplicativo de console C#!

Agora você está pronto para executar o aplicativo. No final do método **Main**, adicione a seguinte linha de código, o que permitirá que você leia o console de saída antes de o aplicativo terminar a execução.

	Console.ReadLine();

Agora, pressione F5 no Visual Studio para compilar o aplicativo no modo de depuração.

Agora você verá a saída do aplicativo iniciado. A saída mostrará os resultados das consultas que adicionamos e deverá coincidir com o texto de exemplo abaixo.

	Created FamilyDB
	Press any key to continue ...
	Created FamilyCollection
	Press any key to continue ...
	Created Family Andersen.1
	Press any key to continue ...
	Created Family Wakefield.7
	Press any key to continue ...
	Running LINQ query...
		Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
	Running direct SQL query...
		Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
	End of demo, press any key to exit.

Parabéns! Você concluiu este tutorial do NoSQL e tem um aplicativo de console em C# funcional!

##<a id="GetSolution"></a> Obter a solução do tutorial do NoSQL completa
Para criar a solução de Introdução que contém todos os exemplos neste artigo, você precisará do seguinte:

-   [Conta de Banco de Dados de Documentos][documentdb-create-account].
-   A solução [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) disponível no GitHub.

Para restaurar as referências do Banco de Dados de Documentos do SDK do .NET no Visual Studio, clique com o botão direito do mouse na solução **GetStarted** no Gerenciador de Soluções e, em seguida, clique em **Habilitar Pacote de Restauração NuGet**. Em seguida, no arquivo App.config, atualize os valores EndpointUrl e AuthorizationKey conforme descrito em [Conectar-se a uma conta do Banco de Dados de Documentos](#Connect).

## Próximas etapas

-   Quer um tutorial mais complexo do NoSQL do ASP.NET MVC? Consulte [Criar um aplicativo Web com ASP.NET MVC usando o Banco de Dados de Documentos](documentdb-dotnet-application.md).
-	Saiba como [monitorar uma conta do Banco de Dados de Documentos](documentdb-monitor-accounts.md).
-	Executar consultas em nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
-	Saiba mais sobre o modelo de programação na seção Desenvolvimento da [Página de documentação do Banco de Dados de Documentos](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png

<!---HONumber=AcomDC_0330_2016-->