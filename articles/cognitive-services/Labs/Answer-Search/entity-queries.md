---
title: Consulta de Entidade da Pesquisa de Resposta de Projeto - Serviços Cognitivos da Microsoft | Microsoft Docs
description: Consulta de Entidades usando a Pesquisa de Resposta de Projeto
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 2b8382b791c02514e5110097700e223d98fafd6a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364217"
---
# <a name="query-for-entities"></a>Consultar Entidades

Se a consulta solicitar informações sobre uma pessoa, lugar ou coisa, a resposta poderá conter uma resposta do `entities`.  As consultas que sempre retornam páginas da Web, [fatos](fact-queries.md) e/ou [entidades](entity-queries.md) dependem de consulta.

As entidades dão suporte a três cenários de consulta: 
-   DominantEntity — há apenas uma única entidade que faz a correspondência da consulta e da intenção do usuário. Por exemplo, a consulta, Space Needle, é um cenário de DominantEntity. 
-   Desambiguidade — não há mais de uma entidade que corresponda a consulta e a intenção do usuário e cabe ao usuário selecionar a entidade correta. Por exemplo, a consulta Game of Thrones é um cenário de Desambiguidade que retorna o programa de TV e a série de livros. 
-   Lista – há várias entidades que correspondem à consulta e à intenção do usuário. Por exemplo, a consulta "Lista de espécies ameaçadas" é um cenário de lista que retorna valores de tabela formatados para exibição nas linhas e células. 
 
Para determinar o cenário de consulta, use o campo `queryScenario` do objeto `entities`. Os dados que a entidade inclui dependem do tipo de entidade. Embora as entidades incluam as mesmas informações básicas, algumas entidades como atrações turísticas ou manuais incluem propriedades adicionais. As entidades que incluem propriedades adicionais incluem o campo `_type` que contém uma dica usada pelo serializador. As entidades a seguir incluem propriedades adicionais: 
-   Livro 
-   MusicRecording 
-   Pessoa 
-   Atração 
 
Para determinar o tipo de entidade que contém a resposta, use o campo `entityTypeHints` como mostrado na consulta por Bill Gates.
````
        },
        "description": "Bill Gates is an American business man and philanthropist, co-founder of Microsoft",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Person"
          ]
        },
        "bingId": "6d7d66a7-2cb8-0ae9-637c-f81fd749dc9a"
      }
````
A seguir, uma consulta sobre a Space Needle:
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=space+needle&mkt=en-us
````
A resposta inclui a resposta `entities`. Observe os campos `entityScenario` e `entityTypeHints`. 
````
  "entities": {
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#Entities.0",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
              "name": "CC-BY-SA",
              "url": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "http://en.wikipedia.org/wiki/Space_Needle"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://en.wikipedia.org/wiki/Space_Needle"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dSpace+Needle\u0026filters\u003dsid:%22f8dd5b08-206d-2554-6e4a-893f51f4de7e%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Space Needle",
        "image": {
          "name": "Space Needle",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA15d336cf119b9b5c7e0ab37e271421d3\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "http://en.wikipedia.org/wiki/Space_Needle"
            }
          ],
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 152,
          "sourceHeight": 300
        },
        "description": "The Space Needle is an observation tower in Seattle, Washington, a landmark of the Pacific Northwest, and an icon of Seattle. It was built in the Seattle Center for the 1962 World\u0027s Fair, which drew over 2.3 million visitors, when nearly 20,000 people a day used its elevators.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Attraction"
          ]
        },
        "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e"
      }
    ]
  },
````

Uma consulta pode retornar uma lista se ela for relevante.

**Consulta:** a consulta a seguir localiza uma lista de espécies ameaçadas:

````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=list+of+endangered+species

````

**Resposta:** a resposta inclui uma lista formatada para exibição como valores de tabela:
````
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.worldwildlife.org/species/directory?direction=desc&sort=e…",
        "url": "https://www.worldwildlife.org/species/directory?direction=desc&sort=extinction_status"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.worldwildlife.org/species/directory?direction=desc&sort=e…",
        "seeMoreUrl": "https://www.worldwildlife.org/species/directory?direction=desc&sort=extinction_status"
      }
    ],
    "value": [
      {
        "subjectName": "Species Directory",
        "richCaption": {
          "_type": "StructuredValue/TabularData",
          "header": [
            "Common name",
            "Scientific name",
            "Conservation status ↓"
          ],
          "rows": [
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Amur Leopard",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/amur-leopard"
                },
                {
                  "text": "Panthera pardus orientalis"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            },
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Black Rhino",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/black-rhino"
                },
                {
                  "text": "Diceros bicornis"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            },
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Bornean Orangutan",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/bornean-orangutan"
                },
                {
                  "text": "Pongo pygmaeus"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            },
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Cross River Gorilla",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/cross-river-gorilla"
                },
                {
                  "text": "Gorilla gorilla diehli"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            }
          ],
          "seeMoreUrl": {
            "text": "46 more rows",
            "url": "https://www.worldwildlife.org/species/directory?direction=desc&sort=extinction_status"
          }
        }
      }
    ]
  },

````


## <a name="next-steps"></a>Próximas etapas
- [Início rápido do C#](c-sharp-quickstart.md)
- [Início rápido do Java](java-quickstart.md)
- [Início rápido do Node](node-quickstart.md)
- [Início rápido do Python](python-quickstart.md)