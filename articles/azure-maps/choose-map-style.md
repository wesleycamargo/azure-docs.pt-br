---
title: Funcionalidades de estilo de mapa no Azure Mapas | Microsoft Docs
description: Saiba mais sobre as funcionalidades relacionadas ao estilo do Azure Mapas.
author: walsehgal
ms.author: v-musehg
ms.date: 08/31/2018
ms.topic: how-to-guides
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b95e3c325160d1c0b775320e25fdd40514b02b9f
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666864"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Escolha um estilo de mapa no Azure Mapas
O Azure Mapas tem quatro estilos de mapas diferentes para escolher. Para saber mais sobre estilos de mapa, consulte [estilos de mapa com suporte no Azure Mapas](./supported-map-styles.md). Este artigo mostra como usar as funcionalidades relacionadas ao estilo para definir um estilo no carregamento do mapa, definir um novo estilo e usar o controle selecionador de estilos.

## <a name="setting-style-on-map-load"></a>Configurar estilo no carregamento do mapa

<iframe height='500' scrolling='no' title='Configurando o estilo no carregamento do mapa' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Configurar estilo no carregamento do mapa</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O código acima cria um objeto de mapa com o estilo definido como Escala de cinza. Consulte [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

## <a name="updating-the-style"></a>Atualizar o estilo

<iframe height='500' scrolling='no' title='Atualizar o estilo' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Atualizar o estilo</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco do código no código acima cria um objeto de mapa sem predefinir o estilo. Consulte [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

O segundo bloco de código usa o método [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setstyle) do mapa para definir o estilo do mapa para satélite.

## <a name="adding-the-style-picker"></a>Adicionar o selecionador de estilos

<iframe height='500' scrolling='no' title='Adicionar o selecionador de estilos' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Atualizar o selecionador de estilos</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código acima cria um objeto de mapa sem predefinir o estilo. Consulte [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

O segundo bloco de código constrói um selecionador de estilos usando o construtor [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol) atlas.

Um selecionador de estilos permite a seleção de estilo para o mapa. O terceiro bloco de código adiciona o selecionador de estilos ao mapa usando o método [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo: 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setstyle)
    * [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol)

* [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)
    * [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol)
    
Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:
* [Adicionar controles de mapa](./map-add-controls.md)
* [Adicionar um PIN](./map-add-pin.md)
