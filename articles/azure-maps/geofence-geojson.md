---
title: Formato de dados GeoJSON de cerca geográfica no Azure Mapas | Microsoft Docs
description: Saiba mais sobre formato de dados GeoJSON de cerca geográfica no Azure Mapas
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 6f76eea365311cd6c10dd39054187bf3a07bd595
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57568984"
---
# <a name="geofencing-geojson-data"></a>Dados GeoJSON de delimitação geográfica

O [GET Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofencepreview) e [POST Geofence](https://docs.microsoft.com/rest/api/maps/spatial/postgeofencepreview) do Azure Mapas permitem que você recupere a proximidade de uma coordenada em relação a uma cerca geográfica ou um conjunto de cercas. Este artigo detalha como preparar os dados de cerca geográfica que podem ser usados na API POST e GET do Azure Mapas.

Os dados para cerca geográfica ou conjunto de cercas geográficas são representados pelo Objeto `Feature` e Objeto `FeatureCollection` em formato `GeoJSON`, que é definido em [rfc7946](https://tools.ietf.org/html/rfc7946). Além disso:

* O tipo de objeto GeoJSON pode ser um Objeto `Feature` ou um Objeto `FeatureCollection`.
* O tipo de objeto de Geometria pode ser um `Point`, `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, `MultiPolygon` e `GeometryCollection`.
* Todas as propriedades do recurso devem conter um `geometryId`, que é usado para identificar a cerca geográfica.
* O recurso com `Point`, `MultiPoint`, `LineString`, `MultiLineString` deve conter `radius` nas propriedades. O valor `radius` é medido em metros, o valor `radius` varia de 1 a 10000.
* O recurso com tipo de geometria `polygon` e `multipolygon` não tem uma propriedade de raio.
* `validityTime` é uma propriedade opcional que permite que o usuário defina o período de tempo de validade e tempo expirado para os dados de cerca geográfica. Se não for especificado, os dados nunca irão expirar e serão sempre válidos.
* O `expiredTime` é a data e hora de expiração dos dados de delimitação geográfica. Se o valor de `userTime` na solicitação for posterior a esse valor, os dados de cerca geográfica correspondentes serão considerados como dados expirados e não serão consultados. Para os quais, o geometryId desses dados de cerca geográfica serão incluídos na matriz `expiredGeofenceGeometryId` dentro da resposta da cerca geográfica.
* O `validityPeriod` é uma lista do período de validade da cerca geográfica. Se o valor de `userTime` na solicitação ficar fora do período de validade, os dados de cerca geográfica correspondentes serão considerados inválidos e não serão consultados. O geometryId desses dados de cerca geográfica é incluído na matriz `invalidPeriodGeofenceGeometryId` dentro da resposta da cerca geográfica. A tabela a seguir mostra as propriedades do elemento validityPeriod.

| NOME | Type | Obrigatório  | DESCRIÇÃO |
| :------------ |:------------: |:---------------:| :-----|
| startTime | DateTime  | verdadeiro | A data/hora de início do período de tempo validade. |
| endTime   | DateTime  | verdadeiro |  A data/hora de término do período de tempo de validade. |
| recurrenceType | cadeia de caracteres | falso |   O tipo de recorrência do período. O valor pode ser `Daily`, `Weekly`, `Monthly` ou `Yearly`. O valor padrão é `Daily`.|
| businessDayOnly | BOOLEAN | falso |  Indique se os dados são válidos apenas durante os dias úteis. O valor padrão é `false`.|


* Todos os valores de coordenadas são representados como [latitude, longitude] `WGS84`.
* Para cada recurso, que contém `MultiPoint`, `MultiLineString`, `MultiPolygon` ou `GeometryCollection`, as propriedades são aplicadas a todos os elementos. Por exemplo: Todos os pontos em `MultiPoint` usarão o mesmo raio para formar uma cerca geográfica de vários círculos.
* Em cenário de círculo ponto, uma geometria de círculo pode ser representada usando um objeto de geometria `Point` com propriedades elaboradas em [Estender geometrias GeoJSON](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

A seguir, é apresentado um corpo da solicitação de exemplo para uma cerca geográfica representada como uma geometria de cerca geográfica de círculo em `GeoJSON` usando um ponto central e um raio. O período válido dos dados de cerca geográfica começa a partir de 22/10/2018, das 9h às 17h, repetido todos os dias, exceto no final de semana. `expiredTime` indica que os dados de cerca geográfica serão considerados expirados, se `userTime` na solicitação for posterior a `2019-01-01`.  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```