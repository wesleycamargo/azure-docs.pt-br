---
title: Mostrar o tráfego com Mapas do Azure | Microsoft Docs
description: Como exibir dados de tráfego em um mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 6ff7a0270509c244fc97bd04d8ba648fd262dc58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600112"
---
# <a name="show-traffic-on-the-map"></a>Mostrar tráfego no mapa

Este artigo mostra como exibir informações de tráfego e incidentes no mapa. 

## <a name="understand-the-code"></a>Compreender o código

<iframe height='456' scrolling='no' title='Mostrar tráfego em um mapa' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Mostrar tráfego em um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Você pode ver [Criar um mapa](map-create.md) para obter instruções.

O segundo bloco de código usa a função [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#settraffic) da classe mapa para renderizar os fluxos de tráfego e incidentes no mapa.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo: 
* [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#settraffic)

Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos: 
* [Interagindo com o mapa – eventos do mouse](./map-events.md)
* [Criando um mapa acessível](./map-accessibility.md)

Confira nossa [página de exemplo de código](http://aka.ms/AzureMapsSamples) para obter mais cenários de mapeamento.
