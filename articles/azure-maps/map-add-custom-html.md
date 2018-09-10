---
title: Adicionar HTML nos Mapas do Azure | Microsoft Docs
description: Como adicionar um HTML personalizado a um mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5060839be244f55700b40735e598964a0b7b6709
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382038"
---
# <a name="add-custom-html-to-the-map"></a>Adicionar HTML personalizado ao mapa

Este artigo mostra como adicionar um HTML personalizado, como um arquivo de imagem para o mapa. 

## <a name="understand-the-code"></a>Compreender o código

<iframe height='466' scrolling='no' title='Adicionar HTML personalizado ao mapa - png' src='//codepen.io/azuremaps/embed/MVoeVw/?height=466&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Adicionar um HTML a um mapa - png</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria um elemento HTML de uma imagem.

O último bloco de código usa a função [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addhtml) da classe de mapa para adicionar a imagem para a posição especificada do mapa.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo: 
* [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addhtml)
    
Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos: 
* [ Mostrar resultados da pesquisa ](./map-search-location.md)
* [Obter informações de uma coordenada](./map-get-information-from-coordinate.md)

