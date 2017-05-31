---
title: "Introdução ao Azure Cosmos DB: API para MongoDB | Microsoft Docs"
description: "Saiba como você pode usar o Azure Cosmos DB para armazenar e consultar grandes volumes de documentos JSON com baixa latência, usando as conhecidas APIs do MongoDB de OSS."
keywords: "o que é o MongoDB"
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/05/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7dc474f9f940132dd363740e8288a70ee2f2b970
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-cosmos-db-api-for-mongodb"></a>Introdução ao Azure Cosmos DB: API para MongoDB

O [Azure Cosmos DB](../cosmos-db/introduction.md) é o serviço multimodelo de banco de dados da Microsoft, distribuído globalmente, para aplicativos críticos. O Azure Cosmos DB fornece [distribuição global imediata](../documentdb/documentdb-distribute-data-globally.md), [dimensionamento elástico da taxa de transferência e do armazenamento](../documentdb/documentdb-partition-data.md) mundialmente, latências de milissegundos de um dígito no 99º percentil, [cinco níveis de consistência bem-definidos](../documentdb/documentdb-consistency-levels.md) e garantia de alta disponibilidade, tudo isso com suporte de [SLAs líderes do setor](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/). O Azure Cosmos DB [indexa dados automaticamente](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sem a necessidade de lidar com o gerenciamento do esquema e do índice. Ele tem vários modelos e dá suporte a modelos de dados de colunas, gráficos, valores-chave e documentos. 

![API do Azure para MongoDB](./media/documentdb-protocol-mongodb/cosmosdb-mongodb.png) 

Os bancos de dados do DocumentDB podem ser usados como o armazenamento de dados para aplicativos escritos para o [MongoDB](https://docs.mongodb.com/manual/introduction/). Isso significa que, ao usar [drivers](https://docs.mongodb.org/ecosystem/drivers/) existentes, seu aplicativo escrito para o MongoDB agora pode se comunicar com o Cosmos DB e usar bancos de dados do DocumentDB, em vez de bancos de dados do MongoDB. Em muitos casos, é possível mudar o uso do MongoDB para o DocumentDB, bastando alterar uma cadeia de conexão. Com essa funcionalidade, você pode facilmente criar e executar aplicativos de banco de dados do MongoDB na nuvem do Azure com a distribuição global e os [SLAs abrangentes líderes do setor](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) do Azure Cosmos DB e, ao mesmo tempo, continuar usando as ferramentas e competências conhecidas do MongoDB.


## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>Qual é a vantagem de usar o Azure Cosmos DB para aplicativos do MongoDB?

* **Produtividade e armazenamento elasticamente escalonáveis:** aumente ou reduza verticalmente seu banco de dados MongoDB para atender às necessidades de seu aplicativo. Os dados são armazenados em SSD (discos de estado sólido) para fornecer latências baixas previsíveis. O Cosmos DB dá suporte a coleções do MongoDB que podem ser dimensionadas para tamanhos de armazenamento e produtividade provisionada praticamente ilimitados. Você pode dimensionar o Cosmos DB de forma elástica com desempenho previsível à medida que o aplicativo cresce. 

* **Replicação de várias regiões:** o Cosmos DB replica os dados de forma transparente para todas as regiões associadas à sua conta do MongoDB, permitindo que você desenvolva aplicativos que exigem acesso global aos dados e fornecendo compensações entre consistência, disponibilidade e desempenho, tudo isso com garantias correspondentes. O Cosmos DB fornece failover regional transparente com APIs de hospedagem múltipla e a capacidade de dimensionar de forma elástica a produtividade e o armazenamento em todo o mundo. Saiba mais em [Distribuir dados globalmente](documentdb-distribute-data-globally.md).

**Compatibilidade com o MongoDB**: use os conhecimentos, o código do aplicativo e as ferramentas do MongoDB que você já tem. Desenvolva aplicativos usando o MongoDB e implante-os em produção usando o serviço Cosmos DB distribuído globalmente e totalmente gerenciado.

**Sem gerenciamento de servidor**: você não precisa gerenciar nem dimensionar os bancos de dados do MongoDB. O Cosmos DB é um serviço totalmente gerenciado, o que significa que você não precisa gerenciar infraestruturas ou Máquinas Virtuais por conta própria. O Cosmos DB está disponível em mais de 30 [Regiões do Azure](https://azure.microsoft.com/regions/services/).

* **Níveis de consistência ajustáveis:** escolha entre cinco níveis de consistência bem-definidos para chegar ao equilíbrio ideal entre consistência e desempenho. Para operações de consulta e leitura, o Cosmos DB oferece cinco níveis de consistência distintos: forte, desatualização limitada, sessão, prefixo constante e eventual. Esses níveis de consistência granulares e bem-definidos permitem que você faça compensações seguras entre consistência, disponibilidade e latência. Saiba mais em [Como usar níveis de consistência para maximizar a disponibilidade e o desempenho](documentdb-consistency-levels.md).

* **Indexação automática**: por padrão, o Cosmos DB indexa automaticamente todas as propriedades em documentos do banco de dados do MongoDB e não espera nem exige nenhum esquema ou a criação de índices secundários.

**Nível empresarial**: o Azure Cosmos DB dá suporte a várias réplicas locais para proporcionar 99,99% de disponibilidade e proteção de dados em caso de falhas locais e regionais. O Azure Cosmos DB apresenta recursos de segurança e [certificações de conformidade](https://www.microsoft.com/trustcenter) de nível empresarial. 

Saiba mais neste vídeo do Azure Friday com Scott Hanselman e o Gerente Principal de Engenharia do Azure Cosmos DB, Kirill Gavrylyuk.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/DocumentDB-Database-as-a-Service-for-MongoDB-Developers/player]
> 

## <a name="how-to-get-started"></a>Como começar

Crie uma conta do Azure Cosmos DB no [Portal do Azure](https://portal.azure.com) e troque a cadeia de conexão do MongoDB pela sua nova conta. 

*É só isso!*

Para obter instruções detalhadas, siga [criar conta](documentdb-create-mongodb-account.md) e [conectar-se à sua conta](documentdb-connect-mongodb-account.md).

## <a name="next-steps"></a>Próximas etapas

As informações sobre a API do MongoDB no Azure Cosmos DB são integradas à documentação geral do Azure Cosmos DB, mas estas são algumas dicas para ajudá-lo a começar:

* Siga o tutorial [Conectar-se a uma conta do MongoDB](documentdb-connect-mongodb-account.md) para saber como obter informações sobre a cadeia de conexão da sua conta.
* Siga o tutorial [Usar o MongoChef com o Azure Cosmos DB](documentdb-mongodb-mongochef.md) para saber como criar uma conexão entre o banco de dados do Azure Cosmos DB e o aplicativo MongoDB no MongoChef.
* Siga o tutorial [Migrar dados para o Azure Cosmos DB com suporte de protocolo para o MongoDB](documentdb-mongodb-migrate.md) para importar seus dados para um banco de dados da API para MongoDB.
* Criar seu primeiro aplicativo da API para MongoDB usando [Node.js](documentdb-mongodb-samples.md).
* Criar seu primeiro aplicativo Web da API para MongoDB usando [.NET](documentdb-mongodb-application.md).
* Conectar-se a uma conta da API para MongoDB usando o [Robomongo](documentdb-mongodb-robomongo.md).
* Saiba quantos RUs suas operações estão usando com o [comando GetLastRequestStatistics e as métricas do Portal do Azure](documentdb-request-units.md#GetLastRequestStatistics).
* Saiba como [configurar preferências de leitura para aplicativos distribuídos globalmente](../cosmos-db/tutorial-global-distribution-mongodb.md).

