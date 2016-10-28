<properties
pageTitle="Indexação de blobs CSV com o indexador de blob da Pesquisa do Azure | Microsoft Azure"
description="Saiba como indexar blobs CSV com a Pesquisa do Azure"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="07/12/2016"
ms.author="eugenesh" />

# Indexando blobs CSV com o indexador de blobs da Pesquisa do Azure 

Por padrão, o [indexador de blobs da Pesquisa do Azure](search-howto-indexing-azure-blob-storage.md) analisa blobs de texte delimitado como um único bloco de texto. No entanto, com blobs contendo dados CSV, o ideal é tratar cada linha no blob como um documento separado. Por exemplo, considerando o seguinte texto delimitado:

	id, datePublished, tags
	1, 2016-01-12, "azure-search,azure,cloud" 
	2, 2016-07-07, "cloud,mobile" 

Talvez você queira analisá-lo em dois documentos, cada um contendo os campos "tags", "datePublished" e "id".

Neste artigo, você aprenderá como analisar blobs CSV com um indexador de blob da Pesquisa do Azure.

> [AZURE.IMPORTANT] Esta funcionalidade está em preview. Ela está disponível somente na API REST que usa a versão **2015-02-28-Preview**. Lembre-se de que as APIs de visualização servem para teste e avaliação, e não devem ser usadas em ambientes de produção.

## Configurando a indexação de CSV

Para indexar blobs CSV, criar ou atualizar uma definição de indexador com o modo de análise `delimitedText`:

	{
	  "name" : "my-csv-indexer",
	  ... other indexer properties
	  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
	}

Para saber mais sobre a API Criar Indexador, veja [Criar indexador](search-api-indexers-2015-02-28-preview.md#create-indexer).

`firstLineContainsHeaders` indica que a primeira linha (não vazia) de cada blob contém cabeçalhos. Se os blobs não contêm uma linha de cabeçalho inicial, os cabeçalhos devem ser especificados na configuração do indexador:

	"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Atualmente, apenas a codificação UTF-8 tem suporte. Além disso, somente o caracter `','` vírgula tem suporte como delimitador. Se você precisa de suporte para outras codificações ou delimitadores, informe-nos em [nosso site UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [AZURE.IMPORTANT] Quando você usa o modo de análise de texto delimitado, a Pesquisa do Azure pressupõe que todos os blobs em sua fonte de dados serão CSV. Se você precisar dar suporte a uma combinação de blobs CSV e não CSV na mesma fonte de dados, informe-nos em [nosso site UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## Exemplos de solicitação

Reunindo tudo isso, estes são os exemplos de carga completa.

Fonte de dados:

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "my-blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
	}   

Indexador:

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "my-csv-indexer",
	  "dataSourceName" : "my-blob-datasource",
	  "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
	}

## Ajude-nos a aprimorar a Pesquisa do Azure

Se você tiver solicitações de recursos ou ideias para o aperfeiçoamentos, entre em contato conosco pelo [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0713_2016-->