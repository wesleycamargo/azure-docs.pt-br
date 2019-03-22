---
title: Adicionar controles de mapa nos mapas do Azure | Microsoft Docs
description: Como adicionar controle de zoom, controle de arremesso, girar o controle e um selecionador de estilo a um mapa nos Mapas do Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c1f5dd329f34d64478d605c21d229d8c75a99300
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260129"
---
# <a name="add-map-controls-to-azure-maps"></a>Adicionar controles de mapa aos mapas do Azure

Este artigo mostra como adicionar controles de mapa a um mapa. Você também aprenderá a criar um mapa com todos os controles e um [selecionador de estilos](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Adicionar o controle de zoom

<iframe height='500' scrolling='no' title='Adicionando um controle de zoom' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Adicionando um controle de zoom</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código define a chave de assinatura e cria um objeto de Mapa sem pré-definir o estilo. Consulte [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

O controle de zoom adiciona a capacidade de aumentar e diminuir o zoom do mapa. O segundo bloco de código cria um objeto Zoom Control usando o atlas [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) e o adiciona ao mapa usando o método [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). O controle de Zoom está dentro do **ouvinte de eventos** do mapa para garantir que seja carregado após o carregamento total do mapa.

## <a name="add-pitch-control"></a>Adicionar controle de densidade

<iframe height='500' scrolling='no' title='Adicionando um controle de densidade' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Adicionando um controle de pitch</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código define a chave de assinatura e cria um objeto de Mapa sem pré-definir o estilo. Consulte [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

O controle de afinação adiciona a capacidade de alterar o tom do mapa. O segundo bloco de código cria um objeto Controle de Pitch usando o atlas [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) e o adiciona ao mapa usando o método [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) do mapa. O controle de Pitch está dentro do **ouvinte de eventos** do mapa para garantir que seja carregado após o carregamento total do mapa.

## <a name="add-compass-control"></a>Adicionar controle de bússola

<iframe height='500' scrolling='no' title='Adicionando um controle de rotação' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Adicionando um controle de rotação</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código define a chave de assinatura e cria um objeto de Mapa sem pré-definir o estilo. Consulte [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

O segundo bloco de código cria um objeto de Controle de Bússola usando o atlas [Compass Control](/javascript/api/azure-maps-control/atlas.control.compasscontrol). Ele também adiciona o controle da bússola ao mapa usando o método [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) do mapa. O controle de Bússola está dentro do **ouvinte de eventos** do mapa para garantir que seja carregado após o carregamento total do mapa.

## <a name="a-map-with-all-controls"></a>Um mapa com todos os controles

<iframe height='500' scrolling='no' title='Um mapa com todos os controles' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>Um mapa com todos os controles</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código define a chave de assinatura e cria um objeto de Mapa sem pré-definir o estilo. Consulte [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

O segundo bloco de código cria um objeto de Controle de Bússola usando o atlas [CompassControl](/javascript/api/azure-maps-control/atlas.control.compasscontrol) e o adiciona ao mapa usando o método [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) do mapa.

O terceiro bloco de código cria um objeto de Controle de Zoom usando o atlas [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) e o adiciona ao mapa usando o método [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) do mapa.

O quarto bloco de código cria um objeto de Controle de Pitch usando o atlas [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) e o adiciona ao mapa usando o método [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) do mapa.

O último bloco de código cria um objeto de Selecionador de Estilos usando o atlas [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) e o adiciona ao mapa usando o método [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) do mapa. Todos os objetos de controle são adicionados dentro do **ouvinte de eventos** do mapa para garantir que sejam carregados após o carregamento total do mapa.

A ordem dos objetos de controle no script determina a ordem em que aparecem no mapa. Para alterar a ordem dos controles no mapa, você pode alterar a ordem deles no script.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para o código completo:

> [!div class="nextstepaction"]
> [Adicionar um PIN](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar um pop-up](./map-add-popup.md)
