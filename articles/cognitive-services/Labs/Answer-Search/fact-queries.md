---
title: 'Início rápido: Consulta de fato de Pesquisa de Resposta do Projeto'
titlesuffix: Azure Cognitive Services
description: Consulta fatos usando a Pesquisa de Resposta de Projeto
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/16/2018
ms.author: rosh
ms.openlocfilehash: 2a2e899d9c1c624a5d516a819e7bcf409975c981
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883212"
---
# <a name="quickstart-query-for-facts"></a>Início Rápido: Consulta fatos

Se a consulta for para um fato como uma data ou informações identificáveis, a resposta poderá conter respostas `facts`. Respostas de fatos contêm resultados relevantes extraídos de parágrafos em documentos da Web.  Essas consultas sempre retornam páginas da Web, e [fatos](fact-queries.md) e/ou [entidades](entity-queries.md) dependem da consulta.

Consultas como diadosnamorados+2016, data+do+ramadan são consideradas consultas relacionadas à data. Se o Bing determinar que a consulta é relacionada à data, a resposta contém uma resposta `facts`.

O exemplo a seguir é uma resposta `facts` relacionada à data.

**Consulta:**
```
https://labsportalppe.azure-api.net/answerSearch/v7.0/search?q=valentines+2016

```

**Resposta:** O campo `subjectName` contém uma versão de exibição de consulta do usuário que você pode usar como um rótulo ao exibir o fato. Se a cadeia de caracteres de consulta for diadosnamorados+2016, o Bing pode alterar para Dia dos Namorados 2016. O campo de descrição contém o fato.

```
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "valentines 2016"
    },
    "facts" : {
        "id" : "https:\/\/www.bingapis.com\/api\/v7\/#Facts",
        "value" : [{
            "description" : "Valentine's Day was on Sunday, February 14, 2016.",
            "subjectName" : "Valentine's Day 2016"
        }]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Facts",
                "value" : {
                    "id" : "https:\/\/www.bingapis.com\/api\/v7\/knowledge\/#Facts"
                }
            }]
        }
    }
}

```

A consulta “Por que o céu é azul?” retorna um exemplo de uma resposta relacionada a informações.

**Consulta:**

```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=why+is+the+sky+blue

```

**Resposta:** O campo `value/description` contém o conhecimento ou as informações solicitadas pela consulta.

```
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "en.wikipedia.org/wiki/Diffuse_sky_radiation",
        "url": "http://en.wikipedia.org/wiki/Diffuse_sky_radiation"
      },
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "spaceplace.nasa.gov/blue-sky/en/",
        "url": "http://spaceplace.nasa.gov/blue-sky/en/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "en.wikipedia.org/wiki/Diffuse_sky_radiation",
        "seeMoreUrl": "http://en.wikipedia.org/wiki/Diffuse_sky_radiation"
      },
      {
        "providerDisplayName": "spaceplace.nasa.gov/blue-sky/en/",
        "seeMoreUrl": "http://spaceplace.nasa.gov/blue-sky/en/"
      }
    ],
    "value": [
      {
        "image": {
          "webSearchUrl": "https://www.bing.com/images/search?view=detailv2&FORM=OIIRPO&q=&id=B632272C4E934D7F0B18790300B2DE34E7676C7A&simid=608045681196075791&iss=eqna",
          "name": "Why is the sky blue in the sky?",
          "thumbnailUrl": "https://www.bing.com/th?id=ODE.858093005&w=140&h=140&c=8&rs=1&qlt=100&pid=3.1",
          "isFamilyFriendly": true
        },
        "description": "When sunlight reaches Earth's atmosphere, it is scattered in all directions by all the gases and particles in the air. Blue is scattered by air molecules more than other colors because it travels as shorter, smaller waves. This is why we see a blue sky most of the time.\n\nCloser to the horizon, the sky fades to a lighter blue or white. The sunlight reaching us from low in the sky has passed through even more air than the sunlight reaching us from overhead. As the sunlight has passed through all this air, the air molecules have scattered and rescattered the blue light many times in many directions. Also, the surface of Earth has reflected and scattered the light. All this scattering mixes the colors together again so we see more white and less blue.",
        "subjectName": "Why is the sky blue in the sky?",
        "primaryData": [
          "The atmosphere scatters more blue light"
        ]
      }
    ]
  },

```

## <a name="tabular-data"></a>Dados tabulares
Em alguns casos, os fatos podem ser retornados como `_type: StructuredValue/TabularData`. A consulta a seguir obtém dados tabulares com informações contrastantes sobre café e chá.

```
https://labsportalppe.azure-api.net/answerSearch/v7.0/search?q=coffee+vs+tea&mkt=en-us

```
Os resultados `facts` incluem as seguintes linhas e células:
```
    "value": [
      {
        "subjectName": "Coffee vs. Tea",
        "richCaption": {
          "_type": "StructuredValue/TabularData",
          "header": [
            "",
            "Coffee",
            "Tea"
          ],
          "rows": [
            {
              "cells": [
                {
                  "text": "Part of plant used"
                },
                {
                  "text": "Bean"
                },
                {
                  "text": "Leaf"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Caffeine Content"
                },
                {
                  "text": "80-185 mg per 8 ounce cup depending upon the brew and the..."
                },
                {
                  "text": "15 - 70 mg per cup"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Types of Consumption"
                },
                {
                  "text": "Drip Coffee"
                },
                {
                  "text": "White Tea"
                }
              ]
            },
            {
              "cells": [
                {
                  "text": "Additions"
                },
                {
                  "text": "Sugar"
                },
                {
                  "text": "Milk"
                }
              ]
            }
          ],
          "seeMoreUrl": {
            "text": "8 more rows",
            "url": "http://www.diffen.com/difference/Coffee_vs_Tea"
          }
        }
      }
    ]
  },

```

## <a name="next-steps"></a>Próximas etapas
- [Início Rápido do C#](c-sharp-quickstart.md)
- [Início Rápido do Java](java-quickstart.md)
- [Início Rápido do Node](node-quickstart.md)
- [Início Rápido do Python](python-quickstart.md)
