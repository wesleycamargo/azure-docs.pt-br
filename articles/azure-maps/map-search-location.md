---
title: Mostrar resultados de pesquisa com os Mapas do Azure | Microsoft Docs
description: Como executar uma solicitação de pesquisa com mapas do Azure e exibir os resultados em um mapa de Javascrip
author: jingjing-z
ms.author: jinzh
ms.date: 08/26/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 540faa04573f7fa66b574f6743d42adfe855c571
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43095248"
---
# <a name="show-search-results-on-the-map"></a>Mostrar resultados da pesquisa no mapa

Este artigo mostra como fazer uma pesquisa para localização de interesse e mostrar os resultados da pesquisa no mapa. 

## <a name="understand-the-code"></a>Compreender o código

<iframe height='305' scrolling='no' title='Mostrar resultados da pesquisa no mapa (módulo de serviço)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Mostrar resultados de pesquisa em um mapa (módulo de serviço)</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de mapa e instancia um serviço de cliente. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código usa a pesquisa difusa [API de pesquisa difusa do Azure Mapas](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) para procurar o ponto de interesse. API de pesquisa difusa pode lidar com qualquer combinação de entradas difusas. A resposta do serviço de pesquisa difusa é então analisada no formato GeoJSON e pins são adicionados ao mapa para mostrar os pontos de interesse no mapa. 

O último bloco de código adiciona limites de câmera para o mapa por meio do uso da propriedade do mapa [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo: 

* [API de pesquisa difusa do Mapas do Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos: 
* [Obter informações de uma coordenada](./map-get-information-from-coordinate.md)
* [Mostrar trajetos de A para B](./map-route.md)
