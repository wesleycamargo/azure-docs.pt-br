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
ms.openlocfilehash: ec1343f85216171adac22f873f9be2e72bb4c282
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892754"
---
# <a name="add-a-heat-map-layer"></a>Adicionar uma camada do mapa de calor

Mapas de calor, também conhecidos como ponto de mapas de densidade, são um tipo de visualização de dados usado para representar a densidade de dados usando um intervalo de cores. Geralmente são usados para mostrar os dados de "pontos de acesso" em um mapa e são uma ótima maneira de processar grandes conjuntos de dados de ponto.  Por exemplo, dezenas de milhares de pontos dentro da exibição de mapa de renderização como símbolos abrangem a maior parte da área do mapa e resultaria em muitos símbolos sendo cobertos por outras pessoas, dificultando obter muitas informações sobre os dados. No entanto, visualizar esse mesmo conjunto de dados como um mapa de calor torna mais fácil ver onde os dados de ponto são o densest e a densidade relativa para outras áreas. Há muitos cenários nos quais os mapas de calor são usados. Veja alguns exemplos;

* Dados de temperatura normalmente são renderizados como mapa de calor, pois ele fornece aproximações para a temperatura entre dois pontos de dados.
* Renderização de dados de sensores de ruído, como um mapa de calor não só mostra a insanidade do ruído em que o sensor está, mas também pode fornecer ideias sobre a dissipação de uma distância. O nível de ruído em um site não pode ser alto, no entanto, se sobrepõe a área de cobertura de ruído de vários sensores, é possível que essa área de sobreposição possa enfrentar níveis mais altos de ruído e, portanto, deve estar visível no mapa de calor.
* Visualizar um traço de GPS que inclua a velocidade, como um mapa de altura de peso em que a intensidade de cada ponto de dados se baseia na velocidade é uma ótima maneira de ver rapidamente onde o veículo estava acelerando.

> [!TIP]
> As camadas de bolha por padrão processarão as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada de tal forma que ela só processe recursos de geometrias, defina a `filter` propriedade da camada para `['==', '$type', 'Point']`

## <a name="add-a-heat-map-layer"></a>Adicionar uma camada do mapa de calor

Para renderizar uma fonte de dados de pontos como mapa de calor, passe sua fonte de dados para uma instância da classe do HeatMapLayer e adicione-e ao mapa conforme exibido aqui.

<br/>

<iframe height='500' scrolling='no' title='Camada do Mapa de calor simples' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Camada do Mapa de calor Simples</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

Neste exemplo, cada ponto de calor tem um raio de 10 pixels em todos os níveis de zoom. Ao adicionar a camada do mapa de calor no mapa, este exemplo insere-o abaixo da camada de rótulo. Isso cria uma melhor experiência de usuário, uma vez que os rótulos são evidentes acima do mapa de calor. Os dados neste exemplo são originados do [Programa de Riscos de Terremoto USGS](https://earthquake.usgs.gov/) e consiste em pontos que representa terremotos significativos que ocorreram nos últimos 30 dias.

## <a name="customizing-the-heat-map-layer"></a>Personalizando a camada do mapa de calor

O exemplo anterior personalizou o mapa de calor, definindo as opções de raio e opacidade. A camada do mapa de calor fornece várias opções de personalização;

* `radius`: Define um raio de pixel no qual renderizar cada ponto de dados. O raio pode ser definido como um número fixo ou como uma expressão. Configurando o raio como uma expressão que define o raio com base no nível de zoom pode resultar em mapas de calor que parecem ter um raio que representa uma área espacial consistente no mapa.
* `color`: Especifica como o mapa de calor é colorido. Uma paleta de cores gradiente é frequentemente usada para mapas de calor, mas as paletas de cor também são úteis se você quiser que seu mapa de calor pareça mais com dados de delimitação. Essas paletas de cores definem as cores do valor de densidade mínimo ao máximo. Valores de cor para mapas de calor são especificados como uma expressão sobre o `heatmap-density` valor. A cor no índice 0 em uma expressão de gradiente ou a cor padrão de uma cor de etapa define a cor da área em que não há nenhum dado ou cor de plano de fundo. Muitos preferem definir esse valor como transparente ou semitransparente preto. Aqui estão exemplos de expressões de cores;

| Gradient Color Expression | Stepped Color Expression | 
|---------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1.00, 'red'<br/>\] |   

* `opacity`: Especifica como opaco ou transparente a camada do mapa de calor.
* `intensity`: Aplica um multiplicador para o peso de cada ponto de dados para aumentar a intensidade geral do mapa de calor. Isso ajuda a tornar as pequenas diferenças no peso de pontos de dados a se tornarem mais fáceis de visualizar.
* `weight`: Por padrão, todos os pontos de dados têm um peso de 1, portanto, todos os pontos de dados são ponderados igualmente. A opção de peso atua como uma multiplicadora e pode ser definida como um número ou uma expressão. Se um número for definido como número, digamos 2, seria o equivalente a colocação de cada ponto de dados no mapa duas vezes, que dobra a densidade. Definir a opção de peso para um número renderiza o mapa de calor de maneira semelhante a usar a opção de intensidade. No entanto, se uma expressão for usada, o peso de cada ponto de dados pode ser baseado em métricas diferentes e baseado em alguma métrica nas propriedades do ponto. Tome os dados do terremoto como exemplo, cada ponto de dados representam um terremoto e uma métrica importante que cada terremoto tem é a magnitude. Terremotos ocorrem o tempo todo, mas a maioria tem uma magnitude baixa e não são sentidos. Usar o valor de magnitude em uma expressão para atribuir a opção de peso permitirá que o terremotos mais significativos que o aumento seja melhor representado dentro do mapa de calor.
* Além das opções de camada base; mín. / máx de zoom, visíveis e filtrar, há também uma `source` opção se você deseja atualizar a fonte de dados e `source-layer` opção se sua fonte de dados é uma fonte de bloco do vetor.

Aqui está uma ferramenta para testar as opções de camada do mapa de calor diferentes.

<br/>

<iframe height='700' scrolling='no' title='Opções da Camada do Mapa de Calor' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Opções de Camada de Bloco</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Habilitar o clustering na fonte de dados, os pontos que estão próximos uns dos outros são agrupados como um ponto em cluster. A contagem de ponto de cada cluster pode ser usada como a expressão de peso para o mapa de calor e reduzir significativamente o número de pontos que precisam ser renderizados. A contagem de ponto de um cluster é armazenada em uma propriedade point_count do recurso de ponto, conforme mostrado abaixo. 
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

