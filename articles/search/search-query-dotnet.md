<properties
	pageTitle="Criar consultas na Pesquisa do Azure no .NET | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
	description="Crie uma consulta de pesquisa na pesquisa do Azure e use os parâmetros de pesquisa para filtrar, classificar e criar uma faceta dos resultados da pesquisa usando a biblioteca ou o SDK do .NET."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="dotnet"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/10/2015"
	ms.author="heidist"/>

#Criar consultas na Pesquisa do Azure no .NET
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Este artigo mostra como criar uma consulta usando o [SDK do .NET da Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx). O conteúdo abaixo é um subconjunto de [Como usar a Pesquisa do Azure de um aplicativo .NET](search-howto-dotnet-sdk.md). Consulte o artigo pai para obter as etapas de ponta a ponta.

Os pré-requisitos para a criação de uma consulta incluem ter estabelecido anteriormente uma conexão com o serviço de pesquisa, o que normalmente é feito por meio de um `SearchServiceClient`.

O trecho de código a seguir cria um método que passa uma entrada de cadeia de caracteres de pesquisa para um método SearchDocuments.

	private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
	{
		// Execute search based on search text and optional filter
		var sp = new SearchParameters();
	
		if (!String.IsNullOrEmpty(filter))
		{
			sp.Filter = filter;
		}
	
		DocumentSearchResult<Hotel> documentSearchResult = indexClient.Documents.Search<Hotel>(searchText, sp);
		foreach (SearchResult<Hotel> result in documentSearchResult.Results)
		{
			Console.WriteLine(result.Document);
		}
	}
	
Primeiro, esse método cria um novo objeto SearchParameters. Isso é usado para especificar opções adicionais para a consulta, como classificação, filtragem, paginação e organização. Neste exemplo, estamos apenas definindo a propriedade Filter.

A próxima etapa é executar a consulta de pesquisa. Isso é feito usando o método Documents.Search. Nesse caso, passamos o texto de pesquisa a ser usado como uma cadeia de caracteres e também os parâmetros de pesquisa criados anteriormente. Também podemos especificar Hotel como o parâmetro de tipo para Documents.Search, que informa ao SDK para desserializar documentos nos resultados da pesquisa em objetos do tipo Hotel.

Por fim, esse método percorre todas as correspondências nos resultados da pesquisa, imprimindo cada documento no console.

Vamos examinar com mais detalhes como esse método é chamado:

	SearchDocuments(indexClient, searchText: "fancy wifi");
	SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

Na primeira chamada, procuramos todos os documentos com os termos de consulta "fancy" ou "wifi". Na segunda chamada, o texto da pesquisa é definido como "*", que significa "encontrar tudo". Você pode saber mais sobre a sintaxe de expressão da consulta de pesquisa em [Sintaxe de consulta simples na Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798920.aspx).

A segunda chamada usa uma expressão $filter OData, category eq ‘Luxo’. Isso restringe a pesquisa a retornar apenas os documentos onde o campo de categoria corresponder exatamente à cadeia de caracteres "Luxo". Saiba mais sobre a sintaxe OData em [Sintaxe de expressão OData para a Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798921.aspx).

<!---HONumber=AcomDC_0204_2016-->