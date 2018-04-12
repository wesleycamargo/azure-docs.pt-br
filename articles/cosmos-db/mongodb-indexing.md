---
title: Indexando na API MongoDB do Azure Cosmos DB | Microsoft Docs
description: Apresenta uma visão geral dos recursos de indexação na API MongoDB do Azure Cosmos DB.
services: cosmos-db
documentationcenter: ''
author: orestis-ms
manager: kfile
editor: ''
ms.assetid: daacbabf-1bb5-497f-92db-079910703047
ms.service: cosmos-db
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 03/01/2018
ms.author: orkostak
ms.openlocfilehash: 6cb267096e04d4957b166e539a4fc58aa25e04cb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="indexing-in-the-azure-cosmos-db-mongodb-api"></a>Indexação no Azure Cosmos DB: API MongoDB

A API MongoDB do Azure Cosmos DB aproveita os recursos de gerenciamento de índice automático do Azure Cosmos DB. Como resultado, os usuários têm acesso às políticas de indexação do Azure Cosmos DB padrão. Assim, se nenhum índice foi definido pelo usuário ou descartado, todos os campos serão automaticamente indexados por padrão quando inseridos na coleção. Na maioria dos cenários, recomendamos usar a política de indexação padrão definida na conta.

## <a name="dropping-the-default-indexes"></a>Descartando os índices padrão

Os seguintes comandos podem ser usados para descartar os índices padrão de uma coleção ```coll```:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

## <a name="creating-compound-indexes"></a>Criando índices compostos

Os índices compostos possuem referências a vários campos de um documento. Logicamente, eles são equivalentes à criação de vários índices individuais por campo. Para aproveitar as otimizações fornecidas pelas técnicas de indexação do Azure Cosmos DB, recomendamos criar vários índices individuais em vez de um único índice composto (não exclusivo).

## <a name="creating-unique-indexes"></a>Criando índices exclusivos

[Índices exclusivos](unique-keys.md) são úteis para impor que nenhum documento contenha o mesmo valor para o campo indexado. 
>[!important] 
> Atualmente, índices exclusivos podem ser criados apenas quando a coleção está vazia (sem documentos). 

O comando abaixo cria um índice exclusivo no campo "student_id":

```JavaScript
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} ) 
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

Para coleções fragmentadas, de acordo com o comportamento do MongoDB, a criação de um índice exclusivo requer o fornecimento da chave do fragmento (partição). Em outras palavras, todos os índices exclusivos em uma coleção fragmentada são índices compostos em que um dos campos é a chave de partição.

Os seguintes comandos criam uma coleção fragmentada ```coll``` (a chave do fragmento é ```university```) com um índice exclusivo nos campos student_id e university:

```JavaScript
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

No exemplo acima, se a cláusula ```"university":1``` for omitida, um erro com a seguinte mensagem será retornado:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>Índices TTL

Para habilitar a validade de documento em determinada coleção, um ["Índice TTL" (índice de vida útil)](../cosmos-db/time-to-live.md) precisará ser criado. Um TTL é um índice no campo _ts com um valor de "expireAfterSeconds".
 
Exemplo:
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

O comando anterior causará a exclusão de todos os documentos na coleção ```db.coll``` que não foram modificados nos últimos 10 segundos. 
 
> [!NOTE]
> **_ts** é um campo específico do Azure Cosmos DB e não pode ser acessado por clientes do MongoDB. É uma propriedade reservada (sistema) que contém o carimbo de data/hora da última modificação do documento.
>

## <a name="migrating-collections-with-indexes"></a>Migrando coleções com índices

Atualmente, a criação de índices exclusivos é possível apenas quando a coleção não contém documentos. Ferramentas de migração do MongoDB populares tentam criar índices exclusivos depois da importação dos dados. Para contornar esse problema, é recomendável que os usuários criem as coleções e os índices exclusivos correspondentes manualmente em vez de habilitar a ferramenta de migração (para ```mongorestore```, esse comportamento é obtido usando o sinalizador --noIndexRestore na linha de comando).

## <a name="next-steps"></a>Próximas etapas
* [Como o Azure Cosmos DB indexa dados?](../cosmos-db/indexing-policies.md)
* [Expirar os dados em coleções do Azure Cosmos DB automaticamente com a vida útil](../cosmos-db/time-to-live.md)
