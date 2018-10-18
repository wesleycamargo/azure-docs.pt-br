---
title: Mostrar resultados de pesquisa com os Mapas do Azure | Microsoft Docs
description: Como realizar uma solicitação de pesquisa com os Mapas do Azure, exiba os resultados em um mapa Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 78ffa42bcf57b7163afc13b2550abdbae240ef00
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729219"
---
# <a name="show-search-results-on-the-map"></a>Mostrar resultados da pesquisa no mapa

Este artigo mostra como fazer uma pesquisa para localização de interesse e mostrar os resultados da pesquisa no mapa.

Há duas maneiras de procurar um local de interesse. Uma maneira é usar um módulo de serviço para fazer uma solicitação de pesquisa. A outra maneira é fazer uma solicitação de pesquisa por meio de um [XMLHttpRequest](https://xhr.spec.whatwg.org/) à [API de pesquisa difusa do Azure Mapas](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). As duas formas são discutidas abaixo.

## <a name="make-a-search-request-via-service-module"></a>Fazer uma solicitação de pesquisa por meio do módulo de serviço

<iframe height='500' scrolling='no' title='Mostrar resultados da pesquisa no mapa (módulo de serviço)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Mostrar resultados de pesquisa em um mapa (módulo de serviço)</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código constrói um objeto de mapa e inicializa o serviço do cliente. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código usa o [API de pesquisa difusa do Azure Mapas](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) para procurar o ponto de interesse. API de pesquisa difusa pode lidar com qualquer combinação de entradas difusas. A resposta do serviço de pesquisa difusa, em seguida, é analisada em GeoJSON formato usando o [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest#geojsonsearchresponse) método. Os pinos, em seguida, são adicionados ao mapa para mostrar os pontos de interesse no mapa.

O último bloco de código ajusta os limites de câmera para o mapa, usando o a propriedade do [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) do Mapa.

## <a name="make-a-search-request-via-xmlhttprequest"></a>Fazer uma solicitação de pesquisa via XMLHttpRequest

<iframe height='500' scrolling='no' title='Mostrar resultados da pesquisa no mapa' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Mostrar resultados em um mapa</a> pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código adiciona a camada de resultados de pesquisa no mapa. A camada de resultados de pesquisa exibirá os resultados da pesquisa como fixa no mapa. PINs são adicionados usando [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

O terceiro bloco de código envia uma [XMLHttpRequest](https://xhr.spec.whatwg.org/) à [API de pesquisa difusa de mapas do Azure Mapas](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) para procurar o ponto de interesse. API de pesquisa difusa pode lidar com qualquer combinação de entradas difusas.

O último bloco de código analisa a resposta e ajusta o ajusta os limites de câmera para o mapa, usando o mapa [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) para renderizar os pinos de resultado.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a **Pesquisa Difusa**:

> [!div class="nextstepaction"]
> [API de pesquisa difusa do Mapas do Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Obter informações de uma coordenada](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Mostrar trajetos de A para B](./map-route.md)
