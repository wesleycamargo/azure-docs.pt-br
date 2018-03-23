---
title: Níveis de Zoom e Grade Lado a Lado nos Serviços do Azure Baseados na Localização | Microsoft Docs
description: Saiba mais sobre os Níveis de Zoom e Grade Lado a Lado nos Serviços do Azure Baseados na Localização
services: location-based-services
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 3/6/2018
ms.topic: article
ms.service: location-based-services
documentationcenter: ''
manager: cpendle
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 3a86bf84ad31933cc5008591a275d4f4aa52c9f1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="zoom-levels-and-tile-grid"></a>Níveis de zoom e grade lado a lado
Serviços do Azure Baseados na Localização usam o sistema de coordenadas de projeção do Spherical Mercator (EPSG: 3857).

O mundo é dividido em blocos quadrados. Render (Raster) possui 19 níveis de zoom, numerados de 0 a 18. Render (Vector) possui 21 níveis de zoom, numerados de 0 a 20. No nível de zoom 0, o mundo inteiro se ajusta em um único bloco:

![Bloco de mundo](./media/zoom-levels-and-tile-grid/world0.png)

Nível de zoom 1 usa 4 blocos para renderizar o mundo: um quadrado de 2 x 2

![Superior esquerda do bloco de mundo](./media/zoom-levels-and-tile-grid/world1a.png)     ![Superior direita do bloco de mundo](./media/zoom-levels-and-tile-grid/world1c.png) 

![Inferior esquerda do bloco de mundo](./media/zoom-levels-and-tile-grid/world1b.png)     ![Inferior direita do bloco de mundo](./media/zoom-levels-and-tile-grid/world1d.png) 


Cada nível de zoom subsequentes divide em quatro os blocos do anterior, criando uma grade de 2<sup>zoom</sup> x 2<sup>zoom</sup>. Nível de zoom 20 é uma grade 2<sup>20</sup> x 2<sup>20</sup>, ou blocos de 1.048.576 x 1.048.576 (109.951.162.778 no total).

A tabela completa de valores para os níveis de zoom está aqui:

|Nível de zoom|medidores/pixel|medidores/lado a lado|
|--- |--- |--- |
|0|156543|40075008|
|1|78271.5|20037504|
|2|39135.8|10018764.8|
|3|19567.9|5009382.4|
|4|9783.9|2504678.4|
|5|4892|1252352|
|6|2446|626176|
|7|1223|313088|
|8|611.5|156544|
|9|305.7|78259.2|
|10|152.9|39142.4|
|11|76.4|19558.4|
|12|38.2|9779.2|
|13|19.1|4889.6|
|14|9.6.|2457.6|
|15|4.8|1228.8|
|16|2.4|614.4|
|17|1.2|307.2|
|18|0.6|152.8|
|19|0.3|76.4|
|20|0.15|38.2|

Blocos são chamados por nível de zoom e as coordenadas x e y correspondentes à posição do bloco da grade para esse nível de zoom.

Ao determinar qual nível de zoom usar, lembre-se de que cada local está em uma posição fixa no seu bloco. Isso significa que o número de blocos necessário para exibir uma determinada extensão de território depende do posicionamento específico da grade de zoom no mundo. Por exemplo, se houver dois pontos de 900 metros de distância, ele *pode* levar apenas três blocos para exibir uma rota entre elas no nível de zoom 17. No entanto, se o ponto ocidental está à direita do seu bloco e o ponto oriental à esquerda dele, pode ter quatro blocos:

![Escala de demonstração de zoom](./media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

Uma vez determinado o nível de zoom, os valores x e y podem ser calculados. O bloco superior esquerdo de cada grade de zoom é x = 0, y = 0; o bloco inferior direito é em x=2<sup>zoom -1</sup>, y=2<sup>zoom 1</sup>.

Aqui está a grade de zoom para o nível de zoom 1:

![Grade de zoom para o nível de zoom 1](./media/zoom-levels-and-tile-grid/api_x_y.png)
