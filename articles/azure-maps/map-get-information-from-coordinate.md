---
title: Mostrar informações sobre uma coordenada com mapas do Azure | Microsoft Docs
description: Como exibir informações sobre um endereço no mapa quando um usuário seleciona uma coordenada
author: jingjing-z
ms.author: jinzh
ms.date: 09/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: bf44437f4d0b60a5d56c2be29418b7132346da2e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45732287"
---
# <a name="get-information-from-a-coordinate"></a>Obter informações de uma coordenada

Este artigo mostra como fazer uma pesquisa inversa de endereço que mostra o endereço de um local de pop-up clicado.

Há duas maneiras de fazer uma pesquisa inversa de endereço. Uma delas é a consulta da [API de Pesquisa do Endereço Reverso do Azure Mapas](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) por meio de um módulo de serviço. A outra maneira é fazer uma [XMLHttpRequest](https://xhr.spec.whatwg.org/) à API para encontrar um endereço. As duas formas são pesquisadas abaixo.

## <a name="make-a-reverse-search-request-via-service-module"></a>Fazer uma solicitação de pesquisa reversa por meio do módulo de serviço

<iframe height='500' scrolling='no' title='Obter informações de uma coordenada (Módulo de Serviço)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Obter informações de uma coordenada (Módulo de Serviço)</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

A linha no segundo bloco de código cria uma instância de um cliente de serviço.

O terceiro bloco de código atualiza o estilo do cursor do mouse para um ponteiro.

O quarto bloco de código cria uma janela pop-up usando [abrir](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Você pode ver [adicionar um popup no mapa](./map-add-popup.md) para obter instruções.

O último bloco de código [adiciona um ouvinte de evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener) para cliques do mouse. Com um clique do mouse, ele cria uma consulta de pesquisa com as coordenadas do ponto clicado. Em seguida, usa o ponto de extremidade [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) do mapa para consultar o endereço das coordenadas.

Para uma resposta com êxito, ele coleta o endereço ao local clicado e define o conteúdo de pop-up e posição via função [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) da classe pop-up.

## <a name="make-a-reverse-search-request-via-xmlhttprequest"></a>Fazer uma solicitação de pesquisa inversa via XMLHttpRequest

<iframe height='500' scrolling='no' title='Obter informações de uma coordenada' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as informações de Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get de uma coordenada </a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código atualiza o estilo do cursor do mouse em um ponteiro.

O terceiro bloco de código cria uma janela pop-up usando [abrir](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Você pode ver [adicionar um popup no mapa](./map-add-popup.md) para obter instruções.

O último bloco de código adiciona um ouvinte de evento de cliques do mouse. Após um clique do mouse, ele envia um [XMLHttpRequest](https://xhr.spec.whatwg.org/) para [a API de Pesquisa do Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Para uma resposta bem-sucedida, ele coleta o endereço ao local clicado e define o conteúdo de pop-up e posição via função [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) da classe pop-up

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
