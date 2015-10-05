<properties
	pageTitle="Bancos de dados NoSQL — introdução ao SDK do .NET do Banco de Dados de Documentos | Microsoft Azure"
	description="Saiba como criar um banco de dados e configurar uma conta do Banco de Dados de Documentos do Azure. Crie um bancos de dados, uma coleção e armazene documentos JSON em uma conta do banco de dados NoSQL."
	keywords="Create a database, create database, nosql database, nosql databases, nuget, documentdb, azure, Microsoft azure"
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
	ms.date="09/16/2015"
	ms.author="anhoh"/>

#Introdução ao SDK do .NET do Banco de Dados de Documentos  

Bem-vindo à Introdução ao SDK do .NET Banco de Dados de Documentos! Após seguir este tutorial, você terá um aplicativo de console que cria e consulta recursos de Banco de Dados de Documentos.

Abordaremos:

- Criando e se conectando a uma conta do Banco de Dados de Documentos
- Configurar a sua Solução do Visual Studio
- Criar um banco de dados
- Criar uma coleção
- Criando documentos JSON
- Consultar a coleção
- Excluir o banco de dados

Você não tem tempo? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started). Consulte [Obter a solução completa](#GetSolution) para as instruções rápidas.

Em seguida, use os botões de votação na parte superior ou inferior desta página para nos enviar comentários. Se quiser que entremos em contato com você diretamente, inclua seu endereço de email em seu comentário.

Agora vamos começar!

## Pré-requisitos

Certifique-se que você tem o seguinte:

- Uma conta ativa do Azure. Se não tem uma, você pode se inscrever para uma [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).
- [Visual Studio 2013 / Visual Studio 2015](http://www.visualstudio.com/).

## Etapa 1: Criar uma conta do Banco de Dados de Documentos

Vamos criar uma conta de Banco de Dados de Documentos. Se você já tem uma conta que deseja usar, você pode pular para [Configurar sua Solução do Visual Studio](#SetupVS).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a> Etapa 2: Configure a sua solução do Visual Studio

1. Abra o **Visual Studio** em seu computador.
2. No menu **Arquivo**, selecione **Novo** e depois **Projeto**.
3. Na caixa de diálogo **Novo projeto**, selecione **Modelos** / **Visual C#** / **Aplicativo de Console**, nomeie o projeto e, em seguida, clique em **OK**.
4. No **Gerenciador de Soluções**, clique com o botão direito do mouse no seu novo aplicativo de console, que está em sua solução do Visual Studio.
5. Sem sair do menu, clique em **Gerenciar Pacotes NuGet...**
6. No canto esquerdo do painel, na janela **Gerenciar Pacotes NuGet**, clique em **Online** / **nuget.org**.
7. Na caixa de entrada **Pesquisar Online**, procure a **Biblioteca do cliente do Banco de Dados de Documentos**.
8. Nos resultados, encontre **Biblioteca do cliente do Banco de Dados de Documentos do Microsoft Azure** e clique em **Instalar**. A ID do pacote da Biblioteca de cliente do Banco de Dados de Documentos é [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)

Ótimo! Agora que a instalação está concluída, vamos começar a escrever algum código.

##<a id="Connect"></a> Etapa 3: Conectar-se a uma conta do Banco de Dados de Documentos

Primeiro, adicione essas referências para o início de seu aplicativo C#, no arquivo Program.cs:

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

Em seguida, salve o ponto de extremidade de conta do Banco de Dados de Documentos e uma das duas chaves de acesso, a primária ou a secundária, que pode ser encontrada no [Portal de Visualização do Azure](https://portal.azure.com).

![Captura de tela da visualização do Portal do Azure mostrando uma conta do Banco de Dados de Documentos com o hub ATIVO realçado, o botão CHAVES realçado na folha da conta do Banco de Dados de Documentos e os valores de URI, de CHAVE PRIMÁRIA e de CHAVE SECUNDÁRIA realçados na folha Chaves][keys]

    private const string EndpointUrl = "<your endpoint URI>";
    private const string AuthorizationKey = "<your key>";

Iniciaremos o aplicativo de demonstração de introdução criando uma nova instância do **DocumentClient**. Crie uma nova tarefa assíncrona chamada **GetStartedDemo** e crie uma instância do nosso novo **DocumentClient**.

	private static async Task GetStartedDemo()
    {
		// Create a new instance of the DocumentClient
    	var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
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

> [AZURE.WARNING]Nunca armazene credenciais no código-fonte. Para simplificar este exemplo, as credenciais são mostradas no código-fonte. Consulte os [Sites do Azure: Como as cadeias de caracteres de Aplicativo e as cadeias de caracteres de Conexão funcionam](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) para obter informações sobre como armazenar credenciais em um ambiente de produção. Veja o nosso aplicativo de exemplo em [GitHub](https://github.com/Azure/azure-documentdb-net/blob/master/tutorials/get-started/src/Program.cs) para obter um exemplo de como armazenar credenciais fora do código-fonte.

Agora que você sabe como conectar uma conta do Banco de Dados de Documentos e criar uma instância da classe **DocumentClient**, vejamos como trabalhar com os recursos do Banco de Dados de Documentos.

## Etapa 4: Criar um banco de dados
Um [banco de dados](documentdb-resources.md#databases) pode ser criado usando o método [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) da classe **DocumentClient**. Um banco de dados é o contêiner lógico de armazenamento de documentos particionado em coleções. Criar um novo banco de dados no seu método **GetStartedDemo** após a criação do **DocumentClient**.

	// Check to verify a database with the id=FamilyRegistry does not exist
	Database database = client.CreateDatabaseQuery().Where(db => db.Id == "FamilyRegistry").AsEnumerable().FirstOrDefault();

	// If the database does not exist, create a new database
    if (database == null)
    {
    	database = await client.CreateDatabaseAsync(
    		new Database
            {
            	Id = "FamilyRegistry"
            });

		// Write the new database's id to the console
		Console.WriteLine(database.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
	}

##<a id="CreateColl"></a>Etapa 5: Criar uma coleção  

> [AZURE.WARNING]**CreateDocumentCollectionAsync** criará uma nova coleção de S1, que tem implicações de preços. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).

É possível criar uma [coleção](documentdb-resources.md#collections) usando o método [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) da classe **DocumentClient**. Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada. A coleção recém-criada será mapeada para um [nível de desempenho S1](documentdb-performance-levels.md). Crie uma nova coleção chamada **FamilyCollection** após a criação de seu banco de dados no método **GetStartedDemo**.

    // Check to verify a document collection with the id=FamilyCollection does not exist
    DocumentCollection documentCollection = client.CreateDocumentCollectionQuery("dbs/" + database.Id).Where(c => c.Id == "FamilyCollection").AsEnumerable().FirstOrDefault();

	// If the document collection does not exist, create a new collection
    if (documentCollection == null)
    {
    	documentCollection = await client.CreateDocumentCollectionAsync("dbs/" + database.Id,
        	new DocumentCollection
            {
            	Id = "FamilyCollection"
            });

		// Write the new collection's id to the console
		Console.WriteLine(documentCollection.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
	}

##<a id="CreateDoc"></a>Etapa 6: Criar documentos
Um [documento](documentdb-resources.md#documents) pode ser criado usando o método [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) da classe **DocumentClient**. Os documentos são conteúdo JSON (arbitrário) definido pelo usuário. Agora podemos inserir um ou mais documentos. Se já tiver dados que gostaria de armazenar em seu banco de dados, você pode usar a[ferramenta de Migração de Dados](documentdb-import-data.md) do Banco de Dados de Documentos.

Primeiro, precisamos criar uma classe **Pai**, **Filho**, **Pet**, **Endereço** e **Família**. Crie essas classes, adicionando as seguintes subclasses internas após o método **GetStartedDemo**.

    internal sealed class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    internal sealed class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    internal sealed class Pet
    {
        public string GivenName { get; set; }
    }

    internal sealed class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }

    internal sealed class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
    }

Em seguida, crie os documentos dentro de seu método assíncrono **GetStartedDemo**.

    // Check to verify a document with the id=AndersenFamily does not exist
    Document document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "AndersenFamily").AsEnumerable().FirstOrDefault();

	// If the document does not exist, create a new document
	if (document == null)
	{
	    // Create the Andersen Family document
	    Family andersonFamily = new Family
	    {
	        Id = "AndersenFamily",
	        LastName = "Andersen",
	        Parents = new Parent[] {
	            new Parent { FirstName = "Thomas" },
	            new Parent { FirstName = "Mary Kay"}
	        },
	        Children = new Child[] {
	            new Child
	            { 
	                FirstName = "Henriette Thaulow", 
	                Gender = "female", 
	                Grade = 5, 
	                Pets = new Pet[] {
	                    new Pet { GivenName = "Fluffy" } 
	                }
	            } 
	        },
	        Address = new Address { State = "WA", County = "King", City = "Seattle" },
	        IsRegistered = true
	    };
	
	    await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, andersonFamily);
	}

    // Check to verify a document with the id=AndersenFamily does not exist
    document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "WakefieldFamily").AsEnumerable().FirstOrDefault();

    if (document == null)
    {
        // Create the WakeField document
        Family wakefieldFamily = new Family
        {
            Id = "WakefieldFamily",
            Parents = new Parent[] {
                new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
                new Parent { FamilyName= "Miller", FirstName= "Ben" }
            },
            Children = new Child[] {
                new Child {
                    FamilyName= "Merriam", 
                    FirstName= "Jesse", 
                    Gender= "female", 
                    Grade= 8,
                    Pets= new Pet[] {
                        new Pet { GivenName= "Goofy" },
                        new Pet { GivenName= "Shadow" }
                    }
                },
                new Child {
                    FamilyName= "Miller", 
                    FirstName= "Lisa", 
                    Gender= "female", 
                    Grade= 1
                }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
        };

        await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, wakefieldFamily);
	}

Agora, você criou o seguinte banco de dados, a coleta e os documentos em sua conta do Banco de Dados de Documentos.

![Diagrama que ilustra a relação hierárquica entre a conta, o banco de dados, a coleção e os documentos](./media/documentdb-get-started/account-database.png)

##<a id="Query"></a>Etapa 7: Recursos de consulta do Banco de Dados de Documentos

O Banco de Dados de Documentos tem suporte para [consultas](documentdb-sql-query.md) avançadas de documentos JSON armazenados em cada coleção. O exemplo de código a seguir mostra diversas consultas - usando a sintaxe SQL do Banco de Dados de Documentos bem como o LINQ - que podem ser realizadas nos documentos que inserimos na etapa anterior. Adicione essas consultas ao método assíncrono **GetStartedDemo**.

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = "AndersenFamily"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family.
    families =
        from f in client.CreateDocumentQuery(documentCollection.DocumentsLink)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family.
    families = client.CreateDocumentQuery(documentCollection.DocumentsLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

O diagrama a seguir ilustra como a sintaxe de consulta do SQL do Banco de Dados de Documentos é chamada em relação à coleção que você criou e a mesma lógica se aplica à consulta LINQ.

![Diagrama que ilustra o escopo e o significado da consulta](./media/documentdb-get-started/collection-documents.png)

A palavra-chave [FROM](documentdb-sql-query.md#from-clause) é opcional na consulta, pois as consultas do Banco de Dados de Documentos já têm o escopo para uma única coleção. Portanto, "FROM Families f" pode ser trocado por "FROM root r" ou qualquer outra variável de nome que você escolher. O Banco de Dados de Documentos fará com que Families, root ou o nome de variável escolhido por você faça referência à coleção atual, por padrão.

##<a id="DeleteDatabase"></a>Etapa 8: Excluir o banco de dados

Excluir o banco de dados criado removerá o banco de dados e todos os recursos filhos (coleções, documentos, etc.). Você pode excluir o banco de dados e o cliente do documento, adicionando o seguinte trecho de código ao final do seu método assíncrono **GetStartedDemo**.

    // Clean up/delete the database
    await client.DeleteDatabaseAsync("dbs/" + database.Id);
	client.Dispose();

##<a id="Run"></a>Etapa 9: Executar o aplicativo!

Agora você está pronto para executar o aplicativo. No final do método **Main**, adicione a seguinte linha de código, o que permitirá que você leia o console de saída antes de o aplicativo terminar a execução.

	Console.ReadLine();

Agora, pressione F5 no Visual Studio para compilar o aplicativo no modo de depuração.

Agora você verá a saída do aplicativo iniciado. A saída mostrará os resultados das consultas que adicionamos e deverá coincidir com o texto de exemplo abaixo.

	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
    	{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from SQL
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ query

Parabéns! Você criou seu primeiro aplicativo do Banco de Dados de Documentos!

##<a id="GetSolution"></a> Obter a solução completa
Para criar a solução de Introdução que contém todos os exemplos neste artigo, você precisará do seguinte:

-   [Conta de Banco de Dados de Documentos][documentdb-create-account].
-   A solução [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) disponível no GitHub.

Para restaurar as referências do Banco de Dados de Documentos do SDK do .NET no Visual Studio, clique com o botão direito do mouse na solução **GetStarted** no Gerenciador de Soluções e, em seguida, clique em **Habilitar Pacote de Restauração NuGet**. Em seguida, no arquivo App.config, atualize os valores EndpointUrl e AuthorizationKey conforme descrito em [Conectar-se a uma conta do Banco de Dados de Documentos](#Connect).

## Próximas etapas

-   Quer um exemplo mais complexo do ASP.NET MVC? Consulte [Criar um aplicativo Web com ASP.NET MVC usando o Banco de Dados de Documentos](documentdb-dotnet-application.md).
-	Saiba como [monitorar uma conta do Banco de Dados de Documentos](documentdb-monitor-accounts.md).
-	Executar consultas em nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
-	Saiba mais sobre o modelo de programação na seção Desenvolvimento da [Página de documentação do Banco de Dados de Documentos](../../services/documentdb/).

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-get-started/keys.png
 

<!---HONumber=Sept15_HO4-->