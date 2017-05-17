---
title: Exemplos da CLI do Azure para o Azure Cosmos DB | Microsoft Docs
description: "Exemplos da CLI do Azure - Criar e gerenciar contas, , bancos de dados, contêineres, regiões e firewalls do Azure Cosmos DB."
services: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmosdb
ms.custom: sample
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: d1a0aa78c94c6305018d24c521de643197d4402c
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Exemplos da CLI do Azure para o Azure Cosmos DB

A tabela a seguir inclui links para exemplos de scripts de CLI do Azure para o Azure Cosmos DB.

| |  |
|---|---|
|**Criar uma conta, banco de dados e contêineres do Azure Cosmos DB**||
|[Criar uma conta de Documento, Graph ou API de Tabela](scripts/create-database-account-collections-cli.md)| Criar uma única conta, banco de dados e contêiner da API de DocumentDB do Azure Cosmos DB. |
| [Criar uma conta da API do MongoDB](scripts/create-mongodb-database-account-cli.md) | Cria uma única conta, banco de dados e coleção da API de MongoDB do Azure Cosmos DB. |
|**Dimensionar o Azure Cosmos DB**||
| [Escalar a taxa de transferência de contêiner](scripts/scale-collection-throughput-cli.md) | Altera a taxa de transferência provisionada em um contêiner.|
|[Replicar uma conta de banco de dados do Azure Cosmos DB em várias regiões e configurar as prioridades de failover](scripts/scale-multiregion-cli.md)|Replica globalmente os dados da conta em várias regiões com uma prioridade de failover especificada.|
|**Proteger o Azure Cosmos DB**||
| [Obter chaves da conta](scripts/secure-get-account-key-cli.md) | Obtém a chaves de gravação mestre primária e secundária e as chaves somente leitura primária e secundária da conta.|
| [Obter cadeia de conexão do MongoDB](scripts/secure-mongo-connection-string-cli.md) | Obtém a cadeia de conexão para conectar seu aplicativo MongoDB à sua conta do Azure Cosmos DB.|
|[Regenerar chaves da conta](scripts/secure-regenerate-key-cli.md)|Regenera a chave mestre ou somente leitura da conta.|
|[Criar um firewall](scripts/create-firewall-cli.md)| Cria uma política de controle de acesso IP de entrada para limitar o acesso à conta por um conjunto aprovado de máquinas e/ou serviços de nuvem.|
|**Alta disponibilidade, recuperação de desastre, backup e restauração**||
|[Configurar política de failover](scripts/ha-failover-policy-cli.md)|Define a prioridade de failover para cada região em que a conta é replicada.|
|**Conectar o Azure Cosmos DB aos recursos**||
|[Conectar um aplicativo Web ao Azure Cosmos DB](https://docs.microsoft.com/azure/app-service-web/scripts/app-service-cli-app-service-documentdb?toc=%2fcli%2fazure%2ftoc.json)|Criar e conectar um banco de dados do Azure Cosmos DB e um aplicativo Web.|
|||
