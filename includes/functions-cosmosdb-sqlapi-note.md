---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: bb915c18965a55c2b1fc7fe3cf1fa923d7f43fd6
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43052512"
---
As associações do Azure Cosmos DB têm suporte apenas para usar com a API do SQL. Para todas as outras APIs do Azure Cosmos DB, você deve acessar o banco de dados a partir de sua função usando o cliente estático para sua API, incluindo a [API do MongoDB](../articles/cosmos-db/mongodb-introduction.md), [API do Cassandra](../articles/cosmos-db/cassandra-introduction.md), [API do Gremlin](../articles/cosmos-db/graph-introduction.md) e [API da Tabela](../articles/cosmos-db/table-introduction.md).
