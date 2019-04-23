---
title: Criar um aplicativo de console .NET para gerenciar dados na conta da API do SQL do Azure Cosmos DB
description: Um tutorial que cria um banco de dados online e um aplicativo de console C# usando a API do SQL.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: sngun
ms.openlocfilehash: a8d144b2cb8ee18c69dc4c4768b09422d44bade2
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617304"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Criar um aplicativo de console .NET para gerenciar dados na conta da API do SQL do Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (versão prévia)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (versão prévia)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Bem-vindo ao tutorial de introdução da API do SQL do Azure Cosmos DB. Após a conclusão deste tutorial, você terá um aplicativo de console que cria e consulta recursos do Azure Cosmos DB.

Este tutorial mostra como:

> [!div class="checklist"]
>
> - Criar uma conta do Azure Cosmos DB e conectar-se a ela
> - Configurar uma solução do Visual Studio
> - Criar um banco de dados
> - Criar uma coleção
> - Criar documentos JSON
> - Consultar a coleção
> - Atualizar um documento JSON
> - Excluir um documento
> - Excluir o banco de dados

## <a name="prerequisites"></a>Pré-requisitos

Visual Studio 2017 com o fluxo de trabalho de desenvolvimento do Azure instalado:
- Você pode baixar e usar o [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **gratuito**. Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio. 

Uma assinatura do Azure ou uma conta de avaliação gratuita do Cosmos DB:
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  
  
- Se você estiver usando o Emulador do Azure Cosmos DB, siga as etapas do [Emulador do Azure Cosmos DB](local-emulator.md) para configurar o emulador. Em seguida, inicie o tutorial em [Configurar a solução do Visual Studio](#SetupVS).
  
## <a name="get-the-completed-solution"></a>Obter a solução concluída

Se você não tiver tempo para concluir o tutorial ou apenas desejar obter os exemplos de código, baixe a solução completa no [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). 

Para executar a solução completa baixada: 

1. Verifique se você tem os [pré-requisitos](#prerequisites) instalados. 
1. Abra o arquivo de solução *GetStarted.sln* baixado no Visual Studio.
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **GetStarted** e selecione **Gerenciar Pacotes NuGet**.
1. Na guia **NuGet**, selecione **Restaurar** para restaurar as referências ao SDK do .NET do Azure Cosmos DB.
1. No arquivo *App.config*, atualize os valores `EndpointUrl` e `PrimaryKey`, conforme descrito na seção [Conectar-se à conta do Azure Cosmos DB](#Connect).
1. Selecione **Depurar** > **Iniciar Sem Depuração** ou pressione **Ctrl**+**F5** para compilar e executar o aplicativo.

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Siga estas instruções para criar uma conta do Azure Cosmos DB no portal do Azure. Se você já tiver uma conta do Azure Cosmos DB para usar, vá para [Configurar a solução do Visual Studio](#SetupVS). 

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Configurar a solução do Visual Studio

1. No Visual Studio 2017, selecione **Arquivo** > **Novo** > **Projeto**.
   
1. Na caixa de diálogo **Novo Projeto**, selecione **Visual C#** > **Aplicativo de Console (.NET Framework)**, nomeie o projeto *AzureCosmosDBApp* e, em seguida, selecione **OK**.
   
   ![Captura de tela da janela Novo Projeto](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
   
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **AzureCosmosDBApp** e selecione **Gerenciar Pacotes NuGet**.
   
   ![Menu de contexto do projeto](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
   
1. Na guia **NuGet**, selecione **Procurar** e insira *azure documentdb* na caixa de pesquisa.
   
1. Localize e selecione **Microsoft.Azure.DocumentDB** e selecione **Instalar** se ele ainda não estiver instalado.
   
   A ID do pacote para a Biblioteca de Clientes do API do SQL do Azure Cosmos DB é [Biblioteca de Clientes do Microsoft Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/).
   
   ![Captura de tela do menu do NuGet para localizar o SDK do cliente do Azure Cosmos DB](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)
   
   Se você receber uma mensagem sobre a visualização das alterações na solução, selecione **OK**. Se você receber uma mensagem sobre a aceitação da licença, selecione **Aceito**.

## <a id="Connect"></a>Conectar-se a uma conta do Azure Cosmos DB

Agora, comece a escrever algum código. O arquivo *Project.cs* concluído para este tutorial está no [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

1. No **Gerenciador de Soluções**, selecione *Program.cs* e, no editor de códigos, adicione as seguintes referências ao início do arquivo:
   
   ```csharp
   using System.Net;
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Newtonsoft.Json;
   ```
   
1. Em seguida, adicione as duas constantes a seguir e a variável `client` a `public class Program`.
   
   ```csharp
   
   public class Program
   {
      private const string EndpointUrl = "<your endpoint URL>";
      private const string PrimaryKey = "<your primary key>";
      private DocumentClient client;
   ```
   
1. A chave primária e a URL de ponto de extremidade permitem que o aplicativo se conecte à sua conta do Azure Cosmos DB e à conta do Azure Cosmos DB para a confiar na conexão. Copie as chaves do [portal do Azure](https://portal.azure.com) e cole-as no código. 

   
   1. Na navegação esquerda de sua conta do Azure Cosmos DB, selecione **Chaves**.
      
      ![Exibir e copiar chaves de acesso no portal do Azure](./media/sql-api-get-started/nosql-tutorial-keys.png)
      
   1. Em **Chaves de Leitura/Gravação**, copie o valor do **URI** usando o botão Copiar à direita e, em seguida, cole-o em `<your endpoint URL>` em *Program.cs*. Por exemplo:  
      
      `private const string EndpointUrl = "https://mysqlapicosmosdb.documents.azure.com:443/";`
      
   1. Copie o valor da **CHAVE PRIMÁRIA** e cole-o em `<your primary key>` em *Program.cs*. Por exemplo:  
      
      `private const string PrimaryKey = "19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==";`
   
1. Após o método `Main`, adicione uma nova tarefa assíncrona chamada `GetStartedDemo`, que cria um `DocumentClient` chamado `client`.
   
   ```csharp
      private async Task GetStartedDemo()
      {
        client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
      }
   ```
   
1. Adicione o código a seguir ao método `Main` para executar a tarefa `GetStartedDemo`. O método `Main` captura as exceções e grava-as no console.
   
   ```csharp
      static void Main(string[] args)
      {
        try
        {
           Program p = new Program();
           p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine($"{de.StatusCode} error occurred: {de.Message}, Message: {baseException.Message}");
        }
        catch (Exception e)
        {
           Exception baseException = e.GetBaseException();
           Console.WriteLine($"Error: {e.Message}, Message: {baseException.Message}");
        }
        finally
        {
           Console.WriteLine("End of demo, press any key to exit.");
           Console.ReadKey();
        }
      }
   ```
   
1. Pressione **F5** para executar o aplicativo. 
   
1. Quando a mensagem **Final da demonstração; pressione qualquer tecla para sair** for exibida na janela do console, isso significará que a conexão foi bem-sucedida. Pressione qualquer tecla para fechar a janela do console. 

Você se conectou à sua conta do Azure Cosmos DB com êxito. Agora, trabalhe com alguns recursos do Azure Cosmos DB.  

## <a name="create-a-database"></a>Criar um banco de dados

Um [banco de dados](databases-containers-items.md#azure-cosmos-databases) do Azure Cosmos DB é o contêiner lógico de armazenamento de documentos JSON particionado em coleções. Crie um banco de dados usando o método [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) da classe `DocumentClient`. 

1. Antes de adicionar o código para criar um banco de dados, adicione um método auxiliar para gravar no console. Copie e cole o método `WriteToConsoleAndPromptToContinue` a seguir após o método `GetStartedDemo` no código.
   
   ```csharp
   private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
   {
      Console.WriteLine(format, args);
      Console.WriteLine("Press any key to continue...");
      Console.ReadKey();
   }
   ```
   
1. Copie e cole a linha a seguir no método `GetStartedDemo`, após a linha `client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);`. Esse código cria um banco de dados chamado `FamilyDB`.
   
   ```csharp
      await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
   ```
   
1. Pressione **F5** para executar o aplicativo.

Você criou um banco de dados do Azure Cosmos DB com êxito. Você poderá ver o banco de dados no [portal do Azure](https://portal.azure.com) selecionando **Data Explorer** no painel de navegação à esquerda de sua conta do Azure Cosmos DB. 

## <a id="CreateColl"></a>Criar uma coleção

Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada. Crie uma coleção usando o método [CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync#overloads) da classe `DocumentClient`. 

> [!IMPORTANT]
> **CreateDocumentCollectionIfNotExistsAsync** cria uma coleção com uma taxa de transferência reservada, com implicações de preço. Para obter mais detalhes, visite a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 

1. Copie e cole o código a seguir no método `GetStartedDemo`, após a linha `await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });`. Esse código cria uma coleção de documentos chamada `FamilyCollection`.
   
   ```csharp
      await client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });
   ```
   
1. Pressione **F5** para executar o aplicativo.

Você criou uma coleção de documentos do Azure Cosmos DB com êxito. Você poderá ver a coleção no banco de dados **FamilyDB** do **Data Explorer** no portal do Azure.  

## <a id="CreateDoc"></a>Criar documentos JSON

Os documentos são um conteúdo JSON arbitrário definido pelo usuário. Os documentos precisam ter uma propriedade de ID serializada como `id` em JSON. Crie documentos usando o método [CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync#overloads) da classe `DocumentClient`. 

> [!TIP]
> Se você já tiver dados que deseja armazenar no banco de dados, use a [Ferramenta de Migração de Dados](import-data.md) do Azure Cosmos DB para importá-los.
>

O código a seguir cria e insere dois documentos na coleção de bancos de dados. Primeiro, crie uma classe `Family` e as subclasses `Parent`, `Child`, `Pet` e `Address` para usá-las dentro de `Family`. Em seguida, você criará um método `CreateFamilyDocumentIfNotExists` e, então, criará e inserirá dois documentos. 

1. Copie e cole as classes `Family`, `Parent`, `Child`, `Pet` e `Address` a seguir após o método `WriteToConsoleAndPromptToContinue` no código.
   
   ```csharp
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
   ```
   
1. Copie e cole o método `CreateFamilyDocumentIfNotExists` a seguir após a classe `Address` recém-adicionada.
   
   ```csharp
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            WriteToConsoleAndPromptToContinue($"Found {family.Id}");
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                WriteToConsoleAndPromptToContinue($"Created Family {family.Id}");
            }
            else
            {
                throw;
            }
        }
    }
   ```
   
1. Copie e cole o código a seguir no final do método `GetStartedDemo`, após a linha `await client.CreateDocumentCollectionIfNotExistsAsync`. Esse código cria e insere dois documentos, um para cada família, Martins e Barros.
   
   ```csharp
    Family andersenFamily = new Family
    {
        Id = "AndersenFamily",
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

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

    Family wakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
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

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);
   ```
   
1. Pressione **F5** para executar o aplicativo.

Você criou dois documentos do Azure Cosmos DB com êxito. Você poderá ver os documentos no banco de dados **FamilyDB** e na coleção **FamilyCollection** do **Data Explorer** no portal do Azure.   

![Diagrama que ilustra a relação hierárquica entre a conta, o banco de dados online, a coleção e os documentos](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Consultar recursos do Azure Cosmos DB

O Azure Cosmos DB dá suporte a [consultas](how-to-sql-query.md) avançadas em documentos JSON armazenados em coleções. O código de exemplo a seguir usa a sintaxe LINQ e SQL do Azure Cosmos DB para executar uma consulta nos documentos de exemplo.

1. Copie e cole o método `ExecuteSimpleQuery` a seguir após o método `CreateFamilyDocumentIfNotExists` no código.
   
   ```csharp
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options.
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Find the Andersen family by its LastName.
        IQueryable<Family> familyQuery = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
            .Where(f => f.LastName == "Andersen");

        // Execute the query synchronously. 
        // You could also execute it asynchronously using the IDocumentQuery<T> interface.
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine($"\tRead {family}");
        }

        // Now execute the same query using direct SQL.
        IQueryable<Family> familyQueryInSql = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
            "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
            queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
            Console.WriteLine($"\tRead {family}");
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }
   ```
   
1. Copie e cole o código a seguir no final do método `GetStartedDemo`, após a linha `await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);`.
   
   ```csharp
      ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Pressione **F5** para executar o aplicativo.

A consulta anterior retorna o item completo para a família Martins. Você consultou uma coleção do Azure Cosmos DB com êxito.

O diagrama a seguir ilustra como a sintaxe de consulta SQL do Azure Cosmos DB é chamada na coleção. A mesma lógica se aplica à consulta LINQ.

![Diagrama que ilustra o escopo e o significado da consulta usada pelo tutorial do NoSQL para criar um aplicativo de console em C#](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

A palavra-chave [FROM](how-to-sql-query.md#FromClause) na consulta SQL é opcional, pois as consultas do Azure Cosmos DB já têm o escopo para uma única coleção. Você pode trocar `FROM Families f` por `FROM root r` ou por qualquer outro nome de variável escolhido. O Azure Cosmos DB inferirá que `Families`, `root` ou o nome da variável escolhido se refere à coleção atual.

## <a id="ReplaceDocument"></a>Atualizar um documento JSON

A API do SQL do Azure Cosmos DB dá suporte à atualização e à substituição de documentos JSON.  

1. Copie e cole o método `ReplaceFamilyDocument` a seguir após o método `ExecuteSimpleQuery` no código.
   
   ```csharp
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
       await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
       WriteToConsoleAndPromptToContinue($"Replaced Family {familyName}");
    }
   ```
   
1. Copie e cole o código a seguir no final do método `GetStartedDemo`, após a linha `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");`. O código atualiza os dados em um dos documentos e, em seguida, executa a consulta novamente para mostrar o documento alterado.
   
   ```csharp
   // Update the Grade of the Andersen Family child
   andersenFamily.Children[0].Grade = 6;
   await ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily", andersenFamily);
   ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Pressione **F5** para executar o aplicativo.

A saída da consulta mostra que a `Grade` para o filho da família Martins for atualizada de `5` para `6`. Você atualizou e substituiu um documento do Azure Cosmos DB com êxito. 

## <a id="DeleteDocument"></a>Excluir um documento JSON

A API do SQL do Azure Cosmos DB dá suporte à exclusão de documentos JSON.  

1. Copie e cole o método `DeleteFamilyDocument` a seguir após o método `ReplaceFamilyDocument`.
   
   ```csharp
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
        Console.WriteLine($"Deleted Family {documentName}");
    }
   ```
   
1. Copie e cole o código a seguir no final do método `GetStartedDemo`, após a segunda linha `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");`.
   
   ```csharp
   await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");
   ```
   
1. Pressione **F5** para executar o aplicativo.

Você excluiu um documento do Azure Cosmos DB com êxito. 

## <a id="DeleteDatabase"></a>Excluir o banco de dados

Exclua o banco de dados criado para removê-lo e todos os seus recursos filho, incluindo a coleção e os documentos. 

1. Copie e cole o código a seguir no final do método `GetStartedDemo`, após a linha `await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");`. 
   
   ```csharp
   // Clean up - delete the database
   await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));
   ```
   
1. Pressione **F5** para executar o aplicativo.

Você excluiu o banco de dados do Azure Cosmos DB com êxito. Você poderá ver no **Data Explorer** de sua conta do Azure Cosmos DB que o banco de dados FamilyDB foi excluído. 

## <a id="Run"></a>Executar todo o aplicativo de console C#

Pressione **F5** no Visual Studio para compilar e executar o aplicativo de console C# completo no modo de depuração. Você deverá ver a seguinte saída na janela do console:

```bash
Created Family AndersenFamily
Press any key to continue ...
 Created Family WakefieldFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Replaced Family AndersenFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Deleted Family AndersenFamily
End of demo, press any key to exit.
```

Parabéns! Você concluiu o tutorial e tem um aplicativo de console C# funcional que cria, consulta, atualiza e exclui recursos do Azure Cosmos DB.  

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre o Azure Cosmos DB, confira [Bem-vindo ao Azure Cosmos DB](introduction.md).
* Para obter um tutorial mais complexo do ASP.NET MVC, confira [Tutorial do ASP.NET MVC: Desenvolvimento de aplicativo Web com o Azure Cosmos DB](sql-api-dotnet-application.md).
* Para realizar os testes de escala e desempenho com o Azure Cosmos DB, confira [Testes de desempenho e escala com o Azure Cosmos DB](performance-testing.md).
* Para saber como monitorar as solicitações, o uso e o armazenamento do Azure Cosmos DB, confira [Monitorar contas](monitor-accounts.md).
* Executar consultas em um conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).

