---
title: Mostrar resultados de pesquisa com os Mapas do Azure | Microsoft Docs
description: Como executar uma solicitação de pesquisa com mapas do Azure e exibir os resultados em um mapa de Javascrip
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b77737b16b23ed00c8f12f84e6a8558a665a7d15
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599874"
---
# <a name="show-search-results-on-the-map"></a>Mostrar resultados da pesquisa no mapa

Este artigo mostra como fazer uma solicitação de pesquisa e mostrar os resultados da pesquisa no mapa. 

## <a name="understand-the-code"></a>Compreender o código

<iframe height='500' scrolling='no' title='Mostrar resultados da pesquisa no mapa' src='//codepen.io/azuremaps/embed/KQbaeM/?height=519&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Mostrar resultados em um mapa</a> pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria e adiciona uma camada de pins de pesquisa no mapa. Você pode ver [adicionar um pin no mapa](./map-add-pin.md) para obter instruções.

O terceiro bloco de código envia um [XMLHttpRequest](https://xhr.spec.whatwg.org/) para [API de pesquisa difusa do mapas do Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

O último bloco de código analisa a resposta de entrada. Para uma resposta bem-sucedida, ele coleta as informações de latitude e longitude de cada local retornado. Adiciona todos os pontos de local para o mapa como pins e ajusta os limites do mapa para processar todos os pins.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo: 

* [API de pesquisa difusa do Mapas do Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
    
Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos: 
* [Obter informações de uma coordenada](./map-get-information-from-coordinate.md)
* [Mostrar trajetos de A para B](./map-route.md)
