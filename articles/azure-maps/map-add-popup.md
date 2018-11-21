---
title: Adicionar um pop-up com Mapas do Azure | Microsoft Docs
description: Como adicionar um pop-up a um mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 11/09/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0be10c155398133887fadb1fe9954068f3afb9d9
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568107"
---
# <a name="add-a-popup-to-the-map"></a>Adicionar um pop-up ao mapa

Este artigo mostra como adicionar um pop-up para um ponto em um mapa.

## <a name="understand-the-code"></a>Compreender o código

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Adicionar um pop até usando Azure Mapas' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Adicionar um pop-up usando os Mapas do Azure</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções. Também cria conteúdo HTML para ser exibido dentro do popup.

O segundo bloco de código cria um objeto de fonte de dados usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Um ponto é um [recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) da [aponte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) classe. Um objeto de ponto com um nome e propriedades de descrição é criado e adicionado à fonte de dados.

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar dados com base em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa.  Uma camada de símbolo é criada no terceiro bloco de código. A fonte de dados é adicionada à camada de símbolo, que é então adicionada ao mapa.

O quarto bloco de código cria um [objeto pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) por meio de `new atlas.Popup()`. Propriedades de pop-up, como position e pixelOffset, fazem parte de [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.popupoptions?view=azure-iot-typescript-latest). PopupOptions podem ser definidos no construtor de pop-up ou via [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) função da classe pop-up. Um ouvinte de evento `mouseover` para a camada de símbolo é criado.

O último bloco de código cria uma função que é acionada pelo ouvinte de evento `mouseover`. Ele define o conteúdo e propriedades de pop-up e adiciona o objeto de pop-up no mapa.

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
