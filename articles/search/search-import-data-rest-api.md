<properties
	pageTitle="Importar dados para a Pesquisa do Azure usando a API REST | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
	description="Como carregar dados em um índice na Pesquisa do Azure usando a API REST."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/17/2016"
	ms.author="heidist"/>

# Importar dados para a Pesquisa do Azure usando a API REST
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Este artigo mostra como preencher um índice usando a [API REST da Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Parte do conteúdo abaixo é de [Adicionar, atualizar ou excluir documentos (API REST da Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798930.aspx). Consulte o artigo pai para obter mais contexto.

Para enviar por push documentos para seu índice usando a API REST, você emitirá solicitações HTTP POST para o ponto de extremidade de URL do serviço.

**Solicitação e Cabeçalhos da Solicitação**:

Na URL, você terá que fornecer o nome do serviço, bem como a versão de API apropriada (a versão de API atual é "2015-02-28" no momento da publicação deste documento).

Nos cabeçalhos da solicitação, você precisará definir o tipo de conteúdo e fornecer a Chave Primária de Administração do serviço.

	POST https://[servicename].search.windows.net/indexes/[indexname]/docs/index?api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key]


**Corpo da Solicitação**:


	{
		"value": [
			{
				"@search.action": "upload",
				"hotelId": "1",
				"baseRate": 199.0,
				"description": "Best hotel in town",
				"description_fr": "Meilleur hôtel en ville",
				"hotelName": "Fancy Stay",
				"category": "Luxury",
				"tags": ["pool", "view", "wifi", "concierge"],
				"parkingIncluded": false,
				"smokingAllowed": false,
				"lastRenovationDate": "2010-06-27T00:00:00Z",
				"rating": 5,
				"location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
			},
			{
				"@search.action": "upload",
				"hotelId": "2",
				"baseRate": 79.99,
				"description": "Cheapest hotel in town",
				"description_fr": "Hôtel le moins cher en ville",
				"hotelName": "Roach Motel",
				"category": "Budget",
				"tags": ["motel", "budget"],
				"parkingIncluded": true,
				"smokingAllowed": true,
				"lastRenovationDate": "1982-04-28T00:00:00Z",
				"rating": 1,
				"location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
			}
		]
	}

Nesse caso, estamos usando "carregar" como nossa ação de pesquisa. Ao atualizar e excluir documentos existentes, você poderá usar "merge", "mergeOrUpload" e "delete".

Ao atualizar o índice, você receberá um código de status "200 OK" para um êxito. Você receberá um código de status “207” caso pelo menos um item em sua solicitação não tenha sido indexado com êxito.

Para saber mais sobre ações do documento e respostas de acertos/erros, consulte [esta página](https://msdn.microsoft.com/library/azure/dn798930.aspx).

<!---HONumber=AcomDC_0224_2016-->