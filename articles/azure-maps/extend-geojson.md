---
title: Estender geometrias GeoJSON no Azure Mapas | Microsoft Docs
description: Saiba como estender geometrias GeoJSON no Azure Mapas
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 2cc0e29615ad4fc19040055d847435a9dffa9c95
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658796"
---
# <a name="extending-geojson-geometries"></a>Estender geometrias GeoJSON

O Azure Mapas fornece uma lista de APIs poderoso para pesquisa interna/ao longo de recursos geográficos.
Essas APIs padronizam na [especificação GeoJSON][1] para representar os recursos geográficos (por exemplo: limites de estado, rotas).  

A [especificação GeoJSON][1] é compatível com as geometrias a seguir:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

Algumas APIs no Azure Mapas (por exemplo: [pesquisa dentro de geometria](https://docs.microsoft.com/en-us/rest/api/maps/search/postsearchinsidegeometry)) aceitar geometrias "Círculo", que não são parte do [GeoJSON especificação][1].

Este artigo fornece uma explicação detalhada sobre como o Azure Mapas estende a [especificação GeoJSON][1] para representar determinadas geometrias.

### <a name="circle"></a>Círculo

A geometria `Circle` não é compatível com a [especificação GeoJSON][1]. Usamos o objeto `GeoJSON Feature` para representar um círculo.

Uma geometria `Circle` representada usando o objeto `GeoJSON Feature` __deve__ conter o seguinte:

1. Centro
   >Centro do círculo é representado usando um tipo `GeoJSON Point`.

2. Raio
   >O círculo `radius` é representada usando as propriedades de `GeoJSON Feature`. O valor de raio está em _metros_ e deve ser do tipo `double`.

3. SubType
   >A geometria do círculo também deve conter a propriedade `subType`. Essa propriedade deve ser uma parte das propriedades `GeoJSON Feature` e seu valor deve ser _Circle_


#### <a name="example"></a>Exemplo

Aqui está como você vai representar um círculo centralizado (latitude: 47,639754, longitude: -122,126986) com um raio igual a 100 metros, usando um objeto `GeoJSON Feature`:

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

[1]: https://tools.ietf.org/html/rfc7946
