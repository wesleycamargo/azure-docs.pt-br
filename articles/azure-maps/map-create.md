---
title: Criar um mapa com Mapas do Azure | Microsoft Docs
description: Como criar um mapa de JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 09/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9759c4149c6b026837e550dcf3ab0a0156bbb736
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730002"
---
# <a name="create-a-map"></a>Criar um mapa

Este artigo mostra como criar um mapa.  

## <a name="understand-the-code"></a>Compreender o código

Há duas maneiras de construir um mapa. Você pode definir a câmera do mapa especificando o ponto central e o nível de zoom. Defina os limites da câmera do mapa especificando o ponto de limite do sudoeste e o ponto de limite do nordeste.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Definir a câmera

<iframe height='310' scrolling='no' title='Criar um mapa por meio de CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Crie um mapa via `CameraOptions` </a> pelos Serviços Baseados em Localização do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, um [objeto Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) é criado via `new atlas.Map()`. As propriedades do mapa, como centro e nível de zoom, fazem parte de [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest). `CameraOptions` pode ser definido no construtor de mapa ou por meio [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) função da classe Map.

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Defina os limites de câmera

<iframe height='310' scrolling='no' title='Criar um mapa por meio de CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Crie um mapa via `CameraBoundsOptions` </a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, uma [objeto Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) é construído por meio de `new atlas.Map()`. As propriedades do mapa como caixa delimitadora fazem parte de [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest). `CameraBoundsOptions` pode ser definida [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) função da classe Map.

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
