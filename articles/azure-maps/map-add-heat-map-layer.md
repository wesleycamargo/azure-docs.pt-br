---
title: Adicionar uma camada de bloco para o Azure Mapas | Microsoft Docs
description: Como adicionar uma camada de bloco ao mapa no Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 957ce60b8519ccb1e3287232f7a5459a56b25bb7
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960608"
---
# <a name="add-a-heat-map-layer"></a>Adicionar uma camada do mapa de calor

Mapas de calor, também conhecidos como ponto de mapas de densidade, são um tipo de visualização de dados usado para representar a densidade de dados usando um intervalo de cores. Geralmente são usados para mostrar os "pontos de acesso" dos dados em um mapa e são uma ótima maneira de renderizar grandes conjuntos de dados de ponto.  Por exemplo, a renderização de dezenas de milhares de pontos dentro da exibição de mapa como símbolos abrange a maior parte da área do mapa e resultaria em muitos símbolos sendo cobertos por outras pessoas, dificultando a obtenção de muitas informações sobre os dados. No entanto, visualizar esse mesmo conjunto de dados como um mapa de calor torna mais fácil ver onde os dados de ponto são o densest e a densidade relativa para outras áreas. Há muitos cenários nos quais os mapas de calor são usados. Veja alguns exemplos;

* Dados de temperatura normalmente são renderizados como mapa de calor, pois ele fornece aproximações para a temperatura entre dois pontos de dados.
* Renderização de dados de sensores de ruído, como um mapa de calor não só mostra a intensidade do ruído em que o sensor está, mas também pode fornecer ideias sobre a dissipação de uma distância. O nível de ruído em um site não pode ser alto, no entanto, se a área de cobertura de ruído de vários sensores é sobreposta, é possível que essa área de sobreposição possa enfrentar níveis mais altos de ruído e, portanto, deve estar visível no mapa de calor.
* Visualizar um rastreamento de GPS que inclui a velocidade, como um mapa de altura ponderada em que a intensidade de cada ponto de dados se baseia na velocidade é uma ótima maneira de ver rapidamente onde o veículo estava acelerando.

> [!TIP]
> Por padrão, as camadas do mapa de calor renderizarão as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada de tal forma que ela só processe recursos de geometrias, defina a `filter` propriedade da camada para `['==', '$type', 'Point']`

## <a name="add-a-heat-map-layer"></a>Adicionar uma camada do mapa de calor

Para renderizar uma fonte de dados de pontos como mapa de calor, passe sua fonte de dados para uma instância da classe `HeatMapLayer` e adicione-a ao mapa conforme exibido aqui.

<br/>

<iframe height='500' scrolling='no' title='Camada do Mapa de calor simples' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Camada do Mapa de calor Simples</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

Neste exemplo, cada ponto de calor tem um raio de 10 pixels em todos os níveis de zoom. Ao adicionar a camada do mapa de calor no mapa, este exemplo insere-o abaixo da camada de rótulo. Isso cria uma melhor experiência de usuário, uma vez que os rótulos são evidentes acima do mapa de calor. Os dados neste exemplo são originados do [Programa de Riscos de Terremoto USGS](https://earthquake.usgs.gov/) e consiste em pontos que representa terremotos significativos que ocorreram nos últimos 30 dias.

## <a name="customizing-the-heat-map-layer"></a>Personalizando a camada do mapa de calor

O exemplo anterior personalizou o mapa de calor, definindo as opções de raio e opacidade. A camada do mapa de calor fornece várias opções de personalização;

* `radius`: Define um raio de pixel no qual renderizar cada ponto de dados. O raio pode ser definido como um número fixo ou como uma expressão. Usando uma expressão, é possível dimensionar o raio com base no nível de zoom; isso representa uma área espacial consistente no mapa (por exemplo, um raio de 5 milhas).
* `color`: Especifica como o mapa de calor é colorido. Uma paleta de cores gradiente é frequentemente usada para mapas de calor, mas as paletas de cor também são úteis se você quiser que seu mapa de calor pareça mais com dados de delimitação. Essas paletas de cores definem as cores do valor de densidade mínimo ao máximo. Valores de cor para mapas de calor são especificados como uma expressão sobre o `heatmap-density` valor. A cor no índice 0 em uma expressão de gradiente ou a cor padrão de uma cor de etapa define a cor da área em que não há nenhum dado e pode ser usada para definir uma cor da tela de fundo. Muitos preferem definir esse valor como transparente ou semitransparente preto. Aqui estão exemplos de expressões de cores;

| Gradient Color Expression | Stepped Color Expression | 
|---------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1.00, 'red'<br/>\] |   

* `opacity`: Especifica como opaco ou transparente a camada do mapa de calor.
* `intensity`: Aplica um multiplicador para o peso de cada ponto de dados para aumentar a intensidade geral do mapa de calor. Isso ajuda a tornar as pequenas diferenças no peso de pontos de dados a se tornarem mais fáceis de visualizar.
* `weight`: Por padrão, todos os pontos de dados têm um peso de 1, portanto, todos os pontos de dados são ponderados igualmente. A opção de peso atua como uma multiplicadora e pode ser definida como um número ou uma expressão. Se um número fosse definido como o peso, digamos 2, seria o equivalente a colocação de cada ponto de dados no mapa duas vezes, dobrando a densidade. Definir a opção de peso para um número renderiza o mapa de calor de maneira semelhante a usar a opção de intensidade. No entanto, se uma expressão for usada, o peso de cada ponto de dados poderá ser baseado em alguma métrica nas propriedades de cada ponto de dados. Use os dados de terremoto como exemplo. Cada ponto de dados representa um terremoto. Uma métrica importante que cada ponto de dados de terremoto tem é um valor de magnitude. Terremotos ocorrem o tempo todo, mas a maioria tem uma magnitude baixa e não são sentidos. Usar o valor de magnitude em uma expressão para atribuir o peso a cada ponto de dados permitirá que os terremotos mais significativos sejam mais bem representados no mapa de calor.
* Além das opções de camada base; mín./máx. de zoom, visíveis e filtrar, há também uma opção `source` se você deseja atualizar a fonte de dados e uma opção `source-layer` se a fonte de dados é uma fonte de bloco do vetor.

Aqui está uma ferramenta para testar as opções de camada do mapa de calor diferentes.

<br/>

<iframe height='700' scrolling='no' title='Opções da Camada do Mapa de Calor' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Opções de Camada de Bloco</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Habilitar o clustering na fonte de dados, os pontos que estão próximos uns dos outros são agrupados como um ponto em cluster. A contagem de ponto de cada cluster pode ser usada como a expressão de peso para o mapa de calor e reduzir significativamente o número de pontos que precisam ser renderizados. A contagem de ponto de um cluster é armazenada em um `point_count property` do recurso de ponto, conforme mostrado abaixo. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Se o raio de clustering é apenas alguns pixels lá será uma diferença um pouco visual da renderização. Um raio maior agrupará mais pontos em cada cluster e melhorará o desempenho de mapa de calor, mas veja quais serão as diferenças mais notáveis.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](./map-add-pin.md)

