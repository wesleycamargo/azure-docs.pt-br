<properties
    pageTitle="Consultar seu Índice da Pesquisa do Azure usando o SDK do .NET | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Crie uma consulta de pesquisa na Pesquisa do Azure e use parâmetros de pesquisa para filtrar e classificar os resultados da pesquisa."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="05/23/2016"
    ms.author="brjohnst"/>

# Consultar seu índice de Pesquisa do Azure usando o SDK do .NET
> [AZURE.SELECTOR]
- [Visão geral](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Este artigo mostrará como consultar um índice usando o [SDK do .NET da Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Antes de começar este passo a passo, você já deve ter [criado um índice de Pesquisa do Azure](search-what-is-an-index.md) e [preenchido-o com dados](search-what-is-data-import.md).

Observe que todos os códigos de exemplo neste artigo são escritos em C#. Você pode encontrar o código-fonte completo [no GitHub](http://aka.ms/search-dotnet-howto).

## I. Identificar sua api-key de consulta do serviço de Pesquisa do Azure
Agora que criou um índice de Pesquisa do Azure, você está quase pronto para emitir consultas usando o SDK do .NET. Primeiro, você precisará obter uma das chaves da API de consulta que foram geradas para o serviço de pesquisa que provisionou. O SDK do .NET enviará essa chave de API em cada solicitação para o serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

1. Para localizar as api-keys de seu serviço, você deve fazer logon no [Portal do Azure](https://portal.azure.com/)
2. Vá para a folha do serviço de Pesquisa do Azure
3. Clique no ícone de "Chaves"

O serviço terá *chaves de administração* e *chaves de consulta*.

  - Suas *chaves de administração* principal e secundária concedem direitos totais para todas as operações, incluindo a capacidade de gerenciar o serviço, criar e excluir índices, indexadores e fontes de dados. Há duas chaves para que você possa continuar a usar a chave secundária se decidir regenerar a chave primária e vice-versa.
  - As *chaves de consulta* concedem acesso somente leitura a índices e documentos e normalmente são distribuídas para aplicativos cliente que emitem solicitações de pesquisa.

Para consultar um índice, você pode usar uma de suas chaves de consulta. As chaves de administração também podem ser usadas para consultas, mas você deve usar uma chave de consulta no código do aplicativo, já que isso segue melhor o [Princípio do privilégio mínimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## II. Criar uma instância da classe SearchIndexClient
Para emitir consultas com o SDK do .NET da Pesquisa do Azure, você precisará criar uma instância da classe `SearchIndexClient`. Essa classe tem vários construtores. A classe desejada usa o nome do serviço de pesquisa, nome do índice e um objeto `SearchCredentials` como parâmetros. `SearchCredentials` encapsula sua api-key.

O código a seguir cria um novo `SearchIndexClient` para o índice "hotéis" (criado em [Criar um índice de Pesquisa do Azure usando o SDK do .NET](search-create-index-dotnet.md)) usando valores para o nome do serviço de pesquisa e a api-key armazenados no arquivo de configuração do aplicativo (`app.config` ou `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string queryApiKey = ConfigurationManager.AppSettings["SearchServiceQueryApiKey"];

SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
```

`SearchIndexClient` tem uma propriedade `Documents`. Essa propriedade fornece todos os métodos necessários para consultar índices de Pesquisa do Azure.

## III. Consultar o índice
Pesquisar com o SDK do .NET é tão simples quanto chamar o método `Documents.Search` em seu `SearchIndexClient`. Este método usa alguns parâmetros, incluindo o texto de pesquisa, junto com um objeto `SearchParameters` que pode ser usado para refinar mais a consulta.

#### Tipos de Consultas
Os dois [tipos de consulta](search-query-overview.md#types-of-queries) principais que você usará são `search` e `filter`. Uma consulta `search` procura um ou mais termos em todos os campos _pesquisáveis_ no índice. Uma consulta `filter`avalia uma expressão booliana em todos os campos _filtráveis_ em um índice.

As pesquisas e os filtros são executados usando o método `Documents.Search`. Uma consulta de pesquisa pode ser passada no parâmetro `searchText`, enquanto uma expressão de filtro pode ser passada na propriedade `Filter` da classe `SearchParameters`. Para filtrar sem pesquisar, basta passar `"*"` para o parâmetro `searchText`. Para pesquisar sem filtrar, deixe a propriedade `Filter` indefinida ou simplesmente não passe uma instância `SearchParameters`.

#### Consultas de Exemplo

O código de exemplo a seguir mostra algumas maneiras diferentes de consultar o índice "hotéis" definido em [Criar um índice de Pesquisa do Azure usando o SDK do .NET](search-create-index-dotnet.md#DefineIndex). Observe que os documentos retornados com os resultados da pesquisa são instâncias da classe `Hotel`, que foi definida em [Importação de dados na Pesquisa do Azure usando o SDK do .NET](search-import-data-dotnet.md#HotelClass). O código de exemplo usa um método `WriteDocuments` para enviar os resultados da pesquisa para o console. Esse método é descrito na próxima seção.

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

## IV. Controlar os resultados da pesquisa
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

Os resultados das consultas da seção anterior se parecem com esses, supondo que o índice "hotéis" seja preenchido com os dados de exemplo em [Importação de dados na Pesquisa do Azure usando o SDK do .NET](search-import-data-dotnet.md):

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

O código de exemplo acima usa o console para gerar os resultados da pesquisa. Da mesma forma, você precisará exibir os resultados da pesquisa em seu próprio aplicativo. Consulte [este exemplo no GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) de como renderizar os resultados da pesquisa em um aplicativo Web baseado no ASP.NET MVC.

<!---HONumber=AcomDC_0525_2016-->