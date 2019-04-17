---
title: Carregar dados para um índice do Azure Search em C# (SDK do .NET) - Azure Search
description: O exemplo de código C# para criar uma consulta de pesquisa no Azure Search. Adicione parâmetros de pesquisa para filtrar e classificar os resultados da pesquisa.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: 1194407122123797c2564c96ac452b9582b017a4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264940"
---
# <a name="quickstart-3---query-an-azure-search-index-in-c"></a>Início Rápido: 3 - Consultar um índice do Azure Search em C#

Este artigo mostra como consultar [um índice do Azure Search](search-what-is-an-index.md) usando C# e o [SDK do .NET](https://aka.ms/search-sdk). Pesquisar documentos para o índice é realizado executando estas tarefas:

> [!div class="checklist"]
> * Crie um objeto [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) para conectar a um índice de pesquisa com direitos somente leitura.
> * Crie um objeto [`SearchParameters`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet) que contém a definição de pesquisa ou de filtro.
> * Chame o `Documents.Search` método no `SearchIndexClient` para enviar consultas para um índice.

## <a name="prerequisites"></a>Pré-requisitos

[Carregar um índice do Azure Search](search-import-data-dotnet.md) com os dados de exemplo de hotéis.

Obtenha uma chave de consulta usada para acesso somente leitura aos documentos. Até agora, você usou uma chave de API de administração para que você possa criar objetos e conteúdo em um serviço de pesquisa. Mas, para suporte a consultas em aplicativos, é altamente recomendável usar uma chave de consulta. Para obter instruções, consulte [Criar uma chave de consulta](search-security-api-keys.md#create-query-keys).

## <a name="create-a-client"></a>Criar um cliente
Crie uma instância da `SearchIndexClient` para que você possa atribuir a ela uma chave de consulta para acesso somente leitura (como oposto aos direitos de acesso de gravação concedidos mediante o `SearchServiceClient` usado na lição anterior).

Essa classe tem vários construtores. A classe desejada usa o nome do serviço de pesquisa, nome do índice e um [`SearchCredentials`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials?view=azure-dotnet) objeto como parâmetros. `SearchCredentials` encapsula a api-key.

O código a seguir cria um novo `SearchIndexClient` para o índice “hotéis” usando valores para o nome do serviço de pesquisa e API-Key e que são armazenados no arquivo de configuração do aplicativo (`appsettings.json` no caso do [aplicativo do exemplo](https://aka.ms/search-dotnet-howto)):

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

`SearchIndexClient` tem uma propriedade [`Documents`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient.documents?view=azure-dotnet). Essa propriedade fornece todos os métodos necessários para consultar índices de Azure Search.

## <a name="construct-searchparameters"></a>Construir SearchParameters
Pesquisar com o SDK do .NET é tão simples quanto chamar o método `Documents.Search` em seu `SearchIndexClient`. Este método usa alguns parâmetros, incluindo o texto de pesquisa, junto com um objeto `SearchParameters` que pode ser usado para refinar mais a consulta.

### <a name="types-of-queries"></a>Tipos de Consultas
Os dois [tipos de consulta](search-query-overview.md#types-of-queries) principais que você usará são `search` e `filter`. Uma consulta `search` procura um ou mais termos em todos os campos *pesquisáveis* no índice. Uma consulta `filter` avalia uma expressão booliana em todos os campos *filtráveis* em um índice. Você pode usar pesquisas e filtros juntos ou separados.

As pesquisas e os filtros são executados usando o método `Documents.Search` . Uma consulta de pesquisa pode ser passada no parâmetro `searchText`, enquanto uma expressão de filtro pode ser passada na propriedade `Filter` da classe `SearchParameters`. Para filtrar sem pesquisar, basta passar `"*"` para o parâmetro `searchText`. Para pesquisar sem filtrar, deixe a propriedade `Filter` indefinida ou simplesmente não passe uma instância `SearchParameters`.

### <a name="example-queries"></a>Consultas de Exemplo
O código de exemplo a seguir mostra algumas maneiras diferentes de consultar o índice "hotéis" definido em [Criar um índice do Azure Search no C#](search-create-index-dotnet.md#DefineIndex). Observe que os documentos retornados com os resultados da pesquisa são instâncias da classe `Hotel`, que foi definida em [Importação de dados no Azure Search usando o C#](search-import-data-dotnet.md#construct-indexbatch). O código de exemplo usa um método `WriteDocuments` para enviar os resultados da pesquisa para o console. Esse método é descrito na próxima seção.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="handle-search-results"></a>Controlar os resultados da pesquisa
O método `Documents.Search` retorna um objeto `DocumentSearchResult` que contém os resultados da consulta. O exemplo na seção anterior usou um método denominado `WriteDocuments` para enviar os resultados da pesquisa para o console:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Aqui está o que os resultados serão semelhantes para as consultas na seção anterior, supondo que o índice "hotéis" seja preenchido com os dados de exemplo:

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577
```

O código de exemplo acima usa o console para gerar os resultados da pesquisa. Da mesma forma, você precisará exibir os resultados da pesquisa em seu próprio aplicativo. Para obter um exemplo de como renderizar os resultados da pesquisa em um aplicativo web com base no ASP.NET MVC, consulte o [DotNetSample projeto](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) no GitHub.

## <a name="next-steps"></a>Próximas etapas

Se você ainda não fez isso, examine o código de exemplo em [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) no GitHub, junto com [Como usar o Azure Search em um aplicativo .NET](search-howto-dotnet-sdk.md) para obter descrições mais detalhadas do código de exemplo. 