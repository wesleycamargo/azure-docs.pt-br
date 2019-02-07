---
title: Mostrar o tráfego com Mapas do Azure | Microsoft Docs
description: Como exibir dados de tráfego em um mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 11/10/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7cd7c0dbb375dad78927183dbaffe574a0dc10c2
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753532"
---
# <a name="show-traffic-on-the-map"></a>Mostrar tráfego no mapa

Este artigo mostra como exibir informações de tráfego e incidentes no mapa.

## <a name="understand-the-code"></a>Compreender o código

<iframe height='456' scrolling='no' title='Mostrar tráfego em um mapa' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Mostrar tráfego em um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de mapa. Você pode ver [criar um mapa](map-create.md) para obter instruções.

O segundo bloco de código usa a função [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) dentro da função [event listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) do mapa para renderizar os fluxos de tráfego e incidentes no mapa.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para exemplos de código completo:

> [!div class="nextstepaction"]
> [Página de exemplo de código](https://aka.ms/AzureMapsSamples)

Aprimore suas experiências de usuário:

> [!div class="nextstepaction"]
> [Mapeie a interação com eventos do mouse](./map-events.md)

> [!div class="nextstepaction"]
> [Criando um mapa acessível](./map-accessibility.md)
