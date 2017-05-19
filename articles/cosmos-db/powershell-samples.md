---
title: Exemplos do Azure PowerShell para o BD Cosmos do Azure | Microsoft Docs
description: "Exemplos do Azure PowerShell – scripts para ajudá-lo a criar contas do BD Cosmos do Azure."
services: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmosdb
ms.custom: sample
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8bf047bd19c5278bfff85cab63ea10a1838cc683
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Exemplos do Azure PowerShell para o BD Cosmos do Azure

A tabela a seguir inclui links para scripts de exemplo do Azure PowerShell para o BD Cosmos do Azure.

| |  |
|---|---|
|**Criar uma conta do BD Cosmos do Azure**||
|[Criar uma conta da API do DocumentDB](scripts/create-database-account-powershell.md)| Cria uma única conta do BD Cosmos do Azure para ser usada com a API do DocumentDB. |
|**Dimensionar o BD Cosmos do Azure**||
|[Replicar uma conta do BD Cosmos do Azure em várias regiões e configurar as prioridades de failover](scripts/scale-multiregion-powershell.md)|Replica globalmente os dados da conta em várias regiões com uma prioridade de failover especificada.|
|**Proteger o BD Cosmos do Azure**||
| [Obter chaves da conta](scripts/secure-get-account-key-powershell.md) | Obtém a chaves de gravação mestre primária e secundária e as chaves somente leitura primária e secundária da conta.|
| [Obter cadeia de conexão do MongoDB](scripts/secure-mongo-connection-string-powershell.md) | Obtém a cadeia de conexão para conectar seu aplicativo MongoDB à sua conta do BD Cosmos do Azure.|
|[Regenerar chaves da conta](scripts/secure-regenerate-key-powershell.md)|Regenera a chave mestre ou somente leitura da conta.|
|[Criar um firewall](scripts/create-firewall-powershell.md)| Cria uma política de controle de acesso IP de entrada para limitar o acesso à conta por um conjunto aprovado de máquinas e/ou serviços de nuvem.|
|**Alta disponibilidade, recuperação de desastre, backup e restauração**||
|[Configurar política de failover](scripts/ha-failover-policy-powershell.md)|Define a prioridade de failover para cada região em que a conta é replicada.|
|||
