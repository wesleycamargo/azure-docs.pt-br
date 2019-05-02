---
title: Indexar blobs que contém vários pesquisar documentos de índice do indexador de BLOBs do Azure para pesquisa de texto completo - Azure Search
description: Rastreamento de blobs do Azure para o conteúdo de texto usando o indexador de BLOBs do Azure Search. Cada blob pode conter um ou mais documentos do índice de Azure Search.
ms.date: 02/12/2019
author: arv100kri
manager: briansmi
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seofeb2018
ms.openlocfilehash: e95eff015340659b642dff800a03f615e22c1577
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871192"
---
# <a name="indexing-blobs-producing-multiple-search-documents"></a>Indexação de blobs para produzir vários documentos de pesquisa
Por padrão, um indexador de blob tratará o conteúdo de um blob como um documento de pesquisa único. Determinados **parsingMode** valores dão suporte a cenários em que um blob individual pode resultar em vários documentos de pesquisa. Os diferentes tipos de **parsingMode** que permitem que um indexador para extrair mais do que um pesquisar documento de um blob é:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

> [!IMPORTANT]
> `jsonLines` modo de análise está em visualização pública e não deve ser usado em ambientes de produção. Para obter mais informações, consulte [REST api-version=2017-11-11-Preview](search-api-2017-11-11-preview.md). 

## <a name="one-to-many-document-key"></a>Chave de documento de um-para-muitos
Cada documento que aparece no índice de Azure Search é identificado exclusivamente por uma chave de documento. 

Quando nenhum modo de análise é especificado, e se não houver nenhum explícito de mapeamento para o campo de chave no índice de Azure Search automaticamente [mapeia](search-indexer-field-mappings.md) o `metadata_storage_path` a propriedade como a chave. Esse mapeamento garante que cada blob aparece como um documento de pesquisa distinto.

Ao usar qualquer um dos modos de análise listados acima, um blob é mapeado para "many" Pesquisar documentos, fazendo uma chave de documento exclusivamente com base nos metadados de blob inadequados. Para superar essa restrição, o Azure Search é capaz de gerar uma chave de documento de "um-para-muitos" de cada entidade individual extraída de um blob. Essa propriedade é denominada `AzureSearch_DocumentKey` e é adicionado a cada entidade individual extraída do blob. O valor dessa propriedade é garantido que seja exclusivo para cada entidade individual _em blobs_ e as entidades serão mostrados como documentos de pesquisa separadas.

Por padrão, quando nenhum mapeamento de campo explícitas para o campo de índice de chave é especificado, o `AzureSearch_DocumentKey` é mapeada para ele, usando o `base64Encode` função de mapeamento de campo.

## <a name="example"></a>Exemplo
Suponha que você tenha uma definição de índice com os seguintes campos:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

E seu contêiner de blob tem blobs com a seguinte estrutura:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Quando você cria um indexador e defina as **parsingMode** para `jsonLines` – sem especificar qualquer campo explícito mapeamentos para o campo de chave, o seguinte mapeamento serão aplicados implicitamente
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Essa configuração resultará no índice de Azure Search, que contém as informações a seguir (id de codificação base64 reduzido para fins de brevidade)

| ID | temperatura | pressão |  timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapeamento de campo personalizado para o campo de chave de índice

Supondo que a mesma definição de índice, como no exemplo anterior, digamos que seu contêiner de blob tem blobs com a seguinte estrutura:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Quando você cria um indexador com `delimitedText` **parsingMode**, pareça natural para configurar uma função de mapeamento de campo para o campo de chave da seguinte maneira:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

No entanto, esse mapeamento irá _não_ resultar em 4 documentos aparecendo no índice, pois o `recordid` campo não é exclusivo _entre blobs_. Consequentemente, recomendamos que você faça usam o mapeamento de campo implícito aplicado a partir de `AzureSearch_DocumentKey` propriedade para o campo de índice de chave de modos de análise de "um-para-muitos".

Se você quiser configurar um mapeamento de campo explícitas, verifique se o _sourceField_ é diferente para cada entidade individual **entre todos os blobs**.

> [!NOTE]
> A abordagem usada pelos `AzureSearch_DocumentKey` da garantia de exclusividade por entidade extraída está sujeitas a alterações e, portanto, você não deve confiar em seu valor para as necessidades do seu aplicativo.

## <a name="see-also"></a>Consulte também

+ [Indexadores no Azure Search](search-indexer-overview.md)
+ [Indexação do Armazenamento de Blobs do Azure com o Azure Search](search-howto-index-json-blobs.md)
+ [Indexando blobs CSV com o indexador de blobs do Azure Search](search-howto-index-csv-blobs.md)
+ [Indexando blobs JSON com o indexador de BLOBs do Azure Search](search-howto-index-csv-blobs.md)

## <a name="NextSteps"></a>Próximas etapas
* Para saber mais sobre o Azure Search, confira a [página do serviço do Search](https://azure.microsoft.com/services/search/).