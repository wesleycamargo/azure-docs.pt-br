---
title: Adicionar um pin com Mapas do Azure | Microsoft Docs
description: Como adicionar um pin para um mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 35b22e28a6a339af6f6f6e8db0655f2ae6de0118
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46122161"
---
# <a name="add-pins-to-the-map"></a>Adicionar pins ao mapa

Este artigo mostra como adicionar um pin para um mapa.  

## <a name="understand-the-code"></a>Compreender o código

<iframe height='500' scrolling='no' title='Adicionar um pin para um mapa' src='//codepen.io/azuremaps/embed/ZrVpEa/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/ZrVpEa/'>Adicionar um pin para um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um pin é criado e adicionado ao mapa. Um pin é um [Recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de [Ponto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) com [PinProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.pinproperties?view=azure-iot-typescript-latest) como sua propriedade de recurso. Use `new atlas.data.Feature(new atlas.data.Point())` para criar um pin e definir suas propriedades. Uma camada de pin é uma matriz de pins. Use a função [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) da classe de mapa para adicionar uma camada de pin para o mapa e definir as propriedades da camada de pin. Consulte as propriedades de uma camada de pin em [PinLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.pinlayeroptions?view=azure-iot-typescript-latest).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar um pop-up](./map-add-popup.md)

> [!div class="nextstepaction"]
> [ Adicione uma forma ](./map-add-shape.md)