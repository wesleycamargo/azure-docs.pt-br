---
title: Adicionar um pop-up com Mapas do Azure | Microsoft Docs
description: Como adicionar um pop-up a um mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 09/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 76a7e230491d5e524a1d73437a56d12594cfebe2
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127430"
---
# <a name="add-a-popup-to-the-map"></a>Adicionar um pop-up ao mapa

Este artigo mostra como adicionar um pop-up a um mapa.  

## <a name="understand-the-code"></a>Compreender o código

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Adicionar um pop-up a um mapa' src='//codepen.io/azuremaps/embed/zRyKxj/?height=545&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/zRyKxj/'>Adicionar um pop-up a um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto Map. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria um pin e o adiciona ao mapa. Você pode ver [adicionar um pin ao mapa](./map-add-pin.md) para obter instruções.

O terceiro bloco de código cria o conteúdo a ser exibido em um pop-up. O conteúdo pop-up é o elemento HTML.

O quarto bloco de código cria um [objeto pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) por meio de `new atlas.Popup()`. Propriedades de pop-up, como o conteúdo e a posição são parte de [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.popupoptions?view=azure-iot-typescript-latest). PopupOptions podem ser definidas no construtor de pop-up ou por meio da função [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) da classe do pop-up.

O último bloco de código usa a função [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener) da classe de mapa para escutar eventos de mouse sobre os pins e usa a função [abrir](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) da classe do pop-up para abrir o pop-up se o evento ocorrer.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Consulte os seguintes artigos excelentes para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [ Adicione uma forma ](./map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicionar um HTML personalizado](./map-add-custom-html.md)
