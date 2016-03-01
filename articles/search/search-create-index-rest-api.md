<properties
	pageTitle="Criar um índice da Pesquisa do Azure usando uma API REST | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
	description="Crie um índice no código usando a Pesquisa do Azure e uma API REST HTTP."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/18/2016"
	ms.author="heidist"/>

# Criar um índice da Pesquisa do Azure usando uma API REST
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Este artigo mostra como criar um índice usando a [API REST da Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Parte do conteúdo abaixo é de [Criar índice (API REST da Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx). Consulte o artigo pai para obter mais contexto.

Os pré-requisitos para a criação de um índice incluem ter estabelecido anteriormente uma conexão com o serviço de pesquisa, o que normalmente é feito por meio de um ‘httpClient’.

## Definir o esquema de índice

Para criar o índice usando a API REST, você emitirá uma solicitação POST para seu ponto de extremidade de URL da Pesquisa do Azure. Você definirá a estrutura do índice usando JSON no corpo da solicitação.

**Solicitação e Cabeçalhos da Solicitação**:

Na amostra abaixo, você precisará usar o ponto de extremidade de URL do seu serviço, observando especificamente o nome do serviço e a versão da API apropriada (a versão da API atual é a "2015-02-28" no momento da publicação deste documento). Nos Cabeçalhos da Solicitação, você também precisará fornecer a Chave do Administrador Principal do serviço que recebeu ao [criar um serviço](https://msdn.microsoft.com/library/azure/dn798941.aspx/).

	POST https://[servicename].search.windows.net/indexes?api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key]


**Corpo da Solicitação**:

Isso é onde você pode identificar o nome do índice ("hotéis", neste caso), bem como os [nomes, os tipos e os atributos dos campos](https://msdn.microsoft.com/library/azure/dn798941.aspx).

	{
		"name": "hotels",  
		"fields": [
			{"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
			{"name": "baseRate", "type": "Edm.Double"},
			{"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
			{"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer:"fr.lucene"},
			{"name": "hotelName", "type": "Edm.String"},
			{"name": "category", "type": "Edm.String"},
			{"name": "tags", "type": "Collection(Edm.String)"},
			{"name": "parkingIncluded", "type": "Edm.Boolean"},
			{"name": "smokingAllowed", "type": "Edm.Boolean"},
			{"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
			{"name": "rating", "type": "Edm.Int32"},
			{"name": "location", "type": "Edm.GeographyPoint"}
		]
	}

Para uma solicitação bem-sucedida, você deverá ver o código de status "201 Criado". Para saber mais sobre como criar um índice por meio da API REST, visite [esta página](https://msdn.microsoft.com/library/azure/dn798941.aspx).

<!---HONumber=AcomDC_0224_2016-->