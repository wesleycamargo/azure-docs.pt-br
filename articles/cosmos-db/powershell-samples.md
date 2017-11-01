---
title: Exemplos do Azure PowerShell para o BD Cosmos do Azure | Microsoft Docs
description: "Exemplos do Azure PowerShell – scripts para ajudá-lo a criar contas do BD Cosmos do Azure."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 10/16/2017
ms.author: mimig
ms.openlocfilehash: f2687369b8e247f00e9de5f3f79d8e44be5b8300
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2017
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Exemplos do Azure PowerShell para o BD Cosmos do Azure

A tabela a seguir inclui links para scripts de exemplo do Azure PowerShell para o BD Cosmos do Azure. Neste momento, só é possível gerenciar o accountlayer do Azure Cosmos DB por meio do PowerShell. Outros recursos como bancos de dados e coleções não podem ser gerenciados por meio do PowerShell.

| |  |
|---|---|
|**Criar uma conta do BD Cosmos do Azure**||
|[Criar uma conta da API do DocumentDB](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma única conta do BD Cosmos do Azure para ser usada com a API do DocumentDB. |
|**Dimensionar o BD Cosmos do Azure**||
|[Replicar uma conta do BD Cosmos do Azure em várias regiões e configurar as prioridades de failover](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Replica globalmente os dados da conta em várias regiões com uma prioridade de failover especificada.|
|**Proteger o Azure Cosmos DB**||
| [Obter chaves da conta](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obtém a chaves de gravação mestre primária e secundária e as chaves somente leitura primária e secundária da conta.|
| [Obter cadeia de conexão do MongoDB](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obtém a cadeia de conexão para conectar seu aplicativo MongoDB à sua conta do Azure Cosmos DB.|
|[Regenerar chaves da conta](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Regenera a chave mestre ou somente leitura da conta.|
|[Criar um firewall](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma política de controle de acesso IP de entrada para limitar o acesso à conta por um conjunto aprovado de máquinas e/ou serviços de nuvem.|
|**Alta disponibilidade, recuperação de desastre, backup e restauração**||
|[Configurar política de failover](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Define a prioridade de failover para cada região em que a conta é replicada.|
|||
