---
title: 'Tutorial: Compilar um aplicativo .NET Core para gerenciar os dados armazenados na conta da API do SQL do Azure Cosmos DB'
description: Este tutorial cria um banco de dados online e um aplicativo de console C# usando o SDK do .NET Core da API do SQL para o Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 03/12/2018
ms.author: sngun
Customer intent: As a developer, I want to build a .NET Core application to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities that Azure Cosmos DB offers.
ms.openlocfilehash: 52e39b705b8bd0e20c846f065702bcaf7b3a45f1
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487270"
---
# <a name="tutorial-build-a-net-core-app-to-manage-data-stored-in-a-sql-api-account"></a>Tutorial: Compilar um aplicativo .NET Core para gerenciar dados armazenados em uma conta da API do SQL

> [!div class="op_single_selector"]
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (versão prévia)](sql-api-dotnet-core-get-started-preview.md)
> * [.NET](sql-api-get-started.md)
> * [.NET (versão prévia)](sql-api-dotnet-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Como um desenvolvedor, você pode ter aplicativos que usam dados de documentos NoSQL. Você pode usar a conta de API do SQL no Azure Cosmos DB para armazenar e acessar esses dados de documento. Este tutorial mostra como compilar um aplicativo .NET Core para criar e consultar dados armazenados na conta da API do SQL do Azure Cosmos DB. 

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Criar e conectar-se a uma conta do Azure Cosmos
> * Configurando sua solução do Visual Studio
> * Criando um banco de dados online
> * Criar uma coleção
> * Criando documentos JSON
> * Executar operações CRUD nos itens, no contêiner e no banco de dados

Você não tem tempo para criar o aplicativo? A solução completa está disponível em [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started). 

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Baixe e use o [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) gratuito. Se você estiver desenvolvendo um aplicativo de UWP (Plataforma Universal do Windows), utilize o  **Visual Studio 2017 com a versão 15.4** ou superior. Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.

    * Para MacOS ou Linux, você pode desenvolver aplicativos .NET Core na linha de comando instalando o [SDK do .NET Core](https://www.microsoft.com/net/core#macos) para a plataforma de sua escolha. 

    * Para Windows, você pode desenvolver aplicativos .NET Core na linha de comando instalando o [SDK do .NET Core](https://www.microsoft.com/net/core#windows). 

## <a name="create-an-azure-cosmos-account"></a>Criar uma conta do Azure Cosmos

Use as etapas a seguir para criar uma conta do Azure Cosmos:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Configurar sua solução do Visual Studio

1. Abra o **Visual Studio 2017** em seu computador.

2. No menu **Arquivo**, selecione **Novo** e depois **Projeto**.

3. Na caixa de diálogo **Novo Projeto**, selecione **Modelos** > **Visual C#** > **.NET Core** > **Aplicativo de Console (.NET Core)**, nomeie o projeto **DocumentDBGettingStarted** e selecione **OK**.

   ![Captura de tela da janela Novo Projeto](./media/sql-api-dotnetcore-get-started/nosql-tutorial-new-project-2.png)

4. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **DocumentDBGettingStarted**.

5. No mesmo menu, selecione **Gerenciar Pacotes NuGet**.

   ![Captura de tela do menu exibido clicando com o botão direito do mouse para o projeto](./media/sql-api-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)

6. Na guia **NuGet**, selecione **Procurar** na parte superior da janela e digite **azure documentdb** na caixa de pesquisa. A caixa de seleção **Incluir pré-lançamento** deve estar marcada.

7. Nos resultados, encontre **Microsoft.Azure.DocumentDB.Core** e selecione **Instalar**.

   A ID do pacote para a biblioteca é [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core). Se você estiver visando uma versão do .NET Framework (como net461) que não tem suporte deste pacote do NuGet do .NET Core, use [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB), que dá suporte a todas as versões do .NET Framework do .NET Framework 4.5 em diante.

8. Quando solicitado, aceite as instalações de pacote NuGet e o contrato de licença.

Agora que a instalação foi concluída, vamos começar a gravar um código. Você pode encontrar um projeto de código completo deste tutorial no [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started).

## <a id="Connect"></a>Conectar-se a uma conta do Azure Cosmos

Conecte-se a uma conta do Cosmos do Azure importando as dependências necessárias. Para importar as dependências, adicione o seguinte código ao início do arquivo Program.cs:

```csharp
using System;

// ADD THIS PART TO YOUR CODE
using System.Linq;
using System.Threading.Tasks;
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

Em seguida, adicione essas duas constantes e sua variável *client* sob sua classe pública *Program*.

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

Em seguida, vá para o [portal do Azure](https://portal.azure.com) para recuperar o URI e a chave primária. O URI e a chave primária do Azure Cosmos DB são necessários para que seu aplicativo reconheça onde deve se conectar e para que o Azure Cosmos DB confie na conexão do aplicativo.

No portal do Azure, vá para sua conta do Azure Cosmos e, em seguida, selecione **Chaves**.

Copie o URI do portal e cole-o em `<your endpoint URI>` no arquivo program.cs. Em seguida, copie a CHAVE PRIMÁRIA do portal e cole-a em `<your key>`. Lembre-se de remover o < e > , mas deixe as aspas duplas ao redor do ponto de extremidade e da chave.

![Obter chaves do portal do Azure][keys]

Iniciaremos o aplicativo de introdução criando uma nova instância de **DocumentClient**.

Abaixo do método **Main**, adicione a nova tarefa assíncrona denominada **GetStartedDemo**, que criará uma instância do novo **DocumentClient**.

```csharp
static void Main(string[] args)
{
}

// ADD THIS PART TO YOUR CODE
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
}
```

Adicione o código a seguir para executar a tarefa assíncrona a partir do seu método **Main** . O método **Main** captura as exceções e as grava no console.

```csharp
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
```

Selecione o botão **DocumentDBGettingStarted** para compilar e executar o aplicativo.

## <a id="CreateDatabase"></a>Criar um banco de dados

Antes de adicionar o código para criar um banco de dados, adicione um método auxiliar para gravar no console. Copie e cole o método **WriteToConsoleAndPromptToContinue** sob o método **GetStartedDemo**.

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Crie seu banco de dados do Azure Cosmos DB usando o método `CreateDatabaseAsync` da classe **DocumentClient**. Um banco de dados é o contêiner lógico de armazenamento de documentos JSON particionado em coleções. Copie e cole o código a seguir no seu método **GetStartedDemo** embaixo da criação do cliente. Isso cria um banco de dados denominado *FamilyDB*.

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

Selecione o botão **DocumentDBGettingStarted** para executar o aplicativo.

## <a id="CreateColl"></a>Criar uma coleção

Crie uma coleção usando o método `CreateDocumentCollectionAsync` da classe **DocumentClient**. Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada.

> [!WARNING]
> **CreateDocumentCollectionAsync** cria uma nova coleção com uma taxa de transferência reservada, que tem implicações de preço. Para obter mais detalhes, vá para a [página de preço](https://azure.microsoft.com/pricing/details/cosmos-db/).

Copie e cole o código a seguir no seu método **GetStartedDemo** sob a criação do banco de dados. Esse código cria uma coleção de documentos denominada *FamilyCollection_oa*.

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.client.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

Selecione o botão **DocumentDBGettingStarted** para executar o aplicativo.

## <a id="CreateDoc"></a>Criar documentos JSON

Crie um documento usando o método `CreateDocumentAsync` da classe **DocumentClient**. Os documentos são conteúdo JSON (arbitrário) definido pelo usuário. Você agora pode inserir um ou mais documentos. 

Primeiro, você cria uma classe **Family** que representa os objetos armazenados no Azure Cosmos DB. Você também cria as subclasses **Parent**, **Child**, **Pet** e **Address** que são usadas em **Family**. Os documentos devem ter uma propriedade **ID** serializada como **id** em JSON. Crie essas classes adicionando as subclasses internas a seguir após o método **GetStartedDemo**. Copie e cole as classes **Family**, **Parent**, **Child**, **Pet** e **Address** sob o método **WriteToConsoleAndPromptToContinue**.

```csharp
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
```

Copie e cole o método **CreateFamilyDocumentIfNotExists** sob o método **CreateDocumentCollectionIfNotExists**.

```csharp
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
```

Insira dois documentos, um para a Família Martins e um para a Família Barros. Copie e cole o código a seguir `// ADD THIS PART TO YOUR CODE` em seu método **GetStartedDemo** embaixo da criação da coleção de documentos.

```csharp
await this.CreateDatabaseIfNotExistsAsync("FamilyDB_oa");

await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);
```

Selecione o botão **DocumentDBGettingStarted** para executar o aplicativo.

![Relação hierárquica entre conta, banco de dados online, coleção](./media/sql-api-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Consultar recursos do Azure Cosmos DB

O Azure Cosmos DB dá suporte a consultas avançadas de documentos JSON armazenados em cada coleção. O exemplo de código a seguir mostra diversas consultas, usando tanto a sintaxe SQL do Azure Cosmos DB quanto do LINQ, que podem ser executadas nos documentos que inserimos na etapa anterior.

Copie e cole o método **ExecuteSimpleQuery** sob seu método **CreateFamilyDocumentIfNotExists**.

```csharp
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
                "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
                Console.WriteLine("\tRead {0}", family);
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Copie e cole o código a seguir no seu método **GetStartedDemo** embaixo da segunda criação de documento.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

// ADD THIS PART TO YOUR CODE
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Selecione o botão **DocumentDBGettingStarted** para executar o aplicativo.

O diagrama a seguir ilustra como a sintaxe de consulta SQL do Azure Cosmos DB é chamada em relação à coleção que você criou. A mesma lógica se aplica à consulta LINQ.

![Diagrama que ilustra o escopo e o significado da consulta usada pelo tutorial do NoSQL para criar um aplicativo de console em C#](./media/sql-api-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

A palavra-chave `FROM` é opcional na consulta, pois as consultas do Azure Cosmos DB já têm o escopo para uma única coleção. Portanto, "FROM Families f" pode ser trocado por "FROM root r" ou qualquer outra variável de nome que você escolher. O Azure Cosmos DB infere que as Famílias, a raiz ou o nome de variável escolhido faz referência à coleção atual por padrão.

## <a id="ReplaceDocument"></a>Substituir o documento JSON

O Azure Cosmos DB dá suporte à substituição de documentos JSON.  

Copie e cole o método **ReplaceFamilyDocument** sob seu método **ExecuteSimpleQuery**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
{
    await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
    this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
}
```

Copie e cole o código a seguir de seu método **GetStartedDemo** embaixo da execução da consulta. Depois de substituir o documento, isso executará a mesma consulta novamente para exibir o documento alterado.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
// Update the Grade of the Andersen Family child
andersenFamily.Children[0].Grade = 6;

await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Selecione o botão **DocumentDBGettingStarted** para executar o aplicativo.

## <a id="DeleteDocument"></a>Excluir o documento JSON

O Azure Cosmos DB dá suporte à exclusão de documentos JSON.  

Copie e cole o método **DeleteFamilyDocument** sob seu método **ReplaceFamilyDocument**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
{
    await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
    Console.WriteLine("Deleted Family {0}", documentName);
}
```

Copie e cole o código a seguir de seu método **GetStartedDemo** embaixo da segunda execução da consulta.

```csharp
await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO CODE
await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");
```

Selecione o botão **DocumentDBGettingStarted** para executar o aplicativo.

## <a id="DeleteDatabase"></a>Excluir o banco de dados

Excluir o banco de dados criado removerá o banco de dados e todos os recursos filho (coleções, documentos, etc.). Copie e cole o código a seguir de seu método **GetStartedDemo** sob do documento para excluir o banco de dados inteiro e todos os recursos filho.

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

Selecione o botão **DocumentDBGettingStarted** para executar o aplicativo.

## <a id="Run"></a>Executar o aplicativo de console C#

Selecione o botão **DocumentDBGettingStarted** no Visual Studio para compilar o aplicativo no modo de depuração. Você deverá ver a saída do aplicativo iniciado na janela do console. A saída mostrará os resultados das consultas que adicionamos e deverá coincidir com o texto de exemplo abaixo.

```bash
Created FamilyDB_oa
Press any key to continue ...
Created FamilyCollection_oa
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
```

Você agora concluiu este tutorial e tem um aplicativo de console em C# funcional.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, você pode excluir o grupo de recursos, a conta do Azure Cosmos e todos os recursos relacionados. Para fazer isso, escolha o grupo de recursos da máquina virtual, escolha **Excluir** e, em seguida, confirme o nome do grupo de recursos que será excluído.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar um aplicativo .NET Core para gerenciar os dados armazenados na conta da API do SQL do Azure Cosmos DB. Prossiga agora para o próximo artigo:

> [!div class="nextstepaction"]
> [Compilar um aplicativo de console em Java com conta da API do SQL do Azure Cosmos DB](sql-api-java-get-started.md)

[create-sql-api-dotnet.md#create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-dotnetcore-get-started/nosql-tutorial-keys.png
