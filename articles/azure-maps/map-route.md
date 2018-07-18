---
title: Mostrar direções com Mapas do Azure | Microsoft Docs
description: Como exibir as direções entre dois locais em um mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 5e9ab73ddc16517e17894cddd9bc102f3941f00c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "35766247"
---
# <a name="show-directions-from-a-to-b"></a>Mostrar direções de A para B 

Este artigo mostra como fazer uma solicitação de rota e mostrar a rota no mapa. 

## <a name="understand-the-code"></a>Compreender o código

<iframe height='500' scrolling='no' title='Mostrar instruções de A para B em um mapa' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Mostrar direções de A para B em um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria e adiciona pins no mapa para representar os pontos de início e de fim da rota. Você pode ver [adicionar um pin no mapa](map-add-pin.md) para obter instruções.

O terceiro bloco de código usa a função [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) da classe de mapa para definir a caixa delimitadora do mapa com base nos pontos de início e de fim da rota.

O quarto bloco de código envia um [XMLHttpRequest](https://xhr.spec.whatwg.org/) para [API de Rota dos Mapas do Azure](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

O último bloco de código analisa a resposta de entrada. Para uma resposta bem-sucedida, ele coleta as informações de latitude e longitude de cada ponto de rota. Ele cria uma matriz de linhas conectando cada ponto de rota até o ponto de rota subsequente. Ele adiciona todas essas linhas ao mapa para renderizar a rota. Você pode consultar [adicionar uma linha no mapa](./map-add-shape.md#addALine) para obter instruções.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo: 

* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)

Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos: 
* [Mostrar tráfego no mapa](./map-show-traffic.md)
* [Interagindo com o mapa – eventos do mouse](./map-events.md)
