<properties
	pageTitle="Bancos de dados NoSQL — introdução ao SDK do .NET do Banco de Dados de Documentos | Microsoft Azure"
	description="Saiba como criar um banco de dados e configurar uma conta do Banco de Dados de Documentos do Azure. Criar bancos de dados, coleções e armazenar documentos JSON em uma conta do banco de dados NoSQL."
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
	ms.date="09/03/2015"
	ms.author="anhoh"/>

#Introdução ao SDK do .NET do Banco de Dados de Documentos  

Este tutorial mostra como começar a usar o [Banco de Dados de Documentos do Microsoft Azure](https://azure.microsoft.com/services/documentdb/) e o [SDK do .NET do Banco de Dados de Documentos](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/). Você criará um aplicativo de console que cria e consulta recursos no Banco de Dados de Documentos, e grava a saída na janela do console.

O Banco de Dados de Documentos é um serviço de banco de dados de documentos NoSQL, que tem uma [série de APIs e SDKs disponíveis](https://msdn.microsoft.com/library/dn781482.aspx). O código neste artigo é escrito em C# e utiliza o SDK do .NET do Banco de Dados de Documentos, que é empacotado e distribuído como um pacote do NuGet.

Os cenários a seguir são abordados neste artigo:

- Criando e se conectando a uma conta do Banco de Dados de Documentos
- Adicionando o Banco de Dados de Documentos à sua solução do Visual Studio
- Criando bancos de dados
- Criando coleções
- Criando documentos JSON
- Consultando recursos
- Excluindo bancos de dados

Não há tempo para concluir o tutorial e deseja apenas a solução de trabalho? Não se preocupe. A solução completa está disponível em [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started). Consulte [Obter a solução completa](#GetSolution) para as instruções rápidas.

Atualizamos e aperfeiçoamos este tópico com frequência, de acordo com seus comentários. Depois de concluir o tutorial, use os botões de votação nas partes superior e inferior desta página e lembre-se de incluir seus comentários sobre os aperfeiçoamentos que deseja ver. Se quiser que entremos em contato com você diretamente, inclua seu endereço de email em seu comentário.

## Pré-requisitos

Antes de seguir as instruções deste artigo, verifique se você possui o seguinte:

- Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).
- [Visual Studio 2013](http://www.visualstudio.com/) Atualização 4 ou mais recente.

## Etapa 1: Criar uma conta do Banco de Dados de Documentos

Vamos iniciar pela criação de uma conta de Banco de Dados de Documentos. Se você já tiver uma conta, é possível pular para [Configurar sua solução do Visual Studio](#SetupVS).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a> Etapa 2: Configure a sua solução do Visual Studio

1. Abra o **Visual Studio** em seu computador.
2. Selecione **Novo** no menu **Arquivo** e escolha **Projeto**.
3. Na caixa de diálogo **Novo projeto**, selecione **Modelos** / **Visual C#** / **Aplicativo de Console**, nomeie o projeto e, em seguida, clique em **Adicionar**.
4. No **Gerenciador de Soluções**, clique com o botão direito do mouse no seu novo aplicativo de console, que está em sua solução do Visual Studio.
5. Sem sair do menu, clique em **Gerenciar Pacotes NuGet...**
6. No canto esquerdo do painel, na janela **Gerenciar Pacotes NuGet**, clique em **Online** / **nuget.org**.
7. Na caixa de entrada **Pesquisar Online**, procure a **Biblioteca do cliente do Banco de Dados de Documentos**.
8. Nos resultados, encontre **Biblioteca do cliente do Banco de Dados de Documentos do Microsoft Azure** e clique em **Instalar**. A ID do pacote da Biblioteca de cliente do Banco de Dados de Documentos é [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)

Ótimo! Agora você está pronto para começar a trabalhar com o Banco de Dados de Documentos.

##<a id="Connect"></a> Etapa 3: Conectar-se a uma conta do Banco de Dados de Documentos

Começaremos criando uma nova instância da classe [DocumentClient](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.aspx) para estabelecer uma conexão com a nossa conta do Banco de Dados de Documentos. Precisaremos das seguintes referências no início do nosso aplicativo C#:

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

Em seguida, uma instância do **DocumentClient** pode ser criada usando o ponto de extremidade da conta do Banco de Dados de Documentos e a chave de acesso primária ou secundária associada à conta. Adicione essas propriedades à sua classe.

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";

Agora, vamos criar uma nova tarefa assíncrona chamada **GetStartedDemo** em sua classe. Dentro desta nova tarefa, criaremos e configuraremos o seu **DocumentClient**.

	private static async Task GetStartedDemo()
    {
		// Create a new instance of the DocumentClient.
    	var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
	}

Chame a tarefa assíncrona do seu método Main semelhante ao código a seguir.

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

Os valores de EndpointUrl e AuthorizationKey são o URI e a PRIMARY KEY para sua conta do Banco de Dados de Documentos, que pode ser obtida na folha [Chaves](https://portal.azure.com) para a sua conta do Banco de Dados de Documentos.

![Captura de tela da visualização do Portal do Azure mostrando uma conta do Banco de Dados de Documentos com o hub ATIVO realçado, o botão CHAVES realçado na folha da conta do Banco de Dados de Documentos e os valores de URI, de CHAVE PRIMÁRIA e de CHAVE SECUNDÁRIA realçados na folha Chaves][keys]

Essas chaves concedem acesso administrativo à sua conta do Banco de Dados de Documentos e aos recursos presentes nela. O Banco de Dados de Documentos tem suporte também para o uso de chaves de recursos que permitem aos clientes ler, gravar e excluir recursos da conta do Banco de Dados de Documentos, de acordo com as permissões que você tiver concedido, sem a necessidade de uma chave de conta. Para obter mais informações sobre as chaves de recurso, consulte [Permissões](documentdb-resources.md#permissions) e [Exibir, copiar e regenerar chaves de acesso](documentdb-manage-account.md#keys).

Agora que você sabe como conectar uma conta do Banco de Dados de Documentos e criar uma instância da classe **DocumentClient**, vejamos como trabalhar com os recursos do Banco de Dados de Documentos.

## Etapa 4: Criar um banco de dados
Um [banco de dados](documentdb-resources.md#databases) pode ser criado usando o método [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) da classe **DocumentClient**. Um banco de dados é o contêiner lógico de armazenamento de documentos particionado em coleções. Criar um novo banco de dados no seu método **GetStartedDemo** após a criação do **DocumentClient**.

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>Etapa 5: Criar uma coleção  

> [AZURE.WARNING]**CreateDocumentCollectionAsync** criará uma nova coleção de S1, que tem implicações de preços. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).

É possível criar uma [coleção](documentdb-resources.md#collections) usando o método [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) da classe **DocumentClient**. Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada. A coleção recém-criada será mapeada para um [nível de desempenho S1](documentdb-performance-levels.md). O banco de dados criado na etapa anterior possui uma série de propriedades, entre elas, a propriedade [CollectionsLink](https://msdn.microsoft.com/library/microsoft.azure.documents.database.collectionslink.aspx). Com essas informações, podemos criar uma coleção após a nossa criação de banco de dados.

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });

##<a id="CreateDoc"></a>Etapa 6: Criar documentos
Um [documento](documentdb-resources.md#documents) pode ser criado usando o método [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) da classe **DocumentClient**. Os documentos são conteúdo JSON (arbitrário) definido pelo usuário. A coleção criada na etapa anterior tem uma série de propriedades, entre elas a propriedade [DocumentsLink](https://msdn.microsoft.com/library/microsoft.azure.documents.documentcollection.documentslink.aspx). Com essas informações, agora podemos inserir um ou mais documentos. Se já tiver dados que gostaria de armazenar em seu banco de dados, você pode usar a[ferramenta de Migração de Dados](documentdb-import-data.md) do Banco de Dados de Documentos.

Primeiro, precisamos criar uma classe **Pai**, **Filho**, **Pet**, **Endereço** e **Família**. Crie essas classes, adicionando as seguintes subclasses internas.

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

    // Create the Andersen family document.
	Family AndersenFamily = new Family
    {
        Id = "AndersenFamily",
        LastName = "Andersen",
        Parents =  new Parent[] {
            new Parent { FirstName = "Thomas" },
            new Parent { FirstName = "Mary Kay"}
        },
        Children = new Child[] {
            new Child {
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

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, AndersenFamily);

    // Create the WakeField family document.
    Family WakefieldFamily = new Family
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

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, WakefieldFamily);

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

    // Query the documents using DocumentSQL with one join.
    var items = client.CreateDocumentQuery<dynamic>(documentCollection.DocumentsLink,
        "SELECT f.id, c.FirstName AS child " +
        "FROM Families f " +
        "JOIN c IN f.Children");

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

    // Query the documents using LINQ with one join.
    items = client.CreateDocumentQuery<Family>(documentCollection.DocumentsLink)
        .SelectMany(family => family.Children
            .Select(children => new
            {
                family = family.Id,
                child = children.FirstName
            }));

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

O diagrama a seguir ilustra como a sintaxe de consulta do SQL do Banco de Dados de Documentos é chamada em relação à coleção que você criou e a mesma lógica se aplica à consulta LINQ.

![Diagrama que ilustra o escopo e o significado da consulta](./media/documentdb-get-started/collection-documents.png)

A palavra-chave [FROM](documentdb-sql-query.md/#from-clause) é opcional na consulta, pois as consultas do Banco de Dados de Documentos já têm o escopo para uma única coleção. Portanto, "FROM Families f" pode ser trocado por "FROM root r" ou qualquer outra variável de nome que você escolher. O Banco de Dados de Documentos interferirá em Families ou root, ou o nome da variável que você escolheu, fazendo referência à coleção atual por padrão.

##<a id="DeleteDatabase"></a>Etapa 8: Excluir o banco de dados

Excluir o banco de dados criado removerá o banco de dados e todos os recursos filhos (coleções, documentos, etc.). Você pode excluir o banco de dados e o cliente do documento, adicionando o seguinte trecho de código ao final do seu método assíncrono **GetStartedDemo**.

    // Clean up/delete the database
    await client.DeleteDatabaseAsync(database.SelfLink);
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
	{
	  "id": "AndersenFamily",
 	  "child": "Henriette Thaulow"
	}
	{
	  "id": "WakefieldFamily",
	  "child": "Jesse"
	}
	{
	  "id": "WakefieldFamily",
	  "child": "Lisa"
	}
	{ family = AndersenFamily, child = Henriette Thaulow }
	{ family = WakefieldFamily, child = Jesse }
	{ family = WakefieldFamily, child = Lisa }


> [AZURE.NOTE]Se executar o aplicativo várias vezes sem remover o banco de dados, você poderá executar o problema da criação de um novo banco de dados com uma ID que já está em uso. Para evitar isso, é possível verificar se já existe um banco de dados, a coleção ou o documento com a mesma ID. Para obter uma referência sobre como isso pode ser obtido, visite a nossa [página GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started).

Parabéns! Você criou seu primeiro aplicativo do Banco de Dados de Documentos!

##<a id="GetSolution"></a> Obter a solução completa
Para criar a solução de Introdução que contém todos os exemplos neste artigo, você precisará do seguinte:

-   [Conta de Banco de Dados de Documentos][documentdb-create-account].
-   A solução [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) disponível no GitHub.

Para restaurar as referências do Banco de Dados de Documentos do SDK do .NET no Visual Studio 2013, clique com o botão direito do mouse na solução **GetStarted** no Gerenciador de Soluções e, em seguida, clique em **Habilitar pacote de restauração NuGet**. Em seguida, no arquivo App.config, atualize os valores EndpointUrl e AuthorizationKey conforme descrito em [Conectar-se a uma conta do Banco de Dados de Documentos](#Connect).

## Próximas etapas

-   Quer um exemplo mais complexo do ASP.NET MVC? Consulte [Criar um aplicativo Web com ASP.NET MVC usando o Banco de Dados de Documentos](documentdb-dotnet-application.md).
-	Saiba como [monitorar uma conta do Banco de Dados de Documentos](documentdb-monitor-accounts.md).
-	Executar consultas em nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
-	Saiba mais sobre o modelo de programação na seção Desenvolvimento da [Página de documentação do Banco de Dados de Documentos](../../services/documentdb/).

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-get-started/keys.png
 

<!---HONumber=Sept15_HO3-->