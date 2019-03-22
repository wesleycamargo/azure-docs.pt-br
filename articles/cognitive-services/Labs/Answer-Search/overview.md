---
title: O que é a Pesquisa de Resposta de Projeto?
titlesuffix: Azure Cognitive Services
description: Introdução à Pesquisa de Resposta de Projeto.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: overview
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: ac1717a8e8a08fcfedc3bc21bb0f03b3e3ca2511
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548074"
---
# <a name="what-is-project-answer-search"></a>O que é a Pesquisa de Resposta de Projeto?
A API de Pesquisa de Resposta de Projeto usa o ponto de extremidade do Bing v7 para obter respostas para consultas interrogativas. A pergunta como "Qual é a circunferência da Terra?" retorna uma resposta com informações reais.  Uma consulta sobre uma pessoa, lugar ou coisa retorna informações sobre a entidade identificada pela consulta. Esses cenários podem ser úteis em aplicativos como bots de conversa, aplicativos de mensagens, leitores, etc.  

As consultas retornam respostas que dependem do cenário da consulta: as páginas da Web são sempre retornadas, enquanto os [fatos](fact-queries.md) e/ou as [entidades](entity-queries.md) são retornados se forem relevantes.

## <a name="endpoint"></a>Ponto de extremidade
Para obter respostas para uma pergunta ou informações sobre uma pessoa, local ou coisa, envie uma solicitação ao ponto de extremidade da API de Pesquisa de Respostas. Use os cabeçalhos e parâmetros de URL para várias especificações.  Inclua o cabeçalho *Ocp-Apim-Subscription-Key* com um token válido.  O parâmetro market é obrigatório. Somente o market `en-us` é atualmente suportado.

A consulta a seguir obtém respostas à pergunta: "Qual é a circunferência da Terra?"

GET:
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

```

O parâmetro de URL `q=` é necessário para especificar o objeto da pesquisa.

## <a name="response-object"></a>Objeto de resposta

A resposta inclui os cabeçalhos HTTP, páginas da Web, fatos e/ou entidades.

```
BingAPIs-TraceId: AB2E75C998614ADB8EBF5110DF648298
X-MSEdge-ClientID: 1E48FC4F7B8768C80B14F7997A106906
BingAPIs-SessionId: 0504DDD6DAE84861A4842306F8DA7A58
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: AB2E75C998614ADB8EBF5110DF648298 Ref B: CO1EDGE0322 Ref C: 2018-04-19T19:57:13Z

JSON Response:

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "what is the circumference of earth"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q\u003dwhat+is+the+circumference+of+earth",
    "totalEstimatedMatches": 217000,
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.0",
        "name": "Circumference of the Earth - Universe Today",
        "url": "https://www.universetoday.com/26461/circumference-of-the-earth/",
        "isFamilyFriendly": true,
        "displayUrl": "https://www.universetoday.com/26461/circumference-of-the-earth",
        "snippet": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "deepLinks": [
          {
            "name": "About Earth",
            "url": "https://www.universetoday.com/14382/10-interesting-facts-about-planet-earth/"
          }
        ],
        "dateLastCrawled": "2018-04-12T14:13:00.0000000Z",
        "language": "en"
      },
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.1",
        "name": "Earth - Wikipedia",
        "url": "https://en.wikipedia.org/wiki/Earth",
        "about": [
          {
            "name": "Earth"
          },
          {
            "name": "Earth"
          }
        ],
        "isFamilyFriendly": true,
        "displayUrl": "https://en.wikipedia.org/wiki/Earth",
        "snippet": "Circumference: 40 075.017 km equatorial (24 901.461 mi) ... Earth is the third planet from the Sun and the only object in the Universe known to harbor life.",
        "deepLinks": [
          {
            "name": "Moon",
            "url": "https://en.wikipedia.org/wiki/Moon"
          },
          {
            "name": "Planet",
            "url": "https://en.wikipedia.org/wiki/Planet"
          },
          {
            "name": "Quasi-Satellites",
            "url": "https://en.wikipedia.org/wiki/Quasi-satellite"
          },
          {
            "name": "World Population",
            "url": "https://en.wikipedia.org/wiki/World_population"
          },
   . . .

    ]
  },
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
              "url": "https://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "https://en.wikipedia.org/wiki/Earth"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "https://en.wikipedia.org/wiki/Earth"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dEarth\u0026filters\u003dsid:%226ddb3372-4801-5567-321e-e8a53bd774a4%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Earth",
        "image": {
          "name": "Earth",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA3ab623665ab412f386c162bd29f0683a\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "https://en.wikipedia.org/wiki/Earth"
            }
          ],
          "hostPageUrl": "https://upload.wikimedia.org/wikipedia/commons/9/97/The_Earth_seen_from_Apollo_17.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 799,
          "sourceHeight": 800
        },
        "description": "Earth is the third planet from the Sun and the only object in the Universe known to harbor life. According to radiometric dating and other sources of evidence, Earth formed over 4.5 billion years ago. Earth\u0027s gravity interacts with other objects in space, especially the Sun and the Moon, Earth\u0027s only natural satellite. Earth revolves around the Sun in 365.26 days, a period known as an Earth year. During this time, Earth rotates about its axis about 366.26 times.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Generic"
          ]
        },
        "bingId": "6ddb3372-4801-5567-321e-e8a53bd774a4"
      }
    ]
  },
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.universetoday.com/26461/circumference-of-the-earth/",
        "url": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.universetoday.com/26461/circumference-of-the-earth/",
        "seeMoreUrl": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "value": [
      {
        "description": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "subjectName": ""
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "Facts",
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Facts"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.0"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.1"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 2,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.2"
          }
        },
        
        . . . 
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        }
      ]
    }
  }
}

```

## <a name="terms-of-use"></a>Termos de uso
A Pesquisa de Respostas de Projeto e as Tendências de Vídeo de Projeto estão sujeitas a [Requisitos de exibição e uso da Pesquisa do Bing](use-display-requirements.md).

Você, ou um terceiro em seu nome, não poderá usar, reter, armazenar, armazenar em cache, compartilhar ou distribuir nenhum dado da API de Análise para fins de teste, desenvolvimento, treinamento, distribuição ou disponibilização de qualquer serviço ou recurso que não seja da Microsoft. 

## <a name="throttling-requests"></a>Solicitações de limitação

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Atribuição de dados  

As respostas da Pesquisa de Respostas de Projeto contêm informações pertencentes a terceiros. Você é responsável por garantir o uso apropriado, por exemplo, respeitando qualquer licença Creative Commons da qual sua experiência do usuário possa depender.  
  
Se uma resposta ou um resultado incluir os campos `contractualRules`, `attributions` ou `provider`, os dados precisarão ser atribuídos. Se a resposta não incluir nenhum desses campos, nenhuma atribuição será necessária. Se a resposta incluir o campo `contractualRules` e os campos `attributions` e/ou `provider`, você precisará usar as regras contratuais para atribuir os dados.  
  
O exemplo a seguir mostra uma entidade que inclui uma regra contratual MediaAttribution e uma Image que inclui um campo `provider`. A regra MediaAttribution identifica a imagem como o destino da regra e, portanto, você ignorará o campo `provider` da imagem e, em vez disso, usará a regra MediaAttribution para fornecer a atribuição.  
  
```  
        "value" : [{
            "contractualRules" : [
                . . .
                {
                    "_type" : "ContractualRules\/MediaAttribution",
                    "targetPropertyName" : "image",
                    "mustBeCloseToContent" : true,
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }
            ],
            . . .
            "image" : {
                "name" : "Space Needle",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A46378861201...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }],
                "hostPageUrl" : "http:\/\/www.citydictionary.com\/Uploaded...",
                "width" : 110,
                "height" : 110
            },
            . . .
        }]
```  
  
Se uma regra contratual incluir o campo `targetPropertyName`, a regra se aplicará somente ao campo de destino. Caso contrário, a regra se aplicará ao objeto pai que contém o campo `contractualRules`.  
  
  
No exemplo a seguir, a regra `LinkAttribution` inclui o campo `targetPropertyName` e, portanto, a regra se aplica ao campo `description`. No caso das regras que se aplicam a campos específicos, é necessário incluir uma linha imediatamente após os dados de destino que contenha um hiperlink para o site do provedor. Por exemplo, para atribuir a descrição, inclua uma linha imediatamente após o texto de descrição que contenha um hiperlink para os dados no site do provedor. Nesse caso, crie um link para en.wikipedia.org.  
  
```  
"entities" : {  
    "value" : [{  
            . . .  
            "description" : "Peyton Williams Manning is a former American....",  
            . . .  
            "contractualRules" : [{  
                    "_type" : "ContractualRules\/LinkAttribution",  
                    "targetPropertyName" : "description",  
                    "mustBeCloseToContent" : true,  
                    "text" : "en.wikipedia.org",  
                    "url" : "http:\/\/www.bing.com\/cr?IG=B8AD73..."  
                 },  
            . . .  
  
```  

### <a name="license-attribution"></a>Atribuição de licença  

Se a lista de regras contratuais incluir uma regra [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution), será necessário exibir o aviso na linha imediatamente após o conteúdo ao qual a licença se aplica. A regra `LicenseAttribution` usa o campo `targetPropertyName` para identificar a propriedade à qual a licença se aplica.  
  
Veja a seguir um exemplo que inclui uma regra `LicenseAttribution`.  
  
![Atribuição de licença](./media/licenseattribution.png)  
  
O aviso de licença exibido precisa incluir um hiperlink para o site que contenha informações sobre a licença. Normalmente, você transforma o nome da licença em um hiperlink. Por exemplo, se o aviso for **Texto sob a licença CC-BY-SA** e CC-BY-SA for o nome da licença, transforme CC-BY-SA em um hiperlink.  
  
### <a name="link-and-text-attribution"></a>Atribuição de link e texto  

As regras [LinkAttribution](reference.md#linkattribution) e [TextAttribution](reference.md#textattribution) normalmente são usadas para identificar o provedor dos dados. O campo `targetPropertyName` identifica o campo ao qual a regra se aplica.  
  
Para atribuir os provedores, inclua uma linha imediatamente após o conteúdo ao qual as atribuições se aplicam (por exemplo, o campo de destino). A linha deve ser claramente rotulada para indicar que os provedores são a fonte dos dados. Por exemplo, "Dados de: en.wikipedia.org". Para regras `LinkAttribution`, é necessário criar um hiperlink para o site do provedor.  
  
Veja a seguir um exemplo que inclui regras `LinkAttribution` e `TextAttribution`.  
  
![Atribuição de link de texto](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Atribuição de mídia  

Se a entidade incluir uma imagem e você exibi-la, você precisará fornecer um link para clicar para o site do provedor. Se a entidade incluir uma regra [MediaAttribution](reference.md#mediaattribution), use a URL da regra para criar o link para clicar. Caso contrário, use a URL incluída no campo `provider` da imagem para criar o link para clicar.  
  
Veja a seguir um exemplo que inclui o campo `provider` e as regras contratuais de uma imagem. Como o exemplo inclui a regra contratual, ignore o campo `provider` da imagem e aplique a regra `MediaAttribution`.  
  
![Atribuição de mídia](./media/mediaattribution.png)  

## <a name="next-steps"></a>Próximas etapas
- [Início Rápido do C#](c-sharp-quickstart.md)
- [Início Rápido do Java](java-quickstart.md)
- [Início Rápido do Node](node-quickstart.md)
- [Início Rápido do Python](python-quickstart.md)
