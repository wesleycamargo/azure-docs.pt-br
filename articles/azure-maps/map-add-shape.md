---
title: Adicionar uma forma com Mapas do Azure | Microsoft Docs
description: Como adicionar uma forma a um mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 6b78c7f5d7d1b5ad4db9401bf2138502c9bbd397
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121883"
---
# <a name="add-a-shape-to-a-map"></a>Adicionar uma forma a um mapa

Este artigo mostra como adicionar uma linha, um círculo e um polígono ao mapa. 

<a id="addALine"></a>

## <a name="add-a-line"></a>Adicionar uma linha

<iframe height='500' scrolling='no' title='Adicionar uma linha a um mapa' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Adicionar uma linha a um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de Mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, uma linha é criada. Uma linha é um [Recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de LineString com LineStringProperties como propriedade de Recurso. Use `new atlas.data.Feature(new atlas.data.LineString())` para criar uma linha e definir suas propriedades.

Uma camada de linhas é uma matriz de linhas. O último bloco de código usa a função [addLineStrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings) da classe de mapa para adicionar a camada de linhas ao mapa e definir as propriedades da camada de linhas. Consulte as propriedades de uma camada de linhas em [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.linestringlayeroptions?view=azure-iot-typescript-latest).

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Adicionar um círculo

<iframe height='500' scrolling='no' title='Adicionar um círculo a um mapa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Adicionar um círculo a um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de Mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um círculo é criado. Um círculo é um [Recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de [Ponto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) com [CircleProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.circleproperties?view=azure-iot-typescript-latest) como sua propriedade de recurso. Use `new atlas.data.Feature(new atlas.data.Point())` para criar um círculo e definir suas propriedades.

Uma camada de círculos é uma matriz de círculos. O último bloco de código usa a função [addCircle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcircles) da classe de mapa para adicionar a camada de círculos ao mapa e definir as propriedades da camada de círculos. Consulte as propriedades de uma camada de círculos em [CircleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.circlelayeroptions?view=azure-iot-typescript-latest).

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Adicionar um polígono

<iframe height='500' scrolling='no' title='Adicionar um polígono a um mapa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Adicionar um polígono a um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de Mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um polígono é criado. Um polígono é um [Recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de [Polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) com [PolygonProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonproperties?view=azure-iot-typescript-latest) como sua propriedade de recurso. Use `new atlas.data.Feature(new atlas.data.Polygon())` para criar um polígono e definir suas propriedades. Fornece coordenadas ordenadas do caminho do polígono no construtor de polígonos.

Uma camada de polígonos é uma matriz de polígonos. O último bloco de código usa a função [addPolygons](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpolygons) da classe de mapa para adicionar a camada de polígonos ao mapa e definir suas propriedades. Consulte as propriedades de uma camada de polígonos em [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonlayeroptions?view=azure-iot-typescript-latest).

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [ Adicione um HTML personalizado ](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [ Mostrar resultados da pesquisa ](./map-search-location.md)
