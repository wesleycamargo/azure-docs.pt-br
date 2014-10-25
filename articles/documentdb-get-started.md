<properties title="Get started with a DocumentDB account" pageTitle="Get started with a DocumentDB account | Azure" description="Learn how to create and configure an Azure DocumentDB account, create databases, create collections, and store JSON documents within the account." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="bradsev" manager="paulettm" editor="cgronlun" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev"></tags>

# Introdução à conta do Banco de Dados de Documentos

Este guia mostra como começar a usar o **Banco de Dados de Documentos do Azure (visualização)**. Os exemplos são escritos em código C\# e utilizam o SDK .NET do Banco de Dados de Documentos. Os cenários abordados incluem a criação e configuração de uma conta do Banco de Dados de Documentos, a criação de bancos de dados, a criação de coleções e o armazenamento de documentos JSON na conta. Para obter mais informações sobre o uso do Banco de Dados de Documentos do Azure, consulte a seção Próximas etapas.

Para usar este guia de introdução, você precisa ter uma conta do Banco de Dados de Documentos e a chave de acesso (primária ou secundária) da conta. Para obter mais informações, consulte:

-   [Criar uma conta do Banco de Dados de Documentos][Criar uma conta do Banco de Dados de Documentos]

## Sumário

-   [Conectar-se a uma conta do Banco de Dados de Documentos][Conectar-se a uma conta do Banco de Dados de Documentos]
-   [Criar um banco de dados][Criar um banco de dados]
-   [Criar uma coleção][Criar uma coleção]
-   [Criar documentos][Criar documentos]
-   [Consultar recursos do Banco de Dados de Documentos][Consultar recursos do Banco de Dados de Documentos]
-   [Próximas etapas][Próximas etapas]

## <span id="Connect"></span></a>Conectar-se a uma conta do Banco de Dados de Documentos

Há diversos SDKs e APIs disponíveis para trabalhar de forma programática com o Banco de Dados de Documentos. Os exemplos abaixo são escritos em código C\# e utilizam o SDK .NET do Banco de Dados de Documentos.

Começaremos criando um DocumentClient para estabelecer uma conexão com nossa conta do Banco de Dados de Documentos. Nós precisaremos das seguintes referências no aplicativo C\#:

    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Microsoft.Azure.Documents;  

Uma instância do DocumentClient pode ser criada usando o ponto de extremidade da conta do Banco de Dados de Documentos e a chave de acesso primária ou secundária associada à conta.

O ponto de extremidade e as chaves do Banco de Dados de Documentos podem ser obtidos da lâmina do portal de visualização de gerenciamento do Azure para sua instância do Banco de Dados de Documentos.

![][]

> Observe que as chaves de acesso do Banco de Dados de Documentos disponíveis na lâmina Chaves concedem acesso administrativo à sua conta do Banco de Dados de Documentos e aos recursos contidos nela. O Banco de Dados de Documentos tem suporte também para o uso de chaves de recursos que permitem aos clientes ler, gravar e excluir recursos da conta do Banco de Dados de Documentos, de acordo com as permissões que você tiver concedido, sem a necessidade de uma chave de conta.

Crie o cliente usando código semelhante ao exemplo a seguir.

    private static string endpointUrl = "<your endpoint URI>";
    private static string authorizationKey = "<your key>";

    //Create a new instance of the DocumentClient
    var client = new DocumentClient(new Uri(endpointUrl), authorizationKey);  

**Aviso:** Nunca armazene credenciais no código-fonte. Para manter esse exemplo simples, elas são mostradas no código-fonte. Consulte [Sites do Microsoft Azure na Web: Como as cadeias de caracteres de aplicativo e a cadeia de caracteres de conexão trabalha][Sites do Microsoft Azure na Web: Como as cadeias de caracteres de aplicativo e a cadeia de caracteres de conexão trabalha] para obter informações sobre como armazenar credenciais.

Agora que você sabe como conectar uma conta do Banco de Dados de Documentos e criar uma instância do DocumentClient, vejamos como trabalhar com os recursos do Banco de Dados de Documentos.

## <span id="CreateDB"></span></a>Criar um banco de dados

Utilizando o SDK .NET, é possível criar um banco de dados do Banco de Dados de Documentos por meio do método CreateDatabaseAsync de um DocumentClient.

    //Create a Database
     Database database = await client.CreateDatabaseAsync(
        new Database
        {
        Id = "FamilyRegistry"
        });

## <span id="CreateColl"></span></a>Criar uma coleção

Utilizando o SDK .NET, é possível criar uma coleção do Banco de Dados de Documentos por meio do método CreateDocumentCollectionAsync de um DocumentClient. O banco de dados criado na etapa anterior tem uma série de propriedades, entre elas a propriedade SelfLink. Com essas informações, podemos criar uma coleção.

        //Create a document collection 
    documentCollection = new DocumentCollection
        {
            Id = "FamilyCollection"
        };

        documentCollection = await client.CreateDocumentCollectionAsync(database.SelfLink,documentCollection); 

## <span id="CreateDoc"></span></a>Criar documentos

Utilizando o SDK .NET, é possível criar um documento do Banco de Dados de Documentos por meio do método CreateDocumentAsync de um DocumentClient. A coleção criada na etapa anterior tem uma série de propriedades, entre elas a propriedade DocumentsLink. Com essas informações, podemos inserir um documento ou mais. Para este exemplo, presumiremos que nós temos uma classe Família que descreve os atributos de uma família como nome, gênero e idade.

    private static async Task CreateDocuments(string    colSelfLink)
    {
        Family AndersonFamily = new Family
        {
            Id = "AndersenFamily",
            LastName = "Andersen",
            Parents =  new Parent[] {
                new Parent { FirstName = "Thomas" },
                new Parent { FirstName = "Mary Kay"}
            },
            Children = new Child[] {
                new Child
                { 
                    FirstName = "Henriette Thaulow", 
                    Gender = "female", 
                    Grade = 5, 
                    Pets = new [] {
                        new Pet { GivenName = "Fluffy" } 
                    }
                } 
            },
            Address = new Address { State = "WA", County = "King", City = "Seattle" },
            IsRegistered = true
        };

        await client.CreateDocumentAsync(colSelfLink, AndersonFamily);

        Family WakefieldFamily = new Family
        {
            Id = "WakefieldFamily",
            Parents = new [] {
                new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
                new Parent { FamilyName= "Miller", FirstName= "Ben" }
            },
            Children = new Child[] {
                new Child
                {
                    FamilyName= "Merriam", 
                    FirstName= "Jesse", 
                    Gender= "female", 
                    Grade= 8,
                    Pets= new Pet[] {
                        new Pet { GivenName= "Goofy" },
                        new Pet { GivenName= "Shadow" }
                    }
                },
                new Child
                {
                    FamilyName= "Miller", 
                    FirstName= "Lisa", 
                    Gender= "female", 
                    Grade= 1
                }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
        };

        await client.CreateDocumentAsync(colSelfLink, WakefieldFamily);

}

## <span id="Query"></span></a>Consultar recursos do Banco de Dados de Documentos

O Banco de Dados de Documentos tem suporte para consultas avançadas de documentos JSON armazenados em cada coleção. O exemplo de código abaixo mostra diversas consultas - usando a sintaxe SQL do Banco de Dados de Documentos bem como o LINQ - que podem ser realizadas nos documentos que inserimos na etapa anterior.

    //
    //Querying the documents using DocumentDB SQL for the Andersen family
    //
    foreach (var family in client.CreateDocumentQuery(collectionLink, 
    "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
    Console.WriteLine("\tRead {0} from SQL", family);
    }

    //
    //Querying the documents using LINQ for the Andersen family
    //
    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
     Console.WriteLine("\tRead {0} from LINQ", family);
    }

    //
    //Querying the documents using LINQ lambdas for the Andersen family
    //
    foreach (var family in client.CreateDocumentQuery(collectionLink)
    .Where(f => f.Id == "AndersenFamily")
    .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    //
    //DocumentDB SQL -  using <> interchangably with != for "not equals"
    //
    families = client.CreateDocumentQuery<Family>(colSelfLink, "SELECT * FROM Families f WHERE f.id <> 'AndersenFamily'");

    //   
    // LINQ - combine equality and inequality
    //
    families = from f in client.CreateDocumentQuery<Family>(colSelfLink)
           where f.Id == "Wakefield" && f.Address.City != "NY"
           select f; 

## <span id="NextSteps"></span></a>Próximas etapas

-   Saiba como [monitorar uma conta do Banco de Dados de Documentos][monitorar uma conta do Banco de Dados de Documentos].
-   Para obter detalhes sobre o modelo de programação, consulte a seção de Desenvolvimento na [página de documentação do Banco de Dados de Documentos][página de documentação do Banco de Dados de Documentos]

  [Criar uma conta do Banco de Dados de Documentos]: ../documentdb-create-account/
  [Conectar-se a uma conta do Banco de Dados de Documentos]: #Connect
  [Criar um banco de dados]: #CreateDB
  [Criar uma coleção]: #CreateColl
  [Criar documentos]: #CreateDoc
  [Consultar recursos do Banco de Dados de Documentos]: #Query
  [Próximas etapas]: #NextSteps
  []: ./media/documentdb-get-started/gs1.png
  [Sites do Microsoft Azure na Web: Como as cadeias de caracteres de aplicativo e a cadeia de caracteres de conexão trabalha]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [monitorar uma conta do Banco de Dados de Documentos]: http://go.microsoft.com/fwlink/p/?LinkId=402378
  [página de documentação do Banco de Dados de Documentos]: http://go.microsoft.com/fwlink/p/?LinkID=402319
