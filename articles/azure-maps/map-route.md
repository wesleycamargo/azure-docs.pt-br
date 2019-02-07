---
title: Mostrar direções com Mapas do Azure | Microsoft Docs
description: Como exibir as direções entre dois locais em um mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 183194d172b9ac11d4f1c5cb1324f7a09f4a157b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756301"
---
# <a name="show-directions-from-a-to-b"></a>Mostrar direções de A para B

Este artigo mostra como fazer uma solicitação de rota e mostrar a rota no mapa.

Há duas maneiras de fazer isso. A primeira maneira é a consultar a [API de Rotas do Azure Mapas](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) por meio de um módulo de serviço. A segunda maneira é fazer uma [XMLHttpRequest](https://xhr.spec.whatwg.org/) para a API. As duas formas são discutidas abaixo.

## <a name="query-the-route-via-service-module"></a>A rota por meio do módulo de serviço de consulta

<iframe height='500' scrolling='no' title='Mostrar direções de A para B em um mapa (Módulo de Serviço)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Mostrar rotas de A para B em um mapa (Módulo de Serviço)</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

A linha no segundo bloco de código instancia um serviço de cliente.

O terceiro cria e adiciona um objeto [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) ao mapa.

 Uma linha é um [recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de LineString. Um [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderiza objetos de linha agrupados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como linhas no mapa. O quarto bloco de código cria e adiciona uma camada de linha ao mapa. Consulte as propriedades de uma camada de linhas em [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.deprecated.linestringlayeroptions?view=azure-iot-typescript-latest).

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar dados com base em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. O quinto bloco de código cria e adiciona uma camada de símbolo ao mapa.

O sexto bloco de código cria o objeto inicial e final [pontos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) e adiciona-os ao objeto dataSource.

O sétimo bloco de código define os limites do mapa usando a propriedade [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do Map.

O último bloco de código consulta o serviço de roteamento do Azure Mapas, que faz parte do [módulo de serviço](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1). O método [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest) é usado para obter uma rota entre os pontos inicial e final. A resposta é então analisada no formato GeoJSON usando o método [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest). Em seguida, renderiza a resposta como uma rota no mapa. Para mais informações sobre como adicionar uma linha ao mapa, consulte [adicionar uma linha no mapa](./map-add-shape.md#addALine).

A consulta de rota, a fonte de dados, o símbolo e as camadas de linha e os limites da câmera são criados e definidos no [ouvinte de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) do mapa para garantir que os resultados sejam exibidos após o carregamento total do mapa.

## <a name="query-the-route-via-xmlhttprequest"></a>Consulta a rota via XMLHttpRequest

<iframe height='500' scrolling='no' title='Mostrar instruções de A para B em um mapa' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Mostrar direções de A para B em um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria e adiciona um objeto [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) ao mapa.

O terceiro bloco de código cria os pontos de início e destino para a rota e os adiciona à fonte de dados. Você pode consultar [adicionar um pin no mapa](map-add-pin.md) para obter instruções sobre como usar [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest).

 Um [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderiza objetos de linha agrupados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como linhas no mapa. O quarto bloco de código cria e adiciona uma camada de linha ao mapa. Consulte as propriedades de uma camada de linha em [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar dados com base em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. O quinto bloco de código cria e adiciona uma camada de símbolo ao mapa. Veja as propriedades de uma camada de símbolo em [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

O próximo bloco de código cria pontos `SouthWest` e `NorthEast` a partir dos pontos inicial e de destino e define os limites do mapa usando a propriedade [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do Mapa.

O último bloco de código envia um [XMLHttpRequest](https://xhr.spec.whatwg.org/) para [a API do Azure Mapas Route](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Em seguida, ele analisa a resposta de entrada. E, para obter uma resposta bem-sucedida, ele coleta as informações de latitude e longitude de cada ponto de rota e cria uma matriz de linhas conectando esses pontos. Em seguida, ele adiciona todas essas linhas ao dataSource para renderizar a rota no mapa. Você pode consultar [adicionar uma linha no mapa](./map-add-shape.md#addALine) para obter instruções.

A consulta de rota, a fonte de dados, o símbolo e as camadas de linha e os limites da câmera são criados e definidos no [ouvinte de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) do mapa para garantir que os resultados sejam exibidos após o carregamento total do mapa.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Mostrar tráfego no mapa](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interagindo com o mapa – eventos do mouse](./map-events.md)
