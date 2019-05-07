---
title: V2 para a migração de API V3
titleSuffix: Azure Cognitive Services
description: O ponto de extremidade da versão 3 APIs foram alterados. Use este guia para entender como migrar para o ponto de extremidade da versão 3 APIs.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: d0b5537f31edf330b54b91ddf7268338df0c0a6b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148267"
---
# <a name="preview-migrate-to-api-version-3x--for-luis-apps"></a>Visualização: Migrar para a versão de API 3.x para aplicativos do LUIS

O ponto de extremidade de previsão de consulta APIs foram alterados. Use este guia para entender como migrar para o ponto de extremidade da versão 3 APIs. 

Essa API V3 fornece os seguintes recursos novos, que incluem alterações significativas de solicitação de e/ou resposta JSON: 

* [Entidades externas](#external-entities-passed-in-at-prediction-time)
* [Listas dinâmicas](#dynamic-lists-passed-in-at-prediction-time)
* [Alterações de JSON de entidade predefinidas](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

O ponto de extremidade de previsão de consulta [solicitação](#request-changes) e [resposta](#response-changes) têm alterações significativas para dar suporte os novos recursos listados acima, incluindo o seguinte:

* [Alterações de objeto de resposta](#top-level-json-changes)
* [Referências de nome de função de entidade em vez do nome da entidade](#entity-role-name-instead-of-entity-name)
* [Propriedades a marcação de entidades em declarações](#marking-placement-of-entities-in-utterances)

São os seguintes recursos de LUIS **não tem suporte** na API V3:

* V7 de verificação ortográfica do Bing

[Documentação de referência](https://aka.ms/luis-api-v3) está disponível para a V3.

## <a name="prebuilt-domains-with-new-models-and-language-coverage"></a>Domínios predefinidos com novos modelos e a abrangência do idioma

Examine os [lista de API V3 de domínios predefinidos](luis-reference-prebuilt-domains.md). Esses domínios são mais completas, tanto no modelo e a abrangência do idioma. 

## <a name="prebuilt-entities-with-new-json"></a>Entidades predefinidas com o novo JSON

As alterações de objeto de resposta V3 incluem [as entidades predefinidas](luis-reference-prebuilt-entities.md). 

## <a name="request-changes"></a>Alterações de solicitação 

### <a name="query-string-parameters"></a>Parâmetros de cadeia de caracteres de consulta

A API de V3 tem parâmetros de cadeia de caracteres de consulta diferentes.

|Nome do parâmetro|Type|Version|Finalidade|
|--|--|--|--|
|`query`|string|Somente V3|**Na V2**, a expressão a ser previsto é no `q` parâmetro. <br><br>**Na V3**, a funcionalidade é passada a `query` parâmetro.|
|`show-all-intents`|boolean|Somente V3|Retornar todas as intenções com a pontuação correspondente na **prediction.intents** objeto. Tentativas são retornadas como objetos em uma pasta pai `intents` objeto. Isso permite o acesso programático sem a necessidade de localizar a intenção em uma matriz: `prediction.intents.give`. Na versão 2, eles foram retornados em uma matriz. |
|`verbose`|boolean|V2 E V3|**Na V2**, quando definido como true, previstos todas as intenções foram retornados. Se você precisar previstas todas as intenções, use o param V3 de `show-all-intents`.<br><br>**Na V3**, esse parâmetro só fornece entidade detalhes de metadados de previsão de entidade.  |

<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>A previsão de consulta JSON de corpo para o `POST` solicitação

```JSON
{
    "query":"your utterance here",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

## <a name="response-changes"></a>Alterações de resposta

A resposta de consulta JSON alterada para permitir maior acesso programático aos dados usados com mais frequência. 

### <a name="top-level-json-changes"></a>Alterações de JSON de nível superior

São as principais propriedades JSON para V2, quando `verbose` é definido como true, que retorna todas as intenções e suas pontuações no `intents` propriedade:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

As principais propriedades JSON para V3 são:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

O `intents` objeto é uma lista não ordenada. Não pressuponha que o primeiro filho na `intents` corresponde à `topIntent`. Em vez disso, use o `topIntent` valor para encontrar a pontuação:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Permitem alterações de esquema de resposta JSON:

* Desmarque a distinção entre a expressão original, `query`e retornada a previsão, `prediction`.
* Mais fácil acesso programático aos dados previstos. Em vez de enumerar por meio de uma matriz na V2, você pode acessar valores por **chamado** para intenções e entidades. Para funções de entidade previsto, o nome da função é retornado porque ele é exclusivo em todo o aplicativo.
* Tipos de dados, se determinado, são respeitados. Valores numéricos não são retornados como cadeias de caracteres.
* Distinção entre as informações de previsão de prioridade primeiro e metadados adicionais, é retornado no `$instance` objeto. 

### <a name="access-instance-for-entity-metadata"></a>Acesso `$instance` para metadados de entidade

Se você precisar de metadados de entidade, a cadeia de caracteres de consulta precisa usar o `verbose=true` sinalizador e a resposta contém os metadados a `$instance` objeto. Exemplos são mostrados nas respostas JSON nas seções a seguir.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>Cada entidade prevista é representada como uma matriz

O `prediction.entities.<entity-name>` objeto contém uma matriz como cada entidade pode ser prevista com mais de uma vez na declaração. 

### <a name="list-entity-prediction-changes"></a>Lista de alterações de previsão de entidade

O JSON para uma previsão de entidade da lista foi alterada para ser uma matriz de matrizes:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Cada matriz interna corresponde ao texto dentro de expressão. O objeto interior é uma matriz porque o mesmo texto pode aparecer em mais de uma sublista de uma entidade da lista. 

Quando o mapeamento entre o `entities` do objeto para o `$instance` do objeto, a ordem dos objetos é preservada para as previsões de entidade da lista.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>Nome da função de entidade em vez do nome da entidade 

Na versão 2, o `entities` matriz retornada todas as entidades previstas com o nome da entidade que está sendo o identificador exclusivo. V3, se a entidade usa funções e a previsão se destina uma função de entidade, o identificador primário é o nome da função. Isso é possível porque os nomes de função de entidade devem ser exclusivos em todo o aplicativo incluindo outros nomes de modelo (entidade de intenção,).

No exemplo a seguir: considere uma expressão que inclui o texto, `Yellow Bird Lane`. Esse texto é previsto como um personalizado `Location` a função da entidade de `Destination`.

|Texto de expressão|Nome da entidade|Nome da função|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

Na versão 2, a entidade é identificada pelo _nome da entidade_ com a função como uma propriedade do objeto:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

Na V3, a entidade é referenciada pela _função de entidade_, se a previsão se destina a função:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

V3, o mesmo resultado com o `verbose` sinalizador para retornar metadados de entidade:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

## <a name="external-entities-passed-in-at-prediction-time"></a>Entidades externas passado em tempo de previsão

Entidades externas permitem que seu aplicativo LUIS para identificar e rotular entidades durante o tempo de execução, que pode ser usado como recursos para entidades existentes. Isso permite que você use seus próprios Extratores de entidade separado e personalizado antes de enviar consultas para seu ponto de extremidade de previsão. Como isso é feito no ponto de extremidade de previsão de consulta, você não precisa readaptar e publicar seu modelo.

O aplicativo cliente está fornecendo seu próprio extrator de entidade por meio do gerenciamento de entidade correspondente e determinar o local em que a expressão dessa entidade correspondente e, em seguida, enviar essas informações com a solicitação. 

Entidades externas são o mecanismo para estender qualquer tipo de entidade enquanto ainda está sendo usado como sinais para outros modelos como funções, composição e outros.

Isso é útil para uma entidade que tem dados disponíveis apenas em tempo de execução de previsão de consulta. Exemplos desse tipo de dados são alterados constantemente específicos por usuário ou dados. Você pode estender uma entidade de contato de LUIS com informações externas da lista de contatos do usuário. 

### <a name="entity-already-exists-in-app"></a>Entidade já existe no aplicativo

O valor de `entityName` para a entidade externa, passada na solicitação de ponto de extremidade do corpo do POST, já deve existir no aplicativo treinado e publicado no momento em que a solicitação é feita. O tipo de entidade não importa, todos os tipos são suportados.

### <a name="first-turn-in-conversation"></a>Pela primeira vez na conversa

Considere uma primeira expressão em uma conversa de bot de bate-papo em que um usuário inserir as seguintes informações incompletas:

`Send Hazem a new message`

A solicitação do bot bate-papo para LUIS pode passar informações no corpo da POSTAGEM sobre `Hazem` para que ele é comparado diretamente como um dos contatos do usuário.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

A resposta de previsão inclui a entidade externa, com todas as outras previstas entidades, porque ele está definido na solicitação.  

### <a name="second-turn-in-conversation"></a>Em segundo lugar ligar na conversa

A próxima declaração de usuário no bot bate-papo usa um termo mais vago:

`Send him a calendar reminder for the party.`

Na declaração anterior, a expressão usa `him` como uma referência para `Hazem`. O bot de conversação bate-papo, no corpo da POSTAGEM, poderá mapear `him` como o valor de entidade extraído da primeira declaração, `Hazem`.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

A resposta de previsão inclui a entidade externa, com todas as outras previstas entidades, porque ele está definido na solicitação.  

#### <a name="resolution"></a>Resolução

O _opcional_ `resolution` propriedade retorna na resposta da previsão, permitindo que você passe os metadados associados com a entidade externa e, em seguida, recebê-la de volta na resposta. 

O objetivo principal é estender as entidades predefinidas, mas ele não está limitado a esse tipo de entidade. 

O `resolution` propriedade pode ser um número, uma cadeia de caracteres, um objeto ou uma matriz:

* "Dallas"
* {"text": "value"}
* 12345 
* ["a", "b", "c"]


## <a name="dynamic-lists-passed-in-at-prediction-time"></a>As listas dinâmicas passado em tempo de previsão

As listas dinâmicas permitem que você estender uma entidade existente da lista treinado e publicado, já no aplicativo LUIS. 

Use esse recurso quando os valores da entidade lista precisam alterar periodicamente. Esse recurso permite que você estenda uma entidade lista já treinado e publicado:

* No momento da solicitação de ponto de extremidade de previsão de consulta.
* Para uma única solicitação.

A entidade da lista pode estar vazia no aplicativo LUIS, mas ele deve existir. A entidade da lista no aplicativo LUIS não é alterada, mas a capacidade de previsão no ponto de extremidade é estendida para incluir listas de até 2 com cerca de 1.000 itens.

### <a name="dynamic-list-json-request-body"></a>Lista dinâmica de corpo da solicitação JSON

Enviar o seguinte corpo JSON para adicionar uma nova sublista com sinônimos à lista e prever a entidade da lista para o texto `LUIS`, com o `POST` solicitação de consulta de previsão:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntityName":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

A resposta de previsão inclui essa entidade de lista, com todas as outras previstas entidades, porque ele está definido na solicitação. 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset renomeado para datetimeReference

**Na V2**, o `timezoneOffset` [parâmetro](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) é enviado na solicitação de previsão como um parâmetro de cadeia de caracteres de consulta, independentemente se a solicitação é enviada como uma solicitação GET ou POST. 

**Na V3**, a mesma funcionalidade é fornecida com o parâmetro de corpo do POST, `datetimeReference`. 

## <a name="marking-placement-of-entities-in-utterances"></a>Posicionamento de entidades em declarações de marcação

**Na V2**, uma entidade foi marcada em uma declaração com o `startIndex` e `endIndex`. 

**Na V3**, a entidade está marcada com `startIndex` e `entityLength`.


## <a name="next-steps"></a>Próximas etapas

Use a documentação da API de V3 atualizar REST existente chama para LUIS [ponto de extremidade](https://aka.ms/luis-api-v3) APIs. 