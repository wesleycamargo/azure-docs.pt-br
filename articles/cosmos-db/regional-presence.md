---
title: Presença regional com Azure Cosmos DB
description: Este artigo explica sobre a presença regional do Azure Cosmos DB e diferentes ambientes de nuvem.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 2062862670f5b373d3153c473e45455e10d5e996
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548673"
---
# <a name="regional-presence-of-azure-cosmos-db"></a>Presença regional do Azure Cosmos DB

Atualmente, o Azure está disponível em [54 regiões](https://azure.microsoft.com/global-infrastructure/regions/) em todo o mundo. O Azure Cosmos DB é um serviço de base no Azure e está disponível em todas as regiões onde o Azure está disponível.

[![Regiões onde o Azure Cosmos DB está disponível](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

O Cosmos DB está disponível em todos os cinco ambientes distintos de nuvem do Azure disponíveis para os clientes:

* **Nuvem pública do Azure**, que está disponível globalmente.

* **Azure China** está disponível por meio de uma parceria exclusiva entre a Microsoft e a 21Vianet, um dos maiores provedores de internet do país.

* **Azure Alemão** fornece serviços sob um modelo de truste de dados, que garante que os dados de clientes permaneçam na Alemanha sob o controle da T-Systems International GmbH, uma subsidiária da Deutsche Telecom, atuando como depositária de dados alemã.

* **Azure Governamental** está disponível em quatro regiões nos Estados Unidos para agências do governo dos EUA e seus parceiros. 

* O **Azure Governamental para o Departamento de Defesa (DoD)** está disponível em duas regiões dos Estados Unidos para o Departamento de Defesa dos EUA.

## <a name="regional-presence-with-global-distribution"></a>Presença regional com a distribuição global

Diferentes APIs expostas pelo Azure Cosmos DB (incluindo armazenamento em SQL, MongoDB, Cassandra, Gremlin e Azure Table) estão disponíveis em todas as regiões do Azure. Por exemplo, você pode ter as APIs do MongoDB e do Cassandra expostas pelo Azure Cosmos DB não apenas em todas as regiões globais do Azure, mas também em regiões soberanas do Azure como as regiões da China, Alemanha, Governo e Departamento de Defesa (DoD).

O Azure Cosmos DB é um banco de dados [globalmente distribuído](distribute-data-globally.md). Você pode associar qualquer número de regiões do Azure à sua conta do Azure Cosmos e seus dados serão replicados de maneira automática e transparente. Você pode adicionar ou remover uma região da sua conta do Azure Cosmos a qualquer momento. Com o recurso de distribuição global turnkey e o protocolo de replicação multi-masterizado, o Azure Cosmos DB oferece latências de leitura e gravação inferiores a 10 ms no 99º percentil, 99,999 leitura e disponibilidade de gravação e capacidade de dimensionar de forma elástica o throughput provisionado para leituras e gravações em todos os regiões associadas à sua conta do Azure Cosmos. O Azure Cosmos DB também oferece cinco modelos de consistência bem definidos e você pode optar por aplicar um modelo de consistência específico aos seus dados. Por fim, o Azure Cosmos DB é o único serviço de banco de dados do setor que fornece um SLA (Contrato de Nível de Serviço) abrangente, abrangendo taxa de transferência provisionada, latência no 99º percentil, alta disponibilidade e consistência.

## <a name="next-steps"></a>Próximas etapas

Agora você pode aprender sobre diferentes conceitos do Azure Cosmos DB com os seguintes artigos:

* [Distribuição de dados globais](distribute-data-globally.md)
* [Como gerenciar uma conta do Azure Cosmos DB](manage-account.md)
* [Taxa de transferência de provisão para contêineres do Cosmos do Azure e bancos de dados](set-throughput.md)
* [SLA do Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
