---
title: Adicionar uma forma com Mapas do Azure | Microsoft Docs
description: Como adicionar uma forma a um mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 505514d836c7b3244464ac53d268cc346a9321ad
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263325"
---
# <a name="add-a-shape-to-a-map"></a>Adicionar uma forma a um mapa

Este artigo mostra como adicionar um [forma](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) para as propriedades de mapa e atualização de uma forma existente no mapa.

<a id="addALine"></a>

## <a name="add-a-line"></a>Adicionar uma linha

<iframe height='500' scrolling='no' title='Adicionar uma linha a um mapa' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Adicionar uma linha a um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código no código acima constrói um objeto Map. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um objeto de fonte de dados é criado usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Uma linha é um [recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de LineString. Em seguida, ele cria e encapsula uma linha no objeto de classe [Forma](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) usando `new atlas.Shape(new atlas.data.Feature((new atlas.data.LineString()))` e a adiciona à fonte de dados.

Um [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderiza objetos de linha envolvidos na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). O último bloco de código cria e adiciona uma camada de linha ao mapa. Consulte as propriedades de uma camada de linhas em [LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.deprecated.linestringlayeroptions?view=azure-iot-typescript-latest). A origem de dados e a camada de linha são criadas e adicionadas ao mapa dentro da função [listener de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que a linha seja exibida após o carregamento total do mapa.

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Adicionar um círculo

<iframe height='500' scrolling='no' title='Adicionar um círculo a um mapa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Adicionar um círculo a um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código no código acima constrói um objeto Map. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um objeto de fonte de dados é criado usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Um círculo é um [Recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) do [Ponto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest). Em seguida, ele cria e envolve um círculo no objeto de classe [Forma](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) usando `new atlas.Shape(new atlas.data.Feature(new atlas.data.Point()))` e o adiciona à fonte de dados.

Um [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) renderiza dados agrupados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) no mapa. O último bloco de código cria e adiciona uma camada de polígono ao mapa. Consulte as propriedades de uma camada de polígonos em [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A fonte de dados e a camada de polígonos são criadas e adicionadas ao mapa dentro da função [listener de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o círculo seja exibido após o carregamento total do mapa.

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Adicionar um polígono

Existem duas maneiras diferentes de adicionar um polígono ao mapa. Ambos são explicadas nos exemplos a seguir.

### <a name="use-polygon-layer"></a>Usar a camada de polígono 

<iframe height='500' scrolling='no' title='Adicionar um polígono a um mapa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Adicionar um polígono a um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um objeto de fonte de dados é criado usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Um [Polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) é um [Recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) da classe [Polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest). `new atlas.Shape(new atlas.data.Feature(new atlas.data.Polygon()))` é usado para criar um polígono com coordenadas ordenadas em um objeto de classe [Forma](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest). O objeto de forma é então adicionado à fonte de dados.

Um [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) renderiza dados agrupados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) no mapa. O último bloco de código cria e adiciona uma camada de polígono ao mapa. Consulte as propriedades de uma camada de polígonos em [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A origem de dados e a camada de polígonos são criadas e adicionadas ao mapa dentro da função [listener de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o polígono seja exibido após o carregamento total do mapa.

### <a name="use-polygon-and-line-layer"></a>Usar a camada de polígonos e linhas

<iframe height='500' scrolling='no' title='Camada de polígonos e linhas para adicionar o polígono' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja o Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polígono e a camada de linha para adicionar o polígono</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um objeto de fonte de dados é criado usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Um [Polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) é um [Recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) do [polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest). `new atlas.Shape(new atlas.data.Feature(new atlas.data.Polygon()))` é usado para criar um polígono com coordenadas ordenadas em um objeto de classe [Forma](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest). O objeto de forma é então adicionado à fonte de dados.

Um [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) renderiza dados agrupados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) no mapa. Consulte as propriedades de uma camada de polígonos em [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Um [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) é uma matriz de linhas. Consulte as propriedades de uma camada de linhas em [LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.deprecated.linestringlayeroptions?view=azure-iot-typescript-latest). O terceiro bloco de código cria camadas de polígonos e linhas.

O último bloco de código adiciona as camadas de polígono e linha ao mapa. A origem de dados e as camadas são criadas e incluídas no mapa dentro da função [listener de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o polígono seja exibido após o carregamento total do mapa.

## <a name="update-a-shape"></a>Uma forma de atualização

Uma classe Shape envolve um [Geometry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) ou [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) e facilita a atualização e manutenção deles.
`new Shape(data: Feature<data.Geometry, any>)` constrói um objeto de forma e o inicializa com o recurso especificado.

<br>

<iframe height='500' scrolling='no' title='Atualizar propriedades de forma' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Pena <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Atualizar propriedades de forma</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código acima constrói um objeto Map. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

Um ponto é um [recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) dos [aponte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) a classe. O segundo bloco de código inicializa o valor do raio para o elemento de controle deslizante de HTML e, em seguida, constrói e envolve um objeto de ponto em um objeto de classe [Forma](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest).

O terceiro bloco de código cria uma função que pega o valor do elemento slider do intervalo HTML e altera o valor do raio usando o método [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest#addproperty) da classe shape.

No quarto bloco de código, um objeto de fonte de dados é criado usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). O ponto é adicionado à fonte de dados.

Um [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) renderiza dados agrupados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) no mapa. O terceiro bloco de código cria uma camada de polígono. Consulte as propriedades de uma camada de polígonos em [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A origem de dados, o ouvinte de eventos de clique e a camada de polígonos são criados e adicionados ao mapa dentro da função [listener de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o ponto seja exibido após o carregamento total do mapa.

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [ Adicione um HTML personalizado ](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [ Mostrar resultados da pesquisa ](./map-search-location.md)
