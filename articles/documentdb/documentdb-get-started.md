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
	ms.date="05/11/2016"
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
- Substituição de um documento
- Exclusão de um documento
- Excluir o banco de dados

Você não tem tempo? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). Vá para a [seção Obter a solução completa](#GetSolution) a fim de ver as instruções rápidas.

Em seguida, use os botões de votação na parte superior ou inferior desta página para nos enviar comentários. Se quiser que entremos em contato com você diretamente, inclua seu endereço de email em seu comentário.

Agora vamos começar!

## Pré-requisitos

Certifique-se que você tem o seguinte:

- Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [Avaliação Gratuita](https://azure.microsoft.com/pricing/free-trial/).
- [Visual Studio 2013/Visual Studio 2015](http://www.visualstudio.com/).
- .NET framework 4.6

## Etapa 1: Criar uma conta do Banco de Dados de Documentos

Vamos criar uma conta de Banco de Dados de Documentos. Se você já tem uma conta que deseja usar, você pode pular para [Configurar sua solução do Visual Studio](#SetupVS).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a> Etapa 2: Configurar a sua solução do Visual Studio

1. Abra o **Visual Studio 2015** em seu computador.
2. No menu **Arquivo**, selecione **Novo** e depois **Projeto**.
3. Na caixa de diálogo **Novo Projeto**, selecione **Modelos**/**Visual C#**/**Aplicativo de Console**, nomeie o projeto e clique em **OK**. ![Captura de tela da janela Novo Projeto](./media/documentdb-get-started/nosql-tutorial-new-project-2.png)
4. No **Gerenciador de Soluções**, clique com o botão direito do mouse no seu novo aplicativo de console, que está em sua solução do Visual Studio.
5. Sem sair do menu, clique em **Gerenciar Pacotes NuGet...** ![Captura de tela do menu exibido pelo clique com o botão direito do mouse para o projeto](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges.png)
6. Na guia **Nuget**, clique em **Procurar** e digite **banco de dados de documentos do azure** na caixa de pesquisa.
7. Nos resultados, encontre **Microsoft.Azure.DocumentDB** e clique em **Instalar**. A ID do pacote para a Biblioteca de Clientes do Banco de Dados de Documentos é [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB) ![Captura de tela do menu Nuget para localizar documentos do SDK do cliente do Banco de Dados de Documentos](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)

Ótimo! Agora que a instalação está concluída, vamos começar a escrever algum código. Você pode encontrar um projeto de código completo deste tutorial no [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

##<a id="Connect"></a> Etapa 3: Conectar-se a uma conta do Banco de Dados de Documentos

Primeiro, adicione essas referências para o início de seu aplicativo C#, no arquivo Program.cs:

    using System;
    using System.Linq;
    using System.Threading.Tasks;

    // ADD THIS PART TO YOUR CODE
    using System.Net;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Newtonsoft.Json;

> [AZURE.IMPORTANT] Para concluir este tutorial do NoSQL, adicione as dependências acima.

Agora, adicione essas duas constantes e sua variável *client* sob sua classe pública *Program*.

	public class Program
	{
		// ADD THIS PART TO YOUR CODE
		private const string EndpointUri = "<your endpoint URI>";
		private const string PrimaryKey = "<your key>";
		private DocumentClient client;

Em seguida, vá para o [Portal do Azure](https://portal.azure.com) para recuperar o URI e a chave primária. O URI do Banco de Dados de Documentos e a chave primária são necessárias para que seu aplicativo entenda onde deve se conectar e para que o Banco de Dados de Documentos confie na conexão do seu aplicativo.

No Portal do Azure, navegue até sua conta do Banco de Dados de Documentos da Etapa 1.

Clique ícone **chaves** na barra **Essentials**. Copie o URI e substitua *<your endpoint URI>* pelo URI copiado em seu programa. Copie a chave primária e substitua *<your key>* pela chave copiada em seu programa.

![Captura de tela do Portal do Azure usado pelo tutorial do NoSQL para criar um aplicativo de console em C#. Mostra uma conta do Banco de Dados de Documentos com o hub ATIVO realçado, o botão CHAVES realçado na folha da conta do Banco de Dados de Documentos e os valores de URI, de CHAVE PRIMÁRIA e de CHAVE SECUNDÁRIA realçados na folha Chaves][keys]

Iniciaremos o aplicativo de introdução criando uma nova instância de **DocumentClient**.

Abaixo do método **Main**, adicione esta nova tarefa assíncrona denominada **GetStartedDemo**, que criará uma instância do nosso novo **DocumentClient**.

	static void Main(string[] args)
	{
	}

	// ADD THIS PART TO YOUR CODE
	private async Task GetStartedDemo()
	{
		this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
	}

Adicione o código a seguir para executar a tarefa assíncrona a partir do seu método **Main**. O método **Main** irá capturar as exceções e escrevê-las no console.

	static void Main(string[] args)
	{
			// ADD THIS PART TO YOUR CODE
			try
			{
					Program p = new Program();
					p.GetStartedDemo().Wait();
			}
			catch (DocumentClientException de)
			{
					Exception baseException = de.GetBaseException();
					Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
			}
			catch (Exception e)
			{
					Exception baseException = e.GetBaseException();
					Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
			}
			finally
			{
					Console.WriteLine("End of demo, press any key to exit.");
					Console.ReadKey();
			}

Pressione **F5** para executar seu aplicativo.

Parabéns! Você se conectou a uma conta do Banco de Dados de Documentos. Agora, vamos conferir o trabalho com recursos do Banco de Dados de Documentos.

## Etapa 4: criar um banco de dados
Antes de adicionar o código para criar um banco de dados, adicione um método auxiliar para gravar no console.

Copie e cole o método **WriteToConsoleAndPromptToContinue** sob o método **GetStartedDemo**.

	// ADD THIS PART TO YOUR CODE
	private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
	{
			Console.WriteLine(format, args);
			Console.WriteLine("Press any key to continue ...");
			Console.ReadKey();
	}

Seu [banco de dados](documentdb-resources.md#databases) do Banco de Dados de Documentos pode ser criado usando o método [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) da classe **DocumentClient**. Um banco de dados é o contêiner lógico de armazenamento de documentos JSON particionado em coleções.

Copie e cole o método **CreateDatabaseIfNotExists** sob o método **WriteToConsoleAndPromptToContinue**.

	// ADD THIS PART TO YOUR CODE
	private async Task CreateDatabaseIfNotExists(string databaseName)
	{
			// Check to verify a database with the id=FamilyDB does not exist
			try
			{
					await this.client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(databaseName));
					this.WriteToConsoleAndPromptToContinue("Found {0}", databaseName);
			}
			catch (DocumentClientException de)
			{
					// If the database does not exist, create a new database
					if (de.StatusCode == HttpStatusCode.NotFound)
					{
							await this.client.CreateDatabaseAsync(new Database { Id = databaseName });
							this.WriteToConsoleAndPromptToContinue("Created {0}", databaseName);
					}
					else
					{
							throw;
					}
			}
	}

Copie e cole o código a seguir no seu método **GetStartedDemo** embaixo da criação do cliente. Isso criará um banco de dados denominado *FamilyDB*.

	private async Task GetStartedDemo()
	{
		this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

		// ADD THIS PART TO YOUR CODE
		await this.CreateDatabaseIfNotExists("FamilyDB");

Pressione **F5** para executar seu aplicativo.

Parabéns! Você criou um Banco de Dados de Documentos com sucesso!

##<a id="CreateColl"></a>Etapa 5: Criar uma coleção  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** criará uma nova coleção com uma taxa de transferência reservada, que tem implicações de preço. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).

É possível criar uma [coleção](documentdb-resources.md#collections) usando o método [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) da classe **DocumentClient**. Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada.

Copie e cole o método **CreateDocumentCollectionIfNotExists** embaixo do método **CreateDatabaseIfNotExists**.

	// ADD THIS PART TO YOUR CODE
	private async Task CreateDocumentCollectionIfNotExists(string databaseName, string collectionName)
	{
		try
		{
			await this.client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName));
			this.WriteToConsoleAndPromptToContinue("Found {0}", collectionName);
		}
		catch (DocumentClientException de)
		{
			// If the document collection does not exist, create a new collection
			if (de.StatusCode == HttpStatusCode.NotFound)
			{
				DocumentCollection collectionInfo = new DocumentCollection();
				collectionInfo.Id = collectionName;

				// Configure collections for maximum query flexibility including string range queries.
				collectionInfo.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

				// Here we create a collection with 400 RU/s.
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
	}

Copie e cole o código a seguir no seu método **GetStartedDemo** sob a criação do banco de dados. Isso criará uma coleção de documentos denominada *FamilyCollection*.

		this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

		await this.CreateDatabaseIfNotExists("FamilyDB");

		// ADD THIS PART TO YOUR CODE
		await this.CreateDocumentCollectionIfNotExists("FamilyDB", "FamilyCollection");

Pressione **F5** para executar seu aplicativo.

Parabéns! Você criou uma coleção de documentos do Banco de Dados de Documentos com sucesso!

##<a id="CreateDoc"></a>Etapa 6: Criar documentos JSON
Um [documento](documentdb-resources.md#documents) pode ser criado usando o método [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) da classe **DocumentClient**. Os documentos são conteúdo JSON (arbitrário) definido pelo usuário. Agora podemos inserir um ou mais documentos. Se já tiver dados que gostaria de armazenar em seu banco de dados, você pode usar a[ferramenta de Migração de Dados](documentdb-import-data.md) do Banco de Dados de Documentos.

Primeiro, precisamos criar uma classe **Family** que representará os objetos armazenados no Banco de Dados de Documentos neste exemplo. Também criaremos as subclasses **Parent**, **Child**, **Pet** e **Address** que são usadas em **Family**. Observe que os documentos devem ter uma propriedade **Id** serializada como **id** em JSON. Crie essas classes, adicionando as seguintes subclasses internas após o método **GetStartedDemo**.

Copie e cole as classes **Family**, **Parent**, **Child**, **Pet** e **Address** embaixo do método **WriteToConsoleAndPromptToContinue**.

	private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
	{
		Console.WriteLine(format, args);
		Console.WriteLine("Press any key to continue ...");
		Console.ReadKey();
	}

	// ADD THIS PART TO YOUR CODE
	public class Family
	{
		[JsonProperty(PropertyName = "id")]
		public string Id { get; set; }
		public string LastName { get; set; }
		public Parent[] Parents { get; set; }
		public Child[] Children { get; set; }
		public Address Address { get; set; }
		public bool IsRegistered { get; set; }
		public override string ToString()
		{
				return JsonConvert.SerializeObject(this);
		}
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

Copie e cole o método **CreateFamilyDocumentIfNotExists** embaixo do método **CreateDocumentCollectionIfNotExists**.

	// ADD THIS PART TO YOUR CODE
	private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
	{
		try
		{
			await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
			this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
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

Insira dois documentos, um para a Família Martins e um para a Família Barros.

Copie e cole o código a seguir no seu método **GetStartedDemo** embaixo da criação da coleção de documentos.

	await this.CreateDatabaseIfNotExists("FamilyDB");

	await this.CreateDocumentCollectionIfNotExists("FamilyDB", "FamilyCollection");

	// ADD THIS PART TO YOUR CODE
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
			Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
			IsRegistered = false
	};

	await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

Pressione **F5** para executar seu aplicativo.

Parabéns! Você criou dois documentos do Banco de Dados de Documentos com sucesso.

![Diagrama que ilustra a relação hierárquica entre a conta, o banco de dados online, a coleção e os documentos usados pelo tutorial do NoSQL para criar um aplicativo de console em C#](./media/documentdb-get-started/nosql-tutorial-account-database.png)

##<a id="Query"></a>Etapa 7: Recursos de consulta do Banco de Dados de Documentos

O Banco de Dados de Documentos tem suporte para [consultas](documentdb-sql-query.md) avançadas de documentos JSON armazenados em cada coleção. O exemplo de código a seguir mostra diversas consultas - usando a sintaxe SQL do Banco de Dados de Documentos bem como o LINQ - que podem ser realizadas nos documentos que inserimos na etapa anterior.

Copie e cole o método **ExecuteSimpleQuery** embaixo de seu método **CreateFamilyDocumentIfNotExists**.

	// ADD THIS PART TO YOUR CODE
	private void ExecuteSimpleQuery(string databaseName, string collectionName)
	{
		// Set some common query options
		FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

			// Here we find the Andersen family via its LastName
			IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
					UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
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
					"SELECT * FROM Family WHERE Family.lastName = 'Andersen'",
					queryOptions);

			Console.WriteLine("Running direct SQL query...");
			foreach (Family family in familyQueryInSql)
			{
					Console.WriteLine("\tRead {0}", family);
			}

			Console.WriteLine("Press any key to continue ...");
			Console.ReadKey();
	}

Copie e cole o código a seguir no seu método **GetStartedDemo** embaixo da segunda criação de documento.

	await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

	// ADD THIS PART TO YOUR CODE
	this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

Pressione **F5** para executar seu aplicativo.

Parabéns! Você consultou uma coleção do Banco de Dados de Documentos com sucesso!

O diagrama a seguir ilustra como a sintaxe de consulta do SQL do Banco de Dados de Documentos é chamada em relação à coleção que você criou e a mesma lógica se aplica à consulta LINQ.

![Diagrama que ilustra o escopo e o significado da consulta usada pelo tutorial do NoSQL para criar um aplicativo de console em C#](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

A palavra-chave [FROM](documentdb-sql-query.md#from-clause) é opcional na consulta, pois as consultas do Banco de Dados de Documentos já têm o escopo para uma única coleção. Portanto, "FROM Families f" pode ser trocado por "FROM root r" ou qualquer outra variável de nome que você escolher. O Banco de Dados de Documentos fará com que Families, root ou o nome de variável escolhido por você faça referência à coleção atual, por padrão.

##<a id="ReplaceDocument"></a>Etapa 8: substituir o documento JSON

O Banco de Dados de Documentos dá suporte à substituição documentos JSON.

Copie e cole o método **ReplaceFamilyDocument** embaixo de seu método **ExecuteSimpleQuery**.

	// ADD THIS PART TO YOUR CODE
	private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
	{
			try
			{
					await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
					this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
			}
			catch (DocumentClientException de)
			{
					throw de;
			}
	}

Copie e cole o código a seguir de seu método **GetStartedDemo** embaixo da execução da consulta. Depois de substituir o documento, isso executará a mesma consulta novamente para exibir o documento alterado.

	await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

	this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

	// ADD THIS PART TO YOUR CODE
	// Update the Grade of the Andersen Family child
	andersenFamily.Children[0].Grade = 6;

	await this.ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1", andersenFamily);

	this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

Pressione **F5** para executar seu aplicativo.

Parabéns! Você substituiu um documento do Banco de Dados de Documentos com sucesso!

##<a id="DeleteDocument"></a>Etapa 9: excluir o documento JSON

O Banco de Dados de Documentos dá suporte à exclusão de documentos JSON.

Copie e cole o método **DeleteFamilyDocument** embaixo de seu método **ReplaceFamilyDocument**.

	// ADD THIS PART TO YOUR CODE
	private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
	{
			try
			{
					await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
					Console.WriteLine("Deleted Family {0}", documentName);
			}
			catch (DocumentClientException de)
			{
							throw de;
			}
	}

Copie e cole o código a seguir de seu método **GetStartedDemo** embaixo da segunda execução da consulta.

	await this.ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1", andersenFamily);

	this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

	// ADD THIS PART TO CODE
	await this.DeleteFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1");

Pressione **F5** para executar seu aplicativo.

Parabéns! Você excluiu um documento do Banco de Dados de Documentos com sucesso!

##<a id="DeleteDatabase"></a>Etapa 10: excluir o banco de dados

Excluir o banco de dados criado removerá o banco de dados e todos os recursos filhos (coleções, documentos, etc.).

Copie e cole o código a seguir de seu método **GetStartedDemo** embaixo da exclusão de documento para excluir o banco de dados inteiro e todos os recursos-filhos.

	this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

	await this.DeleteFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1");

	// ADD THIS PART TO CODE
	// Clean up/delete the database
	await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));

Pressione **F5** para executar seu aplicativo.

Parabéns! Você excluiu um banco de dados do Banco de Dados de Documentos com sucesso!

##<a id="Run"></a>Etapa 11: executar o aplicativo de console C# inteiro!

Pressione F5 no Visual Studio para compilar o aplicativo no modo de depuração.

Você deverá ver a saída do aplicativo iniciado. A saída mostrará os resultados das consultas que adicionamos e deverá coincidir com o texto de exemplo abaixo.

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
	Replaced Family Andersen.1
	Press any key to continue ...
	Running LINQ query...
		Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
	Running direct SQL query...
		Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
	Deleted Family Andersen.1
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

<!---HONumber=AcomDC_0511_2016-->