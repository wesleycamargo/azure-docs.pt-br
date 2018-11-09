---
title: Criar um mapa com Mapas do Azure | Microsoft Docs
description: Como criar um mapa de JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 659f6def7e6bb045606b7214a4b8b4cab693117b
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248160"
---
# <a name="create-a-map"></a>Criar um mapa

Este artigo mostra maneiras de criar um mapa e animar um mapa.  

## <a name="understand-the-code"></a>Compreender o código

Há duas maneiras de construir um mapa. Você pode definir a câmera do mapa especificando o ponto central e o nível de zoom ou pode definir os limites da câmera do mapa especificando os pontos de limite sudoeste e nordeste.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Definir a câmera

<iframe height='500' scrolling='no' title='Criar um mapa por meio de CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Crie um mapa via `CameraOptions` </a> pelos Serviços Baseados em Localização do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, um [objeto Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) é criado via `new atlas.Map()` e o centro e o zoom são definidos. As propriedades do mapa, como centro e nível de zoom, fazem parte de [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest).

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Defina os limites de câmera

<iframe height='500' scrolling='no' title='Criar um mapa por meio de CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Crie um mapa via `CameraBoundsOptions` </a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, uma [objeto Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) é construído por meio de `new atlas.Map()`. As propriedades do mapa, como `CameraBoundsOptions`, podem ser definidas por meio da função [ setCamera ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) da classe Map. As propriedades de limites e preenchimento são definidas usando `setCamera`.

### <a name="animate-map-view"></a>Animar a exibição do mapa

<iframe height='500' scrolling='no' title='Animar a exibição do mapa' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/WayvbO/'>animar a exibição do mapa</a> por mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código cria um [objeto Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) por meio de `new atlas.Map()`. As propriedades do mapa, como centro e nível de zoom, fazem parte de [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest). `CameraOptions` pode ser definido no construtor de mapa ou por meio [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) função da classe Map. O [mapear estilo](https://review.docs.microsoft.com/azure/azure-maps/supported-map-styles?branch=pr-en-us-54960) é definido como `road`.

O segundo bloco de código cria uma função animate map, que anima a mudança na visualização do mapa definindo [AnimateOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.animationoptions?view=azure-iot-typescript-latest) através da função [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera). A função é disparada pelo botão 'Mapa animar' para gerar um nível de zoom aleatório após cada clique.

## <a name="try-out-the-code"></a>Experimentar o código

Examine o código de exemplo acima. Você pode editar o código JavaScript na **guia JS** à esquerda e ver a exibição do mapa na guia **Tabela de Resultado** à direita. Você também pode clicar no botão **Edit on CodePen** e editar o código no CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte exemplos de código para adicionar funcionalidade ao seu aplicativo:

> [!div class="nextstepaction"]
> [Escolher um estilo de mapa](choose-map-style.md)

> [!div class="nextstepaction"]
> [Adicionar controles de mapa](map-add-controls.md)
