<properties 
	pageTitle="Introdução ao SDK do .NET do Banco de Dados de Documentos | Azure" 
	description="Aprenda a criar e configurar uma conta do Banco de Dados de Documentos Azure, criar bancos de dados, criar coleções e armazenar documentos JSON em uma conta do banco de dados de documentos NoSQL." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/12/2014" 
	ms.author="mimig"/>

#Introdução ao SDK do .NET do Banco de Dados de Documentos  

Este guia mostra como começar a usar o[**Banco de Dados de Documentos do Microsoft Azure (visualização)**](https://portal.azure.com/#gallery/Microsoft.DocumentDB) e o SDK do .NET dos Banco de Dados de Documentos. O Banco de Dados de Documentos é um serviço de banco de dados de documentos NoSQL, que tem uma série de APIs e SDKs disponíveis. Os exemplos de código neste artigo são escritos em C# e utilizam o [SDK do .NET do Banco de Dados de Documentos](http://go.microsoft.com/fwlink/p/?linkid=402989), que é empacotado e distribuído como um pacote do NuGet. 

Os cenários abordados neste artigo incluem a criação e configuração de uma conta do Banco de Dados de Documentos, a criação de bancos de dados, a criação de coleções e o armazenamento de documentos JSON na conta. Cada um desses exemplos fazem parte de uma solução completa disponível no [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started). Você pode [baixar a solução](#GetSolution) para exibir o código de exemplo em contexto ou você pode simplesmente analisar os exemplos neste artigo.

##Sumário

-	[Conectar-se a uma conta do Banco de Dados de Documentos](#Connect)
-	[Criar um banco de dados](#CreateDB)
-	[Criar uma coleção](#CreateColl)
-	[Criar documentos](#CreateDoc)
-	[Consultar recursos do Banco de Dados de Documentos](#Query)
-	[Obter a solução completa](#GetSolution)
-	[Próximas etapas](#NextSteps)

##<a id="Connect"></a>Conectar-se a uma conta do Banco de Dados de Documentos

Começaremos criando uma nova instância da classe **DocumentClient**para estabelecer uma conexão com nossa conta do Banco de Dados de Documentos.   Nós precisaremos das seguintes referências no aplicativo C#:  

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
 
Uma instância do **DocumentClient** pode ser criada usando o ponto de extremidade da conta do Banco de Dados de Documentos e a chave de acesso primária ou secundária associada à conta.  

O ponto de extremidade e as chaves do Banco de Dados de Documentos podem ser obtidos da lâmina do [portal de visualização de gerenciamento do Azure](https://portal.azure.com/#gallery/Microsoft.DocumentDB) para sua instância do Banco de Dados de Documentos. 

![][1]
 
>Observe que as chaves de acesso do Banco de Dados de Documentos disponíveis na lâmina Chaves concedem acesso administrativo à sua conta do Banco de Dados de Documentos e aos recursos contidos nela.  O Banco de Dados de Documentos tem suporte também para o uso de chaves de recursos que permitem aos clientes ler, gravar e excluir recursos da conta do Banco de Dados de Documentos, de acordo com as permissões que você tiver concedido, sem a necessidade de uma chave de conta.    

Crie o cliente usando código semelhante ao exemplo a seguir.  

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";
    
    // Create a new instance of the DocumentClient.
    var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);  

**Aviso:** nunca armazenar credenciais no código-fonte. Para manter esse exemplo, as credenciais são mostradas no código-fonte. Consulte [Sites do Windows Azure: como as cadeias de caracteres de aplicativo e a cadeias de caracteres de conexão trabalham](http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)para obter informações sobre como armazenar credenciais.

Agora que você sabe como conectar uma conta do Banco de Dados de Documentos e criar uma instância do **DocumentClient**, vejamos como trabalhar com os recursos do Banco de Dados de Documentos.  

##<a id="CreateDB"></a>Criar um banco de dados
Utilizando o SDK do .NET do Banco de Dados de Documentos, é possível criar um banco de dados do Banco de Dados de Documentos por meio do método **CreateDatabaseAsync** de um DocumentClient.  

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>Criar uma coleção  

Utilizando o SDK do .NET do Banco de Dados de Documentos, é possível criar uma coleção do Banco de Dados de Documentos por meio do método **CreateDatabaseAsync** de um DocumentClient.  O banco de dados criado na etapa anterior possui uma série de propriedades, entre elas, a propriedade **CollectionsLink**.  Com essas informações, podemos criar uma coleção.  

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });
    
##<a id="CreateDoc"></a>Criar documentos	
Utilizando o SDK do .NET do Banco de Dados de Documentos, é possível criar um documento do Banco de Dados de Documentos por meio do método **CreateDatabaseAsync** de um DocumentClient.  A coleção criada na etapa anterior tem uma série de propriedades, entre elas a propriedade **DocumentsLink**.  Com essas informações, agora podemos inserir um ou mais documentos.  Para este exemplo, presumiremos que nós temos uma classe Família que descreve os atributos de uma família como nome, gênero e idade.  

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
 

##<a id="Query"></a>Consultar recursos do Banco de Dados de Documentos
O Banco de Dados de Documentos tem suporte para consultas avançadas de documentos JSON armazenados em cada coleção.  O exemplo de código a seguir mostra diversas consultas - usando a sintaxe SQL do Banco de Dados de Documentos bem como o LINQ - que podem ser realizadas nos documentos que inserimos na etapa anterior.  

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = \"AndersenFamily\"");

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

##<a id="GetSolution"></a>Obter a solução completa
Para criar a solução de Introdução que contém todos os exemplos neste artigo, você precisará do seguinte:

-   [Conta do Banco de Dados de Documentos][documentdb-create-account].
-   A solução de [Introdução ao](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) disponível no GitHub. 

Para restaurar as referências do Banco de Dados de Documentos do SDK do .NET no Visual Studio 2013, clique com o botão direito na solução de Introdução no Gerenciador de Soluções e, em seguida, clique em Habilitar pacote de restauração NuGet, que irá restaurar as referências. 

##<a id="NextSteps"></a>Próximas etapas
-	Saiba como [monitorar uma conta do Banco de Dados de Documentos](http://go.microsoft.com/fwlink/p/?LinkId=402378).
-	Para obter detalhes sobre o modelo de programação, consulte a seção Desenvolvimento na [página de documentação do Banco de Dados de Documentos](http://go.microsoft.com/fwlink/p/?LinkID=402319).


[Conectar-se a uma conta do Banco de Dados de Documentos]: #Connect
[Criar um banco de dados]: #CreateDB
[Criar uma coleção]: #CreateColl
[Criar documentos]: #CreateDoc
[Consultar recursos do Banco de Dados de Documentos]: #Query
[Próximas etapas]: #NextSteps
[doc-landing-page]: ../documentation/services/documentdb/
[documentdb-create-account]: ../documentdb-create-account/
[documentdb-manage]: ../documentdb-manage/

[1]: ./media/documentdb-get-started/gs1.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
