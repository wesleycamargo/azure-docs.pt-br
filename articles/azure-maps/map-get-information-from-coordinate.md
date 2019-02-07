---
title: Mostrar informações sobre uma coordenada com mapas do Azure | Microsoft Docs
description: Como exibir informações sobre um endereço no mapa quando um usuário seleciona uma coordenada
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 20616adf649924a13e80411aa5135889a175f442
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750181"
---
# <a name="get-information-from-a-coordinate"></a>Obter informações de uma coordenada

Este artigo mostra como fazer uma pesquisa inversa de endereço que mostra o endereço de um local de pop-up clicado.

Há duas maneiras de fazer uma pesquisa inversa de endereço. Uma delas é a consulta da [API de Pesquisa do Endereço Reverso do Azure Mapas](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) por meio de um módulo de serviço. A outra maneira é fazer uma [XMLHttpRequest](https://xhr.spec.whatwg.org/) à API para encontrar um endereço. As duas formas são pesquisadas abaixo.

## <a name="make-a-reverse-search-request-via-service-module"></a>Fazer uma solicitação de pesquisa reversa por meio do módulo de serviço

<iframe height='500' scrolling='no' title='Obter informações de uma coordenada (Módulo de Serviço)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Obter informações de uma coordenada (Módulo de Serviço)</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

A linha no segundo bloco de código instancia um serviço de cliente.

O terceiro bloco de código atualiza o estilo do cursor do mouse para um ponteiro e o objeto [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Você pode ver [adicionar um popup no mapa](./map-add-popup.md) para obter instruções.

O quarto bloco de código adiciona um [ouvinte de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para cliques do mouse. Com um clique do mouse, ele cria uma consulta de pesquisa com as coordenadas do ponto clicado. Em seguida, ele usa o ponto de extremidade [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest) do mapa para consultar o endereço das coordenadas.

Para uma resposta bem-sucedida, ele coleta o endereço do local clicado e define o conteúdo e a posição do popup por meio da função [ setOptions ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) da classe pop-up.

A mudança de cursor, um objeto pop-up e o evento click são todos criados no [listener de eventos de carregamento do mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o mapa carregue totalmente antes que as informações de coordenadas possam ser recuperadas.

## <a name="make-a-reverse-search-request-via-xmlhttprequest"></a>Fazer uma solicitação de pesquisa inversa via XMLHttpRequest

<iframe height='500' scrolling='no' title='Obter informações de uma coordenada' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as informações de Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get de uma coordenada </a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código acima constrói um objeto Map. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código atualiza o estilo do cursor do mouse para um ponteiro e o objeto [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Você pode ver [adicionar um popup no mapa](./map-add-popup.md) para obter instruções.

O terceiro bloco de código adiciona um ouvinte de evento para cliques do mouse. Com um clique do mouse, ele envia um [XMLHttpRequest](https://xhr.spec.whatwg.org/) para [API de pesquisa de endereço reverso do Azure Mapas](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para consultar o endereço de coordenadas clicadas. Para uma resposta bem-sucedida, ele coleta o endereço do local clicado e define o conteúdo e a posição do popup por meio da função [ setOptions ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) da classe pop-up.

A mudança de cursor, um objeto pop-up e o evento click são todos criados no [listener de eventos de carregamento do mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o mapa carregue totalmente antes que as informações de coordenadas possam ser recuperadas.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Mostrar trajetos de A para B](./map-route.md)

> [!div class="nextstepaction"]
> [Mostrar tráfego](./map-show-traffic.md)
