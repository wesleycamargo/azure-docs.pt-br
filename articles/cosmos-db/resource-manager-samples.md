---
title: Modelos do Azure Resource Manager para o Azure Cosmos DB
description: Use modelos do Azure Resource Manager para criar e configurar o Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: c907de019b64a6a9d03206514a1147fd43c78106
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078454"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modelos do Azure Resource Manager para o Azure Cosmos DB

A tabela a seguir inclui links para modelos do Azure Resource Manager para o Azure Cosmos DB:

|**Tipo de API** | **Vincular a amostra**| **Descrição** |
|---|---| ---|
|API Core (SQL)| [Criar uma conta do Azure Cosmos DB (vários mestres)](manage-sql-with-resource-manager.md) | Este modelo cria uma conta de API do SQL em duas regiões com vários mestres habilitado. A conta de Cosmos do Azure terá dois contêineres que compartilham a taxa de transferência de nível de banco de dados. |
| API do MongoDB | [Criar uma conta do Azure Cosmos DB (vários mestres)](manage-mongodb-with-resource-manager.md) | Este modelo cria uma conta usando a API do Azure Cosmos DB para MongoDB em duas regiões com vários mestres habilitado. A conta de Cosmos do Azure terá dois contêineres que compartilham a taxa de transferência de nível de banco de dados. |
| API Cassandra | [Criar uma conta do Azure Cosmos DB (vários mestres)](manage-cassandra-with-resource-manager.md) | Este modelo cria uma conta de API do Cassandra em duas regiões com vários mestres habilitado. A conta de Cosmos do Azure terá duas tabelas que compartilham a taxa de transferência de nível de keyspace. |
| API do Gremlin| [Criar uma conta do Azure Cosmos DB (vários mestres)](manage-gremlin-with-resource-manager.md) | Este modelo cria uma conta da API do Gremlin em duas regiões com vários mestres habilitado. A conta de Cosmos do Azure terá dois gráficos que compartilham a taxa de transferência de nível de banco de dados. |
| API de Tabela | [Criar uma conta do Azure Cosmos DB (vários mestres)](manage-table-with-resource-manager.md) | Este modelo cria uma conta da API de tabela em duas regiões com vários mestres habilitado. A conta de Cosmos do Azure terá uma única tabela. |

> [!TIP]
> Para habilitar a taxa de transferência compartilhada ao usar a API de tabela, habilite a taxa de transferência no nível da conta no Portal do Azure.

Ver [referência ARM do Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) página para obter a documentação de referência.