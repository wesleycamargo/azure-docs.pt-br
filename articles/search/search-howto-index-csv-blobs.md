---
title: "Indexação de blobs CSV com o indexador de blob do Azure Search | Microsoft Docs"
description: Saiba como indexar blobs CSV com o Azure Search
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: ed3c9cff-1946-4af2-a05a-5e0b3d61eb25
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 12/28/2017
ms.author: eugenesh
ms.openlocfilehash: 40b7f1f4f75d389a64329e7d8fd3c7feb79d5e55
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indexando blobs CSV com o indexador de blobs do Azure Search
Por padrão, o [indexador de blobs do Azure Search](search-howto-indexing-azure-blob-storage.md) analisa blobs de texto delimitado como um único bloco de texto. No entanto, com blobs contendo dados CSV, o ideal é tratar cada linha no blob como um documento separado. Por exemplo, considerando o seguinte texto delimitado: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Talvez você queira analisá-lo em dois documentos, cada um contendo os campos "tags", "datePublished" e "id".

Neste artigo, você aprenderá como analisar blobs CSV com um indexador de blob do Azure Search. 

> [!IMPORTANT]
> Esta funcionalidade está em preview. Ela está disponível somente na API REST que usa a versão **2015-02-28-Preview**. Lembre-se de que as APIs de visualização servem para teste e avaliação, e não devem ser usadas em ambientes de produção. 
> 
> 

## <a name="setting-up-csv-indexing"></a>Configurando a indexação de CSV
Para indexar blobs CSV, criar ou atualizar uma definição de indexador com o modo de análise `delimitedText` :  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Para saber mais sobre a API Criar Indexador, veja [Criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

`firstLineContainsHeaders` indica que a primeira linha (não vazia) de cada blob contém cabeçalhos.
Se os blobs não contêm uma linha de cabeçalho inicial, os cabeçalhos devem ser especificados na configuração do indexador: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

É possível personalizar o caractere delimitador usando a configuração `delimitedTextDelimiter`. Por exemplo: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Atualmente, apenas a codificação UTF-8 tem suporte. Se precisar de suporte para outras codificações, informe-nos em [nosso site UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Quando você usa o modo de análise de texto delimitado, o Azure Search pressupõe que todos os blobs em sua fonte de dados serão CSV. Se você precisar dar suporte a uma combinação de blobs CSV e não CSV na mesma fonte de dados, informe-nos em [nosso site UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Exemplos de solicitação
Reunindo tudo isso, estes são os exemplos de carga completa. 

Fonte de dados: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
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

## <a name="help-us-make-azure-search-better"></a>Ajude-nos a aprimorar o Azure Search
Se você tiver solicitações de recursos ou ideias para o aperfeiçoamentos, entre em contato conosco pelo [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

