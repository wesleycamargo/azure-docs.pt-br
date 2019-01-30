---
title: Adicionar uma camada de Símbolo ao Azure Mapas | Microsoft Docs
description: Como adicionar símbolos ao mapa JavaScript
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 718a679418790a6bf1207a96e5c204f7962de239
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2019
ms.locfileid: "54411247"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Adicionar uma camada de símbolo a um mapa

Este artigo mostra como você pode renderizar os dados de ponto de uma fonte de dados como uma camada de Símbolo em um mapa. As camadas de Símbolo são renderizadas usando o WebGL e dão suporte a um número significativamente maior de pontos de dados comparado aos marcadores HTML, mas não dão suporte a elementos CSS e HTML tradicionais para estilos.  

> [!TIP]
> Por padrão, as camadas de Símbolo renderizarão as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada de tal forma que ela só processe recursos de geometrias, defina a `filter` propriedade da camada para `['==', '$type', 'Point']`

## <a name="add-a-symbol-layer"></a>Adicionar uma camada de símbolo

<iframe height='500' scrolling='no' title='Mudar localização de fixação' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Confira a Caneta <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Mudar localização de fixação</a> por Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código acima constrói um objeto Map. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um objeto de fonte de dados é criado usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Um [Recurso] que contém uma geometria [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) é encapsulado pela classe [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) para facilitar a atualização e, em seguida, é criado e adicionado à fonte de dados.

O terceiro bloco de código cria um [ouvinte de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) e atualiza as coordenadas do ponto após clique do mouse, usando o método [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) da classe shape.

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar dados com base em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa.  A fonte de dados, o ouvinte de eventos de clique e a camada de símbolo são criados e adicionados ao mapa dentro da função [ouvinte de evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o ponto seja exibido após o mapa ser totalmente carregado.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Adicionar um ícone personalizado a uma camada de símbolo

As camadas de símbolo são renderizadas usando o WebGL. Assim, todos os recursos, como imagens de ícone, precisam ser carregados no contexto do WebGL. Esta amostra explica como adicionar um ícone de símbolo personalizado aos recursos de mapa e, em seguida, usá-lo para renderizar o ponto de dados com um símbolo personalizado no mapa. A propriedade `textField` da camada de símbolo exige a especificação de uma expressão. Nesse caso, desejamos renderizar a propriedade de temperatura do recurso de ponto como o valor de texto. Isso pode ser obtido com esta expressão: `['get', 'temperature']`. 

<br/>

<iframe height='500' scrolling='no' title='Ícone de Imagem de Símbolo Personalizado' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Confira a Caneta <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Ícone de Imagem do Símbolo Personalizado</a> do Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-symbol-layer"></a>Personalizar uma camada de símbolo 

A camada de símbolo tem muitas opções de estilo disponíveis. Veja a seguir uma ferramenta para testar essas várias opções de estilo.

<br/>

<iframe height='700' scrolling='no' title='Opções da camada de símbolo' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Confira a Caneta <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Opções de Camada de Símbolo</a> do Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TexTOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar um pop-up](./map-add-popup.md)

> [!div class="nextstepaction"]
> [ Adicione uma forma ](./map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](./map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar marcadores HTML](./map-add-bubble-layer.md)
