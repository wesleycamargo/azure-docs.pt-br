---
title: Criar um mapa com Mapas do Azure | Microsoft Docs
description: Como criar um mapa de JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b86f29e4d3faa1382ac3a79ed828855a5d9f6d7f
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/23/2018
ms.locfileid: "42747212"
---
# <a name="create-a-map"></a>Criar um mapa

Este artigo mostra como criar um mapa.  

## <a name="understand-the-code"></a>Compreender o código

Há duas maneiras de construir um mapa. Você pode definir a câmera do mapa especificando o ponto central e o nível de zoom ou definir os limites da câmera do mapa especificando o ponto delimitador sudoeste e ponto delimitador nordeste.

<a id="setCameraOptions"></a>

### <a name="setting-the-camera"></a>Definindo a câmera

<iframe height='310' scrolling='no' title='Criar um mapa por meio de CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Criar um mapa por meio de CameraOptions</a> pelo Azure LBS (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, um [objeto de mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) é criado por meio de `new atlas.Map()`. As propriedades do mapa, como centro e nível de zoom, fazem parte de [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest). CameraOptions podem ser definidas no construtor de mapa ou por meio da função [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) da classe de mapa.

<a id="setCameraBoundsOptions"></a>

### <a name="setting-the-camera-bounds"></a>Definindo os limites da câmera

<iframe height='310' scrolling='no' title='Criar um mapa por meio de CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Criar um mapa por meio de CameraBoundsOptions</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, um [objeto de mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) é construído por meio de `new atlas.Map()`. As propriedades do mapa como caixa delimitadora fazem parte de [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest). CameraBoundsOptions podem ser definidas por meio da função [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) da classe de mapa.

## <a name="try-out-the-code"></a>Experimentar o código 

Examine o código de exemplo acima. Você pode editar o código JavaScript na guia JS à esquerda e ver as alterações de exibição do mapa na guia Resultados à direita. Você também pode clicar no botão “Editar no CodePen” e editar o código em CodePen. 

<a id="relatedReference"></a>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo: 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds)
    
Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos: 
* [Adicionar um PIN](./map-add-pin.md)
* [Adicionar um pop-up](map-add-popup.md)
    

