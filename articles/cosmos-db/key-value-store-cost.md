---
title: "Azure Cosmos DB como um repositório de valores de chave – Visão geral do custo | Microsoft Docs"
description: "Saiba mais sobre o baixo custo do uso do Azure Cosmos DB como um repositório de valores de chave."
keywords: "repositório de valores de chave"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: 
documentationcenter: 
ms.assetid: 7f765c17-8549-4509-9475-46394fc3a218
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: e6f87cd82ebe31965fcaac1f66d2df03fd58294f
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB como um repositório de valores de chave – Visão geral do custo

O Azure Cosmos DB é um serviço de multimodelo de banco de dados distribuído globalmente para a criação fácil de aplicativos altamente disponíveis de grande escala. Por padrão, o Azure Cosmos DB indexa automaticamente e com eficiência todos os dados ingeridos. Isso permite consultas [SQL](sql-api-sql-query.md) (e [JavaScript](programming.md)) rápidas e consistentes em qualquer tipo de dados. 

Este artigo descreve o custo do Azure Cosmos DB para operações simples de gravação e leitura quando ele é usado como um repositório de chaves/valores. Operações de gravação incluem inserções, substituições, exclusões e upserts de documentos. Além de garantir uma 99,99% SLA de disponibilidade para todas as contas de região única e todas as contas de várias regiões com consistência reduzida e 99,999% disponibilidade de leitura em todas as contas de banco de dados de várias regiões, o Azure Cosmos DB oferece garantidamente < latência de 10 ms para leituras e < latência de 15 ms para gravações (indexadas), respectivamente, no 99º percentual. 

## <a name="why-we-use-request-units-rus"></a>Por que usamos RUs (Unidades de Solicitação)

O desempenho do Azure Cosmos DB se baseia na quantidade de [RUs](request-units.md) (Unidades de Solicitação) provisionadas para a partição. O provisionamento ocorre a uma granularidade de segundos e é adquirido em RUs/s ([não deve ser confundido com a cobrança por hora](https://azure.microsoft.com/pricing/details/cosmos-db/)). RUs devem ser considerados como uma moeda que simplifica o provisionamento da taxa de transferência necessária para o aplicativo. Nossos clientes não precisam pensar em diferenciar unidades de capacidade de leitura e gravação. O modelo de moeda única de RUs cria eficiência no compartilhamento da capacidade provisionada entre leituras e gravações. Esse modelo de capacidade provisionada permite que o serviço forneça uma taxa de transferência previsível e consistente, além de garantia de baixa latência e alta disponibilidade. Finalmente, usamos o RU para modelar a taxa de transferência, mas cada RU provisionado também tem uma quantidade definida de recursos (Memória, Núcleo). RU/s não é apenas o IOPS.

Por ser um sistema de banco de dados distribuído globalmente, o Cosmos DB é o único serviço do Azure que fornece um SLA para latência, produtividade e consistência, além de alta disponibilidade. A produtividade que você provisiona é aplicada a cada uma das regiões associadas à sua conta de banco de dados do Cosmos DB. Para leituras, o Cosmos DB oferece vários [níveis de consistência](consistency-levels.md) bem definidos para sua escolha. 

A tabela a seguir mostra o número de RUs necessários para realizar transações de leitura e gravação com base no tamanho de documento de 1 KB e 100 KBs.

|Tamanho do Item|1 Leitura|1 Gravação|
|-------------|------|-------|
|1 KB|1 RU|5 RUs|
|100 KB|10 RUs|50 RUs|

## <a name="cost-of-reads-and-writes"></a>Custo de leituras e gravações

Se você provisionar 1.000 RU/s, o resultado será de 3,6 m RU/hora e custará $0,08 por hora (nos EUA e Europa). Para um documento com 1KB, isso significa que você pode consumir 3,6 m de leituras ou 0,72 m de gravações (3,6m RU/5) usando sua taxa de transferência provisionada. Ao normalizar para milhões de leituras e gravações, o custo seria de $0,022/m leituras ($0,08 / 3,6) e $0,111/m de gravações ($0,08 / 0,72). O custo por milhão se torna mínimo, conforme mostra a tabela a seguir.

|Tamanho do Item|1 m leitura|1 m gravação|
|-------------|-------|--------|
|1 KB|$ 0,022|$ 0,111|
|100 KB|$ 0,222|$ 1,111|


A maioria dos repositórios de blob ou de objeto básicos cobra $ 0,40 por um milhão de transações de leitura, e $ 5 por um milhão de transações de gravação. Se for usado de forma adequada, o Cosmos DB poderá ser até 98% mais barato do que essas outras soluções (para transações de 1 KB).

## <a name="next-steps"></a>Próximas etapas

Fique atento a novos artigos sobre como otimizar o provisionamento de recursos do Azure Cosmos DB. Enquanto isso, fique à vontade para usar nossa [Calculadora de RU](https://www.documentdb.com/capacityplanner).

