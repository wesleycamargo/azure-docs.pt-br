---
title: Mostrar direções com Mapas do Azure | Microsoft Docs
description: Como exibir as direções entre dois locais em um mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: ed522779f5a86e38ee12a246cea9ac85d0379f9e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729025"
---
# <a name="show-directions-from-a-to-b"></a>Mostrar direções de A para B

Este artigo mostra como fazer uma solicitação de rota e mostrar a rota no mapa.

Há duas maneiras de fazer isso. A primeira maneira é a consultar a [API de Rotas do Azure Mapas](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) por meio de um módulo de serviço. A segunda maneira é fazer uma [XMLHttpRequest](https://xhr.spec.whatwg.org/) para a API. As duas formas são discutidas abaixo.

## <a name="query-the-route-via-service-module"></a>A rota por meio do módulo de serviço de consulta

<iframe height='500' scrolling='no' title='Mostrar direções de A para B em um mapa (Módulo de Serviço)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Mostrar rotas de A para B em um mapa (Módulo de Serviço)</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

A linha no segundo bloco de código cria uma instância de um cliente de serviço.

O terceiro bloco de código inicializa a [linha String Layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings) no mapa.

O quarto bloco de código cria e adiciona pins no mapa para representar o ponto de partida e final da rota. Você pode consultar [adicionar um pin no mapa](map-add-pin.md) para obter instruções sobre como usar [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

O próximo bloco de código usa a função [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) da classe do mapa para definir a caixa delimitadora do mapa com base no ponto inicial e final da rota.

O sexto bloco de código constrói uma consulta de rota.

O último bloco de código consulta o serviço de roteamento do Azure Maps por meio do método [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) para obter uma rota entre o ponto inicial e o destino. A resposta é então analisada no formato GeoJSON usando o método [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes). Ele adiciona todas essas linhas ao mapa para renderizar a rota. Você pode ver [adicionar uma linha do mapa](./map-add-shape.md#addALine) para obter mais informações.

## <a name="query-the-route-via-xmlhttprequest"></a>Consulta a rota via XMLHttpRequest

<iframe height='500' scrolling='no' title='Mostrar instruções de A para B em um mapa' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Mostrar direções de A para B em um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria e adiciona pins no mapa para representar os pontos de início e de fim da rota. Você pode consultar [adicionar um pin no mapa](map-add-pin.md) para obter instruções sobre como usar [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

O terceiro bloco de código usa a função [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) da classe de mapa para definir a caixa delimitadora do mapa com base nos pontos de início e de fim da rota.

O quarto bloco de código envia um [XMLHttpRequest](https://xhr.spec.whatwg.org/) para [API de Rota dos Mapas do Azure](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

O último bloco de código analisa a resposta de entrada. Para uma resposta bem-sucedida, ele coleta as informações de latitude e longitude de cada ponto de rota. Ele cria uma matriz de linhas conectando cada ponto de rota até o ponto de rota subsequente. Ele adiciona todas essas linhas ao mapa para renderizar a rota. Você pode consultar [adicionar uma linha no mapa](./map-add-shape.md#addALine) para obter instruções.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Mostrar tráfego no mapa](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interagindo com o mapa – eventos do mouse](./map-events.md)
