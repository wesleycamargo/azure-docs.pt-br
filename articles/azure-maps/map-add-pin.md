---
title: Adicionar símbolos e marcadores com o Azure Mapas | Microsoft Docs
description: Como adicionar símbolos e marcadores a um mapa de Javascript
author: walsehgal
ms.author: v-musehg
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c56ac35f49c364b7b0f2ad26b82b178411419414
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52282678"
---
# <a name="add-symbols-and-markers-to-a-map"></a>Adicionar símbolos e marcadores a um mapa

Este artigo mostra como adicionar símbolos e marcadores a um mapa usando uma fonte de dados.

## <a name="add-a-symbol-marker"></a>Adicionar um marcador de símbolo

<iframe height='500' scrolling='no' title='Mudar localização de fixação' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Confira a Caneta <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Mudar localização de fixação</a> por Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código acima constrói um objeto Map. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um objeto de fonte de dados é criado usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Um ponto é então criado e adicionado à fonte de dados. Um ponto é um [Recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de [Ponto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest).

O terceiro bloco de código cria um [ouvinte de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) e atualiza as coordenadas do ponto após clique do mouse, usando o método [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest#setcoordinates) da classe shape.

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar dados com base em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa.  A fonte de dados, o ouvinte de eventos de clique e a camada de símbolo são criados e adicionados ao mapa dentro da função [ouvinte de evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o ponto seja exibido após o mapa ser totalmente carregado.

## <a name="add-a-custom-symbol"></a>Adicionar um símbolo personalizado

<iframe height='500' scrolling='no' title='DataSource HTML' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>DataSource HTML</a> pelos Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código adiciona um [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) ao mapa usando a propriedade [markers](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) da classe [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). O HtmlMarker é adicionado ao mapa de dentro da função de [ouvinte de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que ele seja exibido depois do carregamento total do mapa.

## <a name="add-bubble-markers"></a>Adicionar marcadores de bolha

<iframe height='500' scrolling='no' title='DataSource BubbleLayer' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>DataSource BubbleLayer</a> pelos Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, uma matriz de posições de é definida e um objeto [MultiPoint](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.multipoint?view=azure-iot-typescript-latest) é criado. Um objeto de fonte de dados é então criado usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) e o objeto MultiPoint é adicionado à fonte de dados.

Uma [camada de bolha](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) renderiza dados baseados em ponto encapsulados na [fonte de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como círculos no mapa. O último bloco de código cria uma camada de bolha e adiciona-a ao mapa. Confira as propriedades de uma camada de bolha em [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

O objeto MultiPoint, a fonte de dados e a camada de bolha são criados e adicionados ao mapa dentro da função [ouvinte de evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o círculo seja exibido após o mapa ser totalmente carregado.

## <a name="add-bubble-markers-with-label"></a>Adicionar marcadores de bolha com rótulo

<iframe height='500' scrolling='no' title='DataSource MultiLayer' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>DataSource MultiLayer</a> pelos Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O código acima mostra como visualizar e rotular dados no mapa. O primeiro bloco de código acima constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria um objeto [point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest). Em seguida, ele cria um objeto de fonte de dados usando a classe [fonte de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) e adiciona o ponto à fonte de dados.

Uma [camada de bolha](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) renderiza dados baseados em ponto encapsulados na [fonte de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como círculos no mapa. O terceiro bloco de código cria uma camada de bolha e adiciona-a ao mapa. Confira as propriedades de uma camada de bolha em [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar dados com base em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. O último bloco de código cria e adiciona uma camada de símbolo ao mapa que renderiza o rótulo de texto para a bolha. Veja as propriedades de uma camada de símbolo em [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

A fonte de dados e as camadas são criadas e adicionados ao mapa dentro da função [ouvinte de evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que os dados sejam exibidos após o mapa ser totalmente carregado.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar um pop-up](./map-add-popup.md)

> [!div class="nextstepaction"]
> [ Adicione uma forma ](./map-add-shape.md)