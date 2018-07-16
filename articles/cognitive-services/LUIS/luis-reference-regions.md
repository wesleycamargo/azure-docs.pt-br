---
title: Regiões do LUIS (Reconhecimento vocal) | Microsoft Docs
titleSuffix: Azure
description: Este artigo contém listas de regiões do LUIS para o site do LUIS, assinaturas do Azure e regiões do mundo.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 86a20770178707f72cf2991ca08b6b98eaeaf0cf
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36236281"
---
# <a name="regions-and-keys"></a>Regiões e chaves

A região na qual você publica seu aplicativo LUIS corresponde à região ou ao local especificado no portal do Azure quando você cria uma chave do ponto de extremidade LUIS do Azure. Quando você [publica um aplicativo](./PublishApp.md), o LUIS gera automaticamente uma URL de ponto de extremidade para a região associada à chave. Para publicar um aplicativo LUIS em mais de uma região, é necessário ter pelo menos uma chave por região. 

## <a name="luis-website"></a>Site do LUIS
Há três sites do LUIS dependendo da sua região. É necessário criar e publicar na mesma região. 

|LUIS|Região|
|--|--|
|[www.luis.ai][www.luis.ai]|EUA<br>não Europa<br>não Austrália|
|[au.luis.ai][au.luis.ai]|Austrália|
|[eu.luis.ai][eu.luis.ai]|Europa|


## <a name="publishing-regions"></a>Regiões de publicação

Os aplicativos LUIS criados no https://www.luis.ai podem ser publicados em todos os pontos de extremidade, exceto nas regiões [europeias](#publishing-to-europe) e [australianas](#publishing-to-australia). 

O aplicativo da região de criação só pode ser publicado em uma região de publicação correspondente. Se seu aplicativo estiver, no momento, na região de criação incorreta, exporte-o e importe-o para a região de criação correta para sua região de publicação. 

 Região global | Região de criação | Região de publicação e de consulta   |   Site do LUIS | Formato da URL do ponto de extremidade   |
|-----|------|------|------|------|
| Ásia | Oeste dos EUA| Ásia Oriental     | [www.luis.ai][www.luis.ai] |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | Oeste dos EUA| Sudeste Asiático     | [www.luis.ai][www.luis.ai] |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Austrália](#publishing-to-australia) | Leste da Austrália| Leste da Austrália     |   [au.luis.ai][au.luis.ai] | https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Europa](#publishing-to-europe)| Europa Ocidental| Norte da Europa     | [eu.luis.ai][eu.luis.ai]|  https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Europa](#publishing-to-europe) | Europa Ocidental| Europa Ocidental     | [eu.luis.ai][eu.luis.ai]|  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| América do Norte | Oeste dos EUA | Leste dos EUA      |[www.luis.ai][www.luis.ai] |   https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | Oeste dos EUA | Leste dos EUA 2     | [www.luis.ai][www.luis.ai] |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | Oeste dos EUA | Centro-Sul dos Estados Unidos     | [www.luis.ai][www.luis.ai] |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| América do Norte | Oeste dos EUA | Centro-Oeste dos EUA     |[www.luis.ai][www.luis.ai] |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | Oeste dos EUA | Oeste dos EUA |  [www.luis.ai][www.luis.ai] | https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| América do Norte | Oeste dos EUA | Oeste dos EUA 2    | [www.luis.ai][www.luis.ai] |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| América do Sul | Oeste dos EUA | Sul do Brasil     | [www.luis.ai][www.luis.ai] |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-europe"></a>Publicando na Europa

Para publicar nas regiões europeias, crie os aplicativos LUIS em https://eu.luis.ai apenas. Se você tentar publicar em outro lugar usando uma chave na região Europa, o LUIS exibirá uma mensagem de aviso. Em vez disso, use https://eu.luis.ai. Os aplicativos LUIS criados em [https://eu.luis.ai][eu.luis.ai] não são migrados automaticamente para outras regiões. Exporte e importe o aplicativo LUIS para migrá-lo.

## <a name="publishing-to-australia"></a>Publicando na Austrália

Para publicar nas regiões australianas, crie os aplicativos LUIS em https://au.luis.ai apenas. Se você tentar publicar em outro lugar usando uma chave na região australiana, o LUIS exibirá uma mensagem de aviso. Em vez disso, use https://au.luis.ai. Os aplicativos LUIS criados em [https://au.luis.ai][au.luis.ai] não são migrados automaticamente para outras regiões. Exporte e importe o aplicativo LUIS para migrá-lo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência a entidades predefinidas](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai