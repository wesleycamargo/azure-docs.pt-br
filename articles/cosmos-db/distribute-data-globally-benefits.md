---
title: Principais benefícios da distribuição global do Azure Cosmos DB
description: Saiba mais sobre os principais benefícios de vários mestres do Azure Cosmos DB oferecidos pela replicação geográfica, por vários mestres e pelos casos de uso, quando necessário.
services: cosmos-db
author: markjbrown
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 11d70a648bfc1882753688e5352835b04cee6b9f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968332"
---
# <a name="distribute-data-globally-with-azure-cosmos-db"></a>Distribuir dados globalmente com o Azure Cosmos DB

Este artigo descreve os principais benefícios da distribuição global de dados e alguns cenários em tempo real em que a distribuição global de dados é necessária.

## <a name="key-benefits"></a>Principais benefícios

A seguir estão os principais benefícios disponíveis para contas que aproveitam as funcionalidades de distribuição global de dados do Azure Cosmos DB:

* **Latência de único dígito** – o Azure Cosmos DB fornece latência de leitura e gravação inferior a 10 ms a 99%, garantida por [SLAs com suporte financeiro](https://azure.microsoft.com/support/legal/sla/cosmos-db/).

* **Disponibilidade de 5 a 9** – o Azure Cosmos DB oferece 99,999% de disponibilidade de leitura e gravação, garantida por [SLAs com suporte financeiro](https://azure.microsoft.com/support/legal/sla/cosmos-db/).

* **Resolução de conflitos flexível** – para os clientes aproveitarem as funcionalidades de vários mestres, o Azure Cosmos DB fornece três modos de tratamento de conflitos para garantir a integridade e coerência global dos dados.

* **Coerência ajustável** – o Azure Cosmos DB dá suporte a cinco [níveis de coerência](consistency-levels.md) diferentes com a distribuição global em mente com suporte para tudo, com exceção da coerência forte com a funcionalidade de vários mestres.

* **Tolerância a falhas implícita**  – usando dados replicados entre várias regiões, os aplicativos podem aproveitar a alta tolerância a falhas regionais.

## <a name="use-cases"></a>Casos de uso

Aqui está uma pequena amostra dos cenários que podem aproveitar as funcionalidades de distribuição global do Azure Cosmos DB.

* **Aplicativos de mídia social** – os aplicativos de mídia social exigem baixa latência, alta disponibilidade e escalabilidade para fornecer uma boa experiência a usuários espalhados pelo mundo todo.

* **IoT**  – as implantações de borda distribuídas geograficamente geralmente precisam acompanhar dados de série temporal de várias localizações. Cada dispositivo pode ser hospedado em sua região mais próxima. Quando dispositivos são movidos para localizações diferentes, eles podem ser hospedados novamente para gravar na região mais próxima disponível.

* **Comércio eletrônico**  – requer muito baixa latência, bem como alta disponibilidade. A distribuição geográfica de dados com leituras e gravações coloca os dados mais próximos aos usuários, aumentando a capacidade de resposta dos aplicativos. A disponibilidade para leituras e gravações em várias regiões fornece maior disponibilidade.

* **Detecção de fraudes/anomalias**  – geralmente os aplicativos que monitoram a atividades de usuário ou atividades de conta são distribuídos globalmente e precisam acompanhar vários eventos ao mesmo tempo para atualizar as pontuações e manter as métricas de risco alinhadas.

* **Medição**  – a contagem e a regulação do uso (como chamadas à API, transações por segundo, minutos usados) podem ser implementadas globalmente de forma simples usando os vários mestres do Azure Cosmos DB. A resolução de conflitos interna garante tanto a precisão de contagens como a regulamentação em tempo real.

## <a name="next-steps"></a>Próximas etapas  

Neste artigo, você aprendeu sobre os principais benefícios e casos de uso das funcionalidades de distribuição global de dados do Azure Cosmos DB. Em seguida, examine os seguintes recursos:

* [Como o Azure Cosmos DB permite a distribuição global turnkey](distribute-data-globally-turnkey.md)

* [Como configurar a replicação de banco de dados global do Azure Cosmos DB](tutorial-global-distribution-sql-api.md)

* [Como habilitar vários mestres para contas do Azure Cosmos DB](enable-multi-master.md)

* [Como funciona o failover automático e manual no Azure Cosmos DB](regional-failover.md)

* [Noções básicas sobre a resolução de conflitos no Azure Cosmos DB](multi-master-conflict-resolution.md)

* [Usando vários mestres com bancos de dados NoSQL de software livre](multi-master-oss-nosql.md)
