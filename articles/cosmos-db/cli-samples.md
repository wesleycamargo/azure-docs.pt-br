---
title: Exemplos da CLI do Azure para o Azure Cosmos DB
description: Exemplos da CLI do Azure - Criar e gerenciar contas, , bancos de dados, contêineres, regiões e firewalls do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: a6348024d4e84c27610f1294f916cca9a851b6b9
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034192"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Exemplos da CLI do Azure para o Azure Cosmos DB

A tabela a seguir inclui links para exemplos de scripts de CLI do Azure para o Azure Cosmos DB. As páginas de referência de todos os comandos do Azure Cosmos DB CLI estão disponíveis na [Referência de CLI do Azure](/cli/azure/cosmosdb).

| |  |
|---|---|
|**Criar uma conta, banco de dados e contêineres do Azure Cosmos DB**||
| [Criar uma conta do Azure Cosmos DB usando a API do SQL](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma única conta, um banco de dados e um contêiner do Azure Cosmos DB. |
| [Criar uma conta do Azure Cosmos DB usando a API do Cosmos DB para MongoDB](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma única conta, um banco de dados e uma coleção do Azure Cosmos DB. |
| [Criar uma conta do Azure Cosmos DB usando a API do Gremlin](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma única conta, um banco de dados e um grafo do Azure Cosmos DB. |
| [Criar uma conta do Azure Cosmos DB usando a API do Cassandra](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma única conta e um banco de dados do Azure Cosmos DB. |
| [Criar uma conta do Azure Cosmos DB usando a API de Tabela](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma única conta, um banco de dados e uma tabela do Azure Cosmos DB. |
|**Dimensionar o Azure Cosmos DB**||
| [Escalar a taxa de transferência de contêiner](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Altera a taxa de transferência provisionada em um contêiner.|
| [Replicar uma conta de banco de dados do Azure Cosmos DB em várias regiões e configurar as prioridades de failover](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Replica globalmente os dados da conta em várias regiões com uma prioridade de failover especificada.|
|**Proteger o Azure Cosmos DB**||
| [Obter chaves da conta](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Obtém a chaves de gravação mestre primária e secundária e as chaves somente leitura primária e secundária da conta.|
| [Obter a cadeia de conexão para a conta do Cosmos configurada com a API do Azure Cosmos DB para MongoDB](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Obtém a cadeia de conexão para conectar seu aplicativo MongoDB à conta do Azure Cosmos DB.|
| [Regenerar chaves da conta](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Regenere as chaves da conta.|
| [Criar um firewall](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Crie uma política de controle de acesso IP de entrada para limitar o acesso à conta por um conjunto aprovado de máquinas e/ou serviços de nuvem.|
|**Alta disponibilidade, recuperação de desastre, backup e restauração**||
| [Configurar política de failover](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Define a prioridade de failover para cada região em que a conta é replicada.|
|**Conectar o Azure Cosmos DB aos recursos**||
| [Conectar um aplicativo Web ao Azure Cosmos DB](../app-service/scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Criar e conectar um banco de dados do Azure Cosmos DB e um aplicativo Web.|
|||
