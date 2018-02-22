---
title: Suporte de recurso do Azure Cosmos DB para MongoDB | Microsoft Docs
description: Saiba mais sobre o suporte de recurso que a API MongoDB do Azure Cosmos DB oferece para o MongoDB 3.4.
services: cosmos-db
author: alekseys
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 29b6547c-3201-44b6-9e0b-e6f56e473e24
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: alekseys
ms.openlocfilehash: e955aa1c3985e540246d964b4dce88d15fb85949
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2018
---
# <a name="mongodb-api-support-for-mongodb-features-and-syntax"></a>Suporte da API MongoDB para recursos e sintaxe do MongoDB

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. Você pode se comunicar com a API MongoDB do banco de dados através de qualquer [driver](https://docs.mongodb.org/ecosystem/drivers) do cliente MongoDB de software livre. A API MongoDB permite o uso de drivers de cliente existentes que sigam o [protocolo de transmissão](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) do MongoDB.

Usando a API MongoDB do Azure Cosmos DB, você pode aproveitar os benefícios das APIs MongoDB às quais você está acostumado, com todos os recursos corporativos do Azure Cosmos DB: [distribuição global](distribute-data-globally.md), [fragmentação automática](partition-data.md), garantias de disponibilidade e latência, indexação automática de cada campo, criptografia em repouso, backups e muito mais.

## <a name="mongodb-query-language-support"></a>Suporte de linguagem de consulta do MongoDB

A API Mongo DB do Azure Cosmos DB oferece suporte abrangente para construções de linguagem de consulta do MongoDB. Abaixo você encontrará uma lista detalhada de operações, operadores, estágios, comandos e opções com suporte atualmente.


## <a name="database-commands"></a>Comandos de banco de dados

O Azure Cosmos DB suporta os seguintes comandos de banco de dados em todas as contas da API MongoDB. 

### <a name="query-and-write-operation-commands"></a>Comandos de operação de consulta e gravação
- excluir
- find
- findAndModify
- getLastError
- getMore
- insert
- atualizar

### <a name="authentication-commands"></a>Comandos de autenticação
- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>Comandos de administração
- dropDatabase
- listCollections
- drop
- create
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- ConnectionStatus
- reIndex

### <a name="diagnostics-commands"></a>Comandos de diagnóstico
- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Pipeline de agregação</a>

O Azure Cosmos DB oferece suporte ao pipeline de agregação em visualização pública. Consulte o [blog do Azure](https://aka.ms/mongodb-aggregation) para obter instruções sobre como integrar a visualização pública.

### <a name="aggregation-commands"></a>Comandos de agregação
- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>Estágios de agregação
- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count

### <a name="aggregation-expressions"></a>Expressões de agregação

#### <a name="boolean-expressions"></a>Expressões boolianas
- $and
- $or
- $not

#### <a name="set-expressions"></a>Expressões de definição
- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Expressões de comparação
- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Expressões aritméticas
- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>Expressões de cadeia de caracteres
- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Expressões de matriz
- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>Expressões de data
- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $segundo
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Expressões condicionais
- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Acumuladores de agregação
- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Operadores

A seguir estão os operadores com suporte e seus exemplos correspondentes de uso. Considere este documento de exemplo usado nas consultas a seguir:

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Operador | Exemplo |
--- | --- |
$eq | ``` { "Volcano Name": { $eq: "Rainier" } } ``` |  | -
$gt | ``` { "Elevation": { $gt: 4000 } } ``` |  | -
$gte | ``` { "Elevation": { $gte: 4392 } } ``` |  | -
$lt | ``` { "Elevation": { $lt: 5000 } } ``` |  | -
$lte | ``` { "Elevation": { $lte: 5000 } } ``` | | -
$ne | ``` { "Elevation": { $ne: 1 } } ``` |  | -
$in | ``` { "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } } ``` |  | -
$nin | ``` { "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } } ``` | | -
$or | ``` { $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$and | ``` { $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$not | ``` { "Elevation": { $not: { $gt: 5000 } } } ```|  | -
$nor | ``` { $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] } ``` |  | -
$exists | ``` { "Status": { $exists: true } } ```|  | -
$type | ``` { "Status": { $type: "string" } } ```|  | -
$mod | ``` { "Elevation": { $mod: [ 4, 0 ] } } ``` |  | -
$regex | ``` { "Volcano Name": { $regex: "^Rain"} } ```|  | -

### <a name="notes"></a>Observações

Em consultas de $regex, as expressões ancoradas à esquerda permitem pesquisa de índice. No entanto, usar o modiciador 'i' (não sensível a maiúsculas e minúsculas) e o modificador 'm' (várias linhas) faz com que a coleção verifique todas as expressões.
Quando houver a necessidade de se incluir '$' ou '|', é melhor criar duas (ou mais) consultas regex. Por exemplo, dada a seguinte consulta original: ```find({x:{$regex: /^abc$/})```, ela deve ser modificada da seguinte maneira: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
A primeira parte usará o índice para restringir a pesquisa a esses documentos começando com ^abc e a segunda parte corresponderá às entradas exatas. A barra de operador '|' atua como uma função "or" - a consulta ```find({x:{$regex: /^abc|^def/})``` faz a correspondência dos documentos onde o campo 'x' possui um valor que começa com "abc" ou "def". Para utilizar o índice, é recomendável dividir a consulta em duas consultas diferentes unidas pelo operador $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="update-operators"></a>Operadores de atualização

#### <a name="field-update-operators"></a>Operadores de atualização de campo
- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>Operadores de atualização de matriz
- $addToSet
- $pop
- $pullAll
- $pull (Observação: não há suporte para $pull com condição)
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>Operador de atualização bit a bit
- $bit

### <a name="geospatial-operators"></a>Operadores geoespaciais

Operador | Exemplo 
--- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | sim
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | sim
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | sim
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | sim
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | sim
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | sim
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | sim
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | sim
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | sim
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | sim
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | sim

## <a name="additional-operators"></a>Operadores adicionais

Operador | Exemplo | Observações 
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` | 
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |  
$size | ```{ "Location.coordinates": { $size: 2 } }``` | 
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` | 
$text |  | Sem suporte. Nesse caso, use $regex 

### <a name="methods"></a>Métodos

Os seguintes métodos são suportados:

#### <a name="cursor-methods"></a>Métodos de cursor

Método | Exemplo | Observações 
--- | --- | --- |
cursor.Sort() | ```cursor.sort({ "Elevation": -1 })``` | Documentos sem chave de classificação não são retornados

## <a name="unique-indexes"></a>Índices exclusivos

O Azure Cosmos DB indexa cada campo em documentos que são gravados no banco de dados por padrão. Índices exclusivos garantem que um campo específico não possua valores duplicados em todos os documentos em uma coleção, de forma semelhante a como a exclusividade é preservada na chave “_id” padrão. Agora você pode criar índices personalizados no Azure Cosmos DB usando o comando createIndex, incluindo a restrição 'unique'.

Índices exclusivos estão disponíveis para todas as contas da API MongoDB.

## <a name="time-to-live-ttl"></a>Vida útil (TTL)

O Azure Cosmos DB suporta uma vida útil (TTL) relativa com base em carimbo de data/hora do documento. A TTL pode ser habilitada para coleções de API MongoDB por meio do [portal do Azure](https://portal.azure.com).

## <a name="user-and-role-management"></a>Gerenciamento de usuários e funções

O Azure Cosmos DB ainda não oferece suporte para usuários e funções. O Azure Cosmos DB oferece suporte ao controle de acesso baseado em função (RBAC) e senhas/chaves de leitura/gravação e somente leitura que podem ser obtidas por meio do [portal do Azure](https://portal.azure.com) (página Cadeia de conexão).

## <a name="replication"></a>Replicação

O Azure Cosmos DB oferece suporte à replicação automática nativa em camadas mais baixas. Essa lógica é estendida para atingir também réplica global e baixa. O Azure Cosmos DB não oferece suporte aos comandos de replicação manual.

## <a name="sharding"></a>Fragmentação

O Azure Cosmos DB oferece suporte à fragmentação automática, do lado do servidor. O Azure Cosmos DB não oferece suporte aos comandos de fragmentação manual.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Studio 3T](mongodb-mongochef.md) com uma API para o banco de dados do MongoDB.
- Saiba como [usar o Robo 3T](mongodb-robomongo.md) com uma API para o banco de dados do MongoDB.
- Explore [exemplos](mongodb-samples.md) do Azure Cosmos DB com suporte de protocolo para MongoDB.
