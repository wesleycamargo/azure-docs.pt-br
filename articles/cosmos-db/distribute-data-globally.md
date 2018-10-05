---
title: Distribuir dados globalmente com o Azure Cosmos DB | Microsoft Docs
description: Aprenda sobre a replicação geográfica em escala de planeta, vários mestres, failover e recuperação de dados usando bancos de dados globais do Azure Cosmos DB, um serviço de banco de dados de vários modelos distribuído globalmente.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.openlocfilehash: 227c243d82665dc533e3bfa6a1fe3e9bb775a262
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408888"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Distribuição de dados global com o Azure Cosmos DB

O Azure é universal: ele tem uma presença global em mais de 50 regiões geográficas e está continuamente em expansão. Com sua presença global e seu suporte de vários mestres, uma das funcionalidades diferenciadas que o Azure oferece aos desenvolvedores é a capacidade de criar, implantar e gerenciar aplicativos distribuídos globalmente com facilidade.

O [Azure Cosmos DB](../cosmos-db/introduction.md) é o serviço multimodelo de banco de dados da Microsoft, distribuído globalmente, para aplicativos críticos. O Azure Cosmos DB fornece distribuição global turnkey, [dimensionamento elástico da produtividade e do armazenamento](../cosmos-db/partition-data.md) no mundo todo, latências de milissegundos de um dígito de leitura e gravação a 99%, [modelos de coerência bem-definidos](consistency-levels.md) e a garantia de alta disponibilidade, tudo isso com suporte de [SLAs abrangentes líderes do setor](https://azure.microsoft.com/support/legal/sla/cosmos-db/). O Azure Cosmos DB [indexa automaticamente todos os seus dados](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sem a necessidade de lidar com o gerenciamento de esquemas ou de índices.

## <a name="global-distribution-with-multi-master"></a>Distribuição global com vários mestres

Como um serviço de nuvem, o Azure Cosmos DB foi cuidadosamente desenvolvido para dar suporte para multilocatário, distribuição global e vários mestres para modelos de dados de documento, chave-valor, grafo e família de colunas.

![Contêiner do Azure Cosmos DB particionado e distribuído em três regiões](./media/distribute-data-globally/global-apps.png)

**Um único contêiner do Azure Cosmos DB particionado e distribuído em várias regiões do Azure**

Como aprendemos durante a criação do Azure Cosmos DB, a adição da distribuição global não pode ser uma consideração posterior. Ela não pode ser “forçada” em um sistema de banco de dados de “site único”. As funcionalidades oferecidas por um banco de dados distribuído globalmente estendem-se além das oferecidas por uma Geo-DR (recuperação de desastre geográfica) tradicional dos bancos de dados de "site único". Bancos de dados de site único que oferecem a capacidade de Geo-DR são um subconjunto restrito de bancos de dados distribuídos globalmente.

Com a distribuição global turnkey do Azure Cosmos DB, os desenvolvedores não precisam criar seu próprio scaffolding de replicação empregando o padrão Lambda no log do banco de dados ou realizando "gravações duplas" em várias regiões. *Não* recomendamos essas abordagens, pois é impossível garantir sua exatidão e fornecer SLAs robustos.

## <a id="Next Steps"></a>Próximas etapas

* [Como o Azure Cosmos DB permite a distribuição global turnkey](distribute-data-globally-turnkey.md)

* [Benefícios da chave de distribuição global do Azure Cosmos DB](distribute-data-globally-benefits.md)

* [Como configurar a replicação do banco de dados global do Azure Cosmos DB](tutorial-global-distribution-sql-api.md)

* [Como ativar o multi-mestre para contas de banco de dados do Azure Cosmos](enable-multi-master.md)

* [Como o failover automático e manual funciona no Azure Cosmos DB](regional-failover.md)

* [Noções básicas sobre a resolução de conflitos no Azure Cosmos DB](multi-master-conflict-resolution.md)

* [Usando vários mestres com bancos de dados NoSQL de software livre](multi-master-oss-nosql.md)
