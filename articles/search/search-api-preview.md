---
title: Visualização da API REST do Azure Search - versão prévia 11-11-2017 - Azure Search
description: A Versão da API REST do Serviço Azure Search 2017-11-11-Versão prévia inclui recursos experimentais como pesquisas de sinônimos e do tipo moreLikeThis.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 06/28/2018
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: e496e4e2e7e2f66033d090a0534911bff4c53baa
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310195"
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Versão da API REST do Serviço Azure Search 2017-11-11-Versão prévia
Este artigo descreve a versão `api-version=2017-11-11-Preview` da API REST do serviço Azure Search, oferecendo recursos experimentais ainda não disponíveis.

> [!NOTE]
> Os recursos de versão prévia estão disponíveis para teste e experimentação com a meta de coletar comentários e estão sujeitos a alteração. É altamente recomendável não usar APIs de versão prévia em aplicativos de produção.


## <a name="new-in-2017-11-11-preview"></a>Novidades na versão prévia 2017-11-11

[**O preenchimento automático** ](search-autocomplete-tutorial.md) une a [API de sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) para adicionar experiências de complementar preenchimento automático para a barra de pesquisa. O preenchimento automático retorna candidato em termos de consulta, um usuário pode escolher como a cadeia de caracteres de consulta para uma pesquisa subsequente. Sugestões retorna documentos reais em resposta às entradas parciais: resultados da pesquisa são imediatos e alteram dinamicamente à medida que a entrada de termo de pesquisa aumenta em tamanho e a especificidade.

A [**pesquisa cognitiva**](cognitive-search-concept-intro.md), uma nova funcionalidade de enriquecimento no Azure Search encontra informações latentes em fontes que não são de texto e em textos não diferenciados, transformando-as em um conteúdo pesquisável de texto completo no Azure Search. Os recursos a seguir são introduzidos ou modificados na API REST de versão prévia. Todas as outras APIs REST são as mesmas, independentemente de você chamar a versão disponível ou de versão prévia.

+ [Operações de conjunto de qualificações (api-version=2017-11-11-Versão prévia)](https://docs.microsoft.com/rest/api/searchservice/skillset-operations)

+ [Criar Indexador (api-version=2017-11-11-Versão prévia)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)

Todas as outras APIs REST são as mesmas, independentemente de como você definir a versão de API. Por exemplo, `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11-Preview` e `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11` (sem `Preview`) são funcionalmente equivalentes.

## <a name="other-preview-features"></a>Outros recursos de versão prévia

Os recursos anunciados em versões prévias anteriores ainda estão em versão prévia pública. Se estiver chamando uma API com uma versão prévia anterior, você poderá continuar usando essa versão ou mudar para a `2017-11-11-Preview` sem alterações no comportamento esperado.

+ [Arquivos CSV na indexação de Blob do Azure](search-howto-index-csv-blobs.md), apresentados na `api-version=2015-02-28-Preview`, continuam sendo um recurso em versão prévia. Esse recurso faz parte da indexação de Blob do Azure e é invocado por uma configuração de parâmetro. Cada linha em um arquivo CSV é indexada como um documento separado.

+ [Matrizes JSON na indexação de Blob do Azure](search-howto-index-json-blobs.md), apresentadas na `api-version=2015-02-28-Preview`, continuam sendo um recurso em versão prévia. Esse recurso faz parte da indexação de Blob do Azure e é invocado por uma configuração de parâmetro. em que cada elemento na matriz é indexado como um documento separado.

+ [O parâmetro de consulta moreLikeThis](search-more-like-this.md) localiza documentos relevantes para um documento específico. Esse recurso é encontrado em versões prévias anteriores. 


## <a name="how-to-call-a-preview-api"></a>Como chamar uma API de versão prévia

As versões prévias mais antigas ainda funcionam, mas se tornam obsoletas ao longo do tempo. Se o código chama `api-version=2016-09-01-Preview` ou `api-version=2015-02-28-Preview`, as chamadas ainda são válidas. No entanto, apenas a versão prévia mais recente é atualizada com melhorias. 

A sintaxe de exemplo a seguir ilustra uma chamada para a versão de visualização da API.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

O serviço Azure Search está disponível em várias versões. Para obter mais informações, confira [Versões de API](search-api-versions.md).
