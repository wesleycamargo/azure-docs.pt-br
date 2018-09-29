---
title: Pesquisa cognitiva de mapa aprimorada de campos de entrada para campos de saída nos índices do Azure Search | Microsoft Docs
description: Extrair e enriquecer os campos de dados de origem e mapear para os campos de saída em um índice do Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 77e7a1cc725dc56ee20d3c1999cfb7cf0039d67f
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094453"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Como mapear campos enriquecidos para um índice pesquisável

Neste artigo, você aprenderá como mapear campos de entrada enriquecidos para campos de saída em um índice pesquisável. Uma vez que você [definiu um conjunto de qualificações](cognitive-search-defining-skillset.md), mapeie os campos de saída de qualquer habilidade diretamente a contribuição de valores para um determinado campo no índice de pesquisa. Mapeamentos de campo são necessários para mover o conteúdo de documentos enriquecidos no índice.


## <a name="use-outputfieldmappings"></a>Use outputFieldMappings
Para mapear campos, adicione `outputFieldMappings` à sua definição do indexador, conforme mostrado abaixo:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

O corpo da solicitação é estruturado da seguinte maneira:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions"
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```
Para cada mapeamento de campo de saída, defina o nome do campo enriquecido (sourceFieldName) e o nome do campo referenciado no índice (targetFieldName).

O caminho em um sourceFieldName pode representar um elemento ou vários elementos. No exemplo acima, ```/document/content/sentiment``` representa um único valor numérico, enquanto ```/document/content/organizations/*/description``` representa várias descrições de organização. Em casos onde há vários elementos, são "simples" em uma matriz que contém cada um dos elementos. Mais concretamente, para o ```/document/content/organizations/*/description``` exemplo, os dados no campo *descrições* seriam uma matriz simples de descrições antes que ele obtém indexado:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Próximas etapas
Após mapear os campos enriquecidos para campos de pesquisa, defina os atributos de campo para cada um dos campos pesquisáveis [como parte da definição do índice](search-what-is-an-index.md).

Para obter mais informações de mapeamentos de campo, consulte [Mapeamentos de campo nos indexadores do Azure Search](search-indexer-field-mappings.md).