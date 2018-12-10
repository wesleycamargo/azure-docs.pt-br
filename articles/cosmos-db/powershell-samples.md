---
title: Exemplos do Azure PowerShell para o Azure Cosmos DB
description: Exemplos do Azure PowerShell – scripts para ajudá-lo a criar contas do BD Cosmos do Azure.
services: cosmos-db
author: SnehaGunda
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.topic: sample
ms.date: 10/16/2017
ms.author: sngun
ms.openlocfilehash: c2bd1ac165e6b17d1bd9604fb1203ae47214278f
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871023"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Exemplos do Azure PowerShell para o BD Cosmos do Azure

A tabela a seguir inclui links para scripts de exemplo do Azure PowerShell para o BD Cosmos do Azure. Neste momento, só é possível gerenciar a conta do Azure Cosmos DB por meio do PowerShell. Outros recursos como bancos de dados e contêineres não podem ser gerenciados por meio do PowerShell.

| |  |
|---|---|
|**Criar uma conta do BD Cosmos do Azure**||
|[Criar uma conta da API do SQL](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma única conta do BD Cosmos do Azure para ser usada com a API do SQL. |
|[Criar uma conta da API do MongoDB](scripts/create-mongodb-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma única conta do Azure Cosmos DB para ser usada com a API do MongoDB. |
|[Criar uma conta da API Gremlin](scripts/create-graph-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma única conta do Azure Cosmos DB para ser usada com a API do Gremlin. |
|[Criar uma conta da API do Cassandra](scripts/create-and-configure-cassandra-database.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma única conta do Azure Cosmos DB para ser usada com a API do Cassandra. |
|[Criar uma conta de API de Tabela](scripts/create-table-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma única conta do Azure Cosmos DB para ser usada com a API da Tabela. |
|**Dimensionar o Azure Cosmos DB**||
|[Replicar uma conta do BD Cosmos do Azure em várias regiões e configurar as prioridades de failover](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Replica globalmente os dados da conta em várias regiões com uma prioridade de failover especificada.|
|**Proteger o Azure Cosmos DB**||
| [Obter chaves da conta](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obtém a chaves de gravação mestre primária e secundária e as chaves somente leitura primária e secundária da conta.|
| [Obter cadeia de conexão do MongoDB](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obtém a cadeia de conexão para conectar seu aplicativo MongoDB à sua conta do Azure Cosmos DB.|
|[Regenerar chaves da conta](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Regenera a chave mestre ou somente leitura da conta.|
|[Criar um firewall](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma política de controle de acesso IP de entrada para limitar o acesso à conta por um conjunto aprovado de máquinas e/ou serviços de nuvem.|
|**Alta disponibilidade, recuperação de desastre, backup e restauração**||
|[Configurar política de failover](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Define a prioridade de failover para cada região em que a conta é replicada.|
|||
