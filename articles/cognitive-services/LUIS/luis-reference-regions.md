---
title: Regiões e pontos de extremidade de publicação
titleSuffix: Azure Cognitive Services
description: A região na qual você publica seu aplicativo LUIS corresponde à região ou ao local especificado no portal do Azure quando você cria uma chave do ponto de extremidade LUIS do Azure. Quando você publica um aplicativo, o LUIS gera automaticamente uma URL de ponto de extremidade para a região associada à chave.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 995300fe2a82fb41078d1c66435f0fb006d5f8cf
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53603684"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Criação e publicação de regiões e as chaves associadas

A região na qual você publica seu aplicativo LUIS corresponde à região ou ao local especificado no portal do Azure quando você cria uma chave do ponto de extremidade LUIS do Azure. Quando você [publica um aplicativo](./luis-how-to-publish-app.md), o LUIS gera automaticamente uma URL de ponto de extremidade para a região associada à chave. Para publicar um aplicativo LUIS em mais de uma região, é necessário ter pelo menos uma chave por região. 

## <a name="luis-website"></a>Site do LUIS
Há três sites do LUIS dependendo da sua região. É necessário criar e publicar na mesma região. 

|LUIS|Região|
|--|--|
|[www.luis.ai][www.luis.ai]|EUA<br>não Europa<br>não Austrália|
|[au.luis.ai][au.luis.ai]|Austrália|
|[eu.luis.ai][eu.luis.ai]|Europa|

## <a name="regions-and-azure-resources"></a>Regiões e recursos do Azure
O aplicativo é publicado em todas as regiões associadas aos recursos do LUIS adicionados no portal do LUIS. Por exemplo, para um aplicativo criado em [www.luis.ai][www.luis.ai], se você criar um recurso de LUIS em **westus** e adicioná-lo ao aplicativo como um recurso, o aplicativo será publicado nessa região. 

## <a name="public-apps"></a>Aplicativos públicos
Um aplicativo público é publicado em todas as regiões para que um usuário com uma chave de recurso do LUIS baseada em região possa acessar o aplicativo em qualquer região associada com a chave de recurso.

## <a name="publishing-regions"></a>Regiões de publicação

Os aplicativos LUIS criados no https://www.luis.ai podem ser publicados em todos os pontos de extremidade, exceto nas regiões [europeias](#publishing-to-europe) e [australianas](#publishing-to-australia). 

O aplicativo da região de criação só pode ser publicado em uma região de publicação correspondente. Se seu aplicativo estiver, no momento, na região de criação incorreta, exporte-o e importe-o para a região de criação correta para sua região de publicação. 

 Região global | Região de criação<br>`API region name` | Região de publicação e de consulta<br>`API region name`   |   Site do LUIS | Formato da URL do ponto de extremidade   |
|-----|------|------|------|------|
| Ásia | Oeste dos EUA<br>`westus`| Ásia Oriental<br>`eastasia`     | [www.luis.ai][www.luis.ai] |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | Oeste dos EUA<br>`westus`| Sudeste Asiático<br>`souteastasia`     | [www.luis.ai][www.luis.ai] |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Austrália](#publishing-to-australia) | Leste da Austrália<br>`australiaeast`| Leste da Austrália<br>`australiaeast`     |   [au.luis.ai][au.luis.ai] | https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Europa](#publishing-to-europe)| Europa Ocidental<br>`westeurope`| Norte da Europa<br>`northeurope`     | [eu.luis.ai][eu.luis.ai]|  https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Europa](#publishing-to-europe) | Europa Ocidental<br>`westeurope`| Europa Ocidental<br>`westeurope`     | [eu.luis.ai][eu.luis.ai]|  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| América do Norte | Oeste dos EUA<br>`westus` | Leste dos EUA<br>`eastus`      |[www.luis.ai][www.luis.ai] |   https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | Oeste dos EUA<br>`westus` | Leste dos EUA 2<br>`eastus2`     | [www.luis.ai][www.luis.ai] |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | Oeste dos EUA<br>`westus` | Centro-Sul dos Estados Unidos<br>`southcentralus`     | [www.luis.ai][www.luis.ai] |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| América do Norte | Oeste dos EUA<br>`westus` | Centro-Oeste dos EUA<br>`westcentralus`     |[www.luis.ai][www.luis.ai] |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | Oeste dos EUA<br>`westus` | Oeste dos EUA<br>`westus`  |  [www.luis.ai][www.luis.ai] | https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| América do Norte | Oeste dos EUA<br>`westus` | Oeste dos EUA 2<br>`westus2`    | [www.luis.ai][www.luis.ai] |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| América do Sul | Oeste dos EUA<br>`westus` | Sul do Brasil<br>`brazilsouth`     | [www.luis.ai][www.luis.ai] |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-europe"></a>Publicando na Europa

Para publicar nas regiões europeias, crie os aplicativos LUIS em https://eu.luis.ai apenas. Se você tentar publicar em outro lugar usando uma chave na região Europa, o LUIS exibirá uma mensagem de aviso. Em vez disso, use https://eu.luis.ai. Os aplicativos LUIS criados em [https://eu.luis.ai][eu.luis.ai] não são migrados automaticamente para outras regiões. Exporte e importe o aplicativo LUIS para migrá-lo.

## <a name="publishing-to-australia"></a>Publicando na Austrália

Para publicar nas regiões australianas, crie os aplicativos LUIS em https://au.luis.ai apenas. Se você tentar publicar em outro lugar usando uma chave na região australiana, o LUIS exibirá uma mensagem de aviso. Em vez disso, use https://au.luis.ai. Os aplicativos LUIS criados em [https://au.luis.ai][au.luis.ai] não são migrados automaticamente para outras regiões. Exporte e importe o aplicativo LUIS para migrá-lo.

## <a name="endpoints"></a>Pontos de extremidade

Atualmente, o LUIS tem 2 pontos de extremidade: um para a criação e outro para análise de texto.

|Finalidade|URL|
|--|--|
|Criação|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Análise de texto (previsão de consulta)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

A tabela a seguir explica os parâmetros, indicados com chaves `{}`, na tabela anterior.

|Parâmetro|Finalidade|
|--|--|
|region|Região do Azure – a criação e a publicação têm regiões diferentes|
|appID|A ID do aplicativo LUIS usada na rota de URL e encontrada no painel do aplicativo|
|q|texto de expressão enviado do aplicativo cliente como chat bot|


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência a entidades predefinidas](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai