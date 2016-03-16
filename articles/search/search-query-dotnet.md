<properties
    pageTitle="Consultar seu índice da Pesquisa do Azure usando o SDK do .NET | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
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
    ms.date="03/09/2016"
    ms.author="brjohnst"/>

# Consultar seu índice de Pesquisa do Azure usando o SDK do .NET
> [AZURE.SELECTOR]
- [Visão geral](search-query-overview.md)
- [Gerenciador de Pesquisa](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Este artigo mostrará como consultar um índice usando o [SDK do .NET da Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx). Antes de começar este passo a passo, você já precisa ter [criado um índice de Pesquisa do Azure](search-create-index-dotnet.md) e [preenchido o índice com dados](search-import-data-dotnet.md).

Observe que todos os códigos de exemplo neste artigo são escritos em C#. Você pode encontrar o código-fonte completo [no GitHub](http://aka.ms/search-dotnet-howto).

## I. Identificar sua api-key de consulta do serviço de Pesquisa do Azure
Agora que criou um índice de Pesquisa do Azure, você está quase pronto para emitir consultas usando o SDK do .NET. Primeiro, você precisará obter uma das chaves da API de consulta que foram geradas para o serviço de pesquisa que provisionou. O SDK do .NET enviará essa chave de API em cada solicitação para o serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

1. Para localizar as chaves de API do serviço, você precisa fazer logon no [Portal do Azure](https://portal.azure.com/)
2. Vá para a folha do serviço de Pesquisa do Azure
3. Clique no ícone de "Chaves"

O serviço terá *chaves de administração* e *chaves de consulta*.

  - Suas *chaves de administração* primária e secundária concedem direitos totais para todas as operações, incluindo a capacidade de gerenciar o serviço, criar e excluir índices, indexadores e fontes de dados. Há duas chaves para que você possa continuar a usar a chave secundária se decidir regenerar a chave primária e vice-versa.
  - As *chaves de consulta* concedem acesso somente leitura a índices e documentos e normalmente são distribuídas para aplicativos cliente que emitem solicitações de pesquisa.

Para consultar um índice, você pode usar uma de suas chaves de consulta. As chaves de administração também podem ser usadas para consultas, mas você deve usar uma chave de consulta no código do aplicativo, pois isso segue melhor o [Princípio do privilégio mínimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## II. Criar uma instância da classe SearchIndexClient
Para emitir consultas com o SDK do .NET da Pesquisa do Azure, você precisará criar uma instância da classe `SearchIndexClient`. Essa classe tem vários construtores. O que você deseja usa o nome de serviço de pesquisa, o nome do índice e um objeto `SearchCredentials` como parâmetros. `SearchCredentials` encapsula a sua chave de API.

O código a seguir cria um novo `SearchIndexClient` para o índice "hotéis" (criado em [Criar um índice de Pesquisa do Azure usando o SDK do .NET](search-create-index-dotnet.md)) usando valores para o nome do serviço de pesquisa e a chave de API que estão armazenados no arquivo de configuração do aplicativo (`app.config` ou `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string queryApiKey = ConfigurationManager.AppSettings["SearchServiceQueryApiKey"];

SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
```

`SearchIndexClient` tem uma propriedade `Documents`. Essa propriedade fornece todos os métodos necessários para consultar índices de Pesquisa do Azure.

## III. Consultar o índice
Pesquisar com o SDK do .NET é tão simples quanto chamar o método `Documents.Search` no seu `SearchIndexClient`. Este método usa alguns parâmetros, incluindo o texto de pesquisa, em conjunto com um objeto `SearchParameters` que pode ser usado para refinar mais a consulta.

#### Tipos de consultas

a Pesquisa do Azure oferece várias opções para a criação de consultas extremamente poderosas. Os dois tipos de consulta principais que você usará são `search` e `filter`. Uma consulta `search` pesquisa por um ou mais termos em todos os campos _pesquisáveis_ no índice e funciona da maneira que você esperaria que um mecanismo de pesquisa como Bing ou Google funcionasse. Uma consulta `filter` avalia uma expressão booliana em todos os campos _filtráveis_ de um índice. Diferente das consultas `search`, consultas `filter` fazem a correspondência exata do conteúdo de um campo, o que significa que elas diferenciam maiúsculas de minúsculas para os campos de cadeia de caracteres.

Você pode usar pesquisas e filtros juntos ou separados. Se você usá-los juntos, o filtro será aplicado primeiro ao índice inteiro e, em seguida, a pesquisa será realizada nos resultados do filtro. Os filtros, portanto, podem ser uma técnica útil para melhorar o desempenho da consulta, uma vez que reduzem o conjunto de documentos que a consulta de pesquisa precisa processar.

Pesquisas e filtros são executados usando o método `Documents.Search`. Uma consulta de pesquisa pode ser passada no parâmetro `searchText`, enquanto uma expressão de filtro pode ser passada na propriedade `Filter` da classe `SearchParameters`. Para filtrar sem pesquisar, basta passar `"*"` para o parâmetro `searchText`. Para pesquisar sem filtragem, deixe a propriedade `Filter` indefinida ou simplesmente não passe uma instância `SearchParameters`.

A sintaxe das expressões de filtro é um subconjunto da [linguagem de filtro OData](https://msdn.microsoft.com/library/azure/dn798921.aspx). Para consultas de pesquisa, você pode usar a [sintaxe simplificada](https://msdn.microsoft.com/library/azure/dn798920.aspx) ou a [sintaxe de consulta Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx).

Para saber mais sobre todos os parâmetros diferentes de uma consulta, veja [a referência do SDK do .NET no MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters.aspx). Veja também algumas consultas de exemplo a seguir.

#### Consultas de Exemplo

O código de exemplo a seguir mostra algumas maneiras diferentes de consultar o índice "hotéis" definido em [Criar um índice de Pesquisa do Azure usando o SDK do .NET](search-create-index-dotnet.md#DefineIndex). Observe que os documentos retornados com os resultados da pesquisa são instâncias da classe `Hotel`, que foi definida em [Importação de dados na Pesquisa do Azure usando o SDK do .NET](search-import-data-dotnet.md#HotelClass). O código de exemplo usa um método `WriteDocuments` para gerar os resultados da pesquisa para o console. Esse método é descrito na próxima seção.

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
O método `Documents.Search` retorna um objeto `DocumentSearchResult` que contém os resultados da consulta. O exemplo na seção anterior usou um método chamado `WriteDocuments` para gerar os resultados da pesquisa para o console:

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

<!---HONumber=AcomDC_0309_2016-->