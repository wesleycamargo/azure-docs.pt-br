---
title: Chamar a API de um aplicativo – Serviços Cognitivos do Azure | Microsoft Docs
description: Como começar a usar o Serviço de Decisão Personalizada do Azure se você chamar as APIs de um aplicativo para smartphone.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/10/2018
ms.author: slivkins
ms.reviewer: marcozo, alekh
ms.openlocfilehash: 2d02b0deaaa701dd0b4818638827c04e2c946558
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364788"
---
# <a name="call-api-from-an-app"></a>Chamar a API de um aplicativo

Faça chamadas para as APIs de Serviço de Decisão Personalizada do Azure de um aplicativo para smartphone. Este artigo explica como começar a usar algumas opções básicas.

Lembre-se de [Registrar seu aplicativo](custom-decision-service-get-started-register.md) primeiro.

Há duas chamadas à API que você faz do seu aplicativo para smartphone para o Serviço de Decisão Personalizada: uma chamada para a API de Classificação para obter uma lista classificada de seu conteúdo e uma chamada para a API de Recompensa para relatar uma recompensa. Aqui estão as chamadas de amostra na [cURL](https://en.wikipedia.org/wiki/CURL).

Para obter mais informações, confira a [API](custom-decision-service-api-reference.md) de referência.

Comece com a chamada para a API de Classificação. Criar o arquivo `<request.json>`, que tem a ID do conjunto de ações. Essa ID é o nome do feed Atom ou RSS correspondente que você inseriu no portal:

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

Muitos conjuntos de ações podem ser especificados da seguinte maneira:

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

Este arquivo JSON é enviado como parte da solicitação de classificação:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

Aqui, `<appId>` é o nome do seu aplicativo registrado no portal. Você deve receber um conjunto ordenado de itens de conteúdo, que você pode renderizar em seu aplicativo. Um retorno de amostra é semelhante a:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

A primeira parte do retorno tem uma lista de ações ordenadas, especificadas pelas suas IDs de ação. Para um artigo, a ID de ação é uma URL. A solicitação geral também tem um `<eventId>` exclusivo, criado pelo sistema.

Posteriormente, você pode especificar se observou um clique no primeiro item de conteúdo desse evento, que é `<actionId3>`. Então você pode relatar uma recompensa neste `<eventId>` ao Serviço de Decisão Personalizada por meio da API de Recompensa com outra solicitação, como:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

Por fim, você precisa fornecer a API de Conjunto de Ações, que retorna a lista de artigos (ações) a serem considerados pelo Serviço de Decisão Personalizada. Implemente essa API como um RSS feed, conforme mostrado aqui:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Aqui, cada elemento `<item>` de nível superior descreve um artigo. O `<link>` é obrigatório e é usado como uma ID de ação pelo Serviço de Decisão Personalizada. Especifique `<date>` (em um formato padrão do RSS) se você tiver mais de 15 artigos. Os 15 artigos mais recentes são usados. O `<title>` é opcional e é usado para criar recursos relacionados ao texto para o artigo.

## <a name="next-steps"></a>Próximas etapas

* Acompanhe um [tutorial](custom-decision-service-tutorial-news.md) para obter um exemplo mais detalhado.
* Consulte a [API](custom-decision-service-api-reference.md) de referência para saber mais sobre a funcionalidade fornecida.