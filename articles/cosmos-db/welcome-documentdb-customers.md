---
title: Clientes do DocumentDB, bem-vindos ao Azure Cosmos DB | Microsoft Docs
description: "Saiba mais sobre a publicação feita na //compilação 2017, na qual os clientes do DocumentDB passaram a ser clientes do Azure Cosmos DB."
services: cosmosdb
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.devlang: n/a
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 05/12/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: f60c3502ed51424647088fa8109f13cabc4e73da
ms.contentlocale: pt-br
ms.lasthandoff: 05/17/2017

---

# <a name="welcome-documentdb-customers-to-azure-cosmos-db"></a>Clientes do DocumentDB, sejam bem-vindos ao Azure Cosmos DB

Clientes do DocumentDB, estamos felizes em informar que agora vocês fazem parte da família Azure Cosmos DB! 

O Azure Cosmos DB, [anunciado na conferência Microsoft Build 2017](https://azure.microsoft.com/blog/azure-cosmos-db-microsofts-globally-distributed-multi-model-database-service/), é o primeiro serviço de banco de dados distribuídos globalmente, que oferece escalonamento elástico de taxas de transferência, armazenamento em diversas regiões geográficas garantindo a baixa latência, alta disponibilidade e consistência suportadas pelos SLAs mais abrangentes do setor e com suporte a uma variedade de modelos de dados e APIs. 

![O Azure Cosmos DB é serviço de banco de dados distribuído globalmente da Microsoft com escala horizontal elástica, baixa latência garantida, cinco modelos de consistência e SLAs de garantia abrangente](./media/welcome-documentdb-customers/azure-cosmos-db.png)

Uma das APIs que o Azure Cosmos DB suporta é a API do DocumentDB e o modelo de dados do documento. Você já está familiarizado com as APIs do DocumentDB. Elas são as APIs que você já está usando para executar seus aplicativos atuais do DocumentDB. Essas APIs _não_ foram alteradas. O pacote do NuGet, os namespaces e todas as dependências permanecem como estão. **Você não precisa alterar nada** para continuar a executar seus aplicativos criados com a API do DocumentDB. O Azure Cosmos DB é simplesmente o nome do serviço do qual eles agora fazem parte. 

Vamos sanar algumas dúvidas que possam surgir. 

## <a name="why-move-to-azure-cosmos-db"></a>Por que mudar para o Azure Cosmos DB? 

O Azure Cosmos DB é o próximo grande salto em distribuição global, em grande escala, de bancos de dados em nuvem. Como um cliente do DocumentDB, agora você tem acesso ao novo sistema inovador de recursos oferecidos pelo Azure Cosmos DB.

O Azure Cosmos DB começou como o "Project Florence" em 2010 para solucionar as adversidades que os desenvolvedores enfrentam com aplicativos de grande escala dentro da Microsoft. Ao perceber que os desafios de criação de aplicativos distribuídos globalmente não são um problema exclusivo para a Microsoft, em 2015, disponibilizamos a primeira geração dessa tecnologia aos desenvolvedores do Azure por meio do Azure DocumentDB. 

Desde então, nós adicionados novos recursos e aumentamos a gama de possibilidades.  Como resultado, surgiu o Azure Cosmos DB.  Nesta versão do Azure Cosmos DB, os clientes do DocumentDB (assim como seus dados) se tornaram automaticamente clientes do Azure Cosmos DB. A transição é contínua e, agora, você tem acesso ao novo sistema inovador e aos recursos oferecidos pelo Azure Cosmos DB. Esses recursos estão nas áreas do mecanismo de banco de dados principal, bem como distribuição global, escalabilidade elástica e SLAs líderes do mercado abrangentes. Especificamente, desenvolvemos o mecanismo do banco de dados do Azure Cosmos DB para conseguir mapear com eficiência todos os modelos de dados populares, sistemas de tipos e APIs do modelo de dados anterior do Azure Cosmos DB. 

O desenvolvedor atual, que encara manifestação desse trabalho, é o novo suporte para o [Gremlin](graph-introduction.md) e as [APIs de armazenamento de tabelas](table-introduction.md). Mas isso é apenas o começo… Vamos adicionar outras APIs populares e modelos de dados mais recentes ao longo do tempo com aprimoramentos voltados para o desempenho e armazenamento em escala global. 

É importante ressaltar que o [dialeto SQL](../documentdb/documentdb-sql-query.md) do DocumentDB sempre foi apenas uma das muitas APIs que o banco de dados anterior, Cosmos DB, foi capaz de suportar. Como um desenvolvedor que usa um serviço completamente gerenciado como Azure Cosmos DB, a única interface para o serviço são as APIs expostas pelo serviço. Para esse fim, nada muda para você como um cliente existente do DocumentDB. O Azure Cosmos DB oferece exatamente a mesma API do SQL que o DocumentDB. No entanto, agora, e no futuro, você pode acessar outros recursos que estavam inacessíveis. 

Outra manifestação do nosso trabalho contínuo é a base estendida para escalabilidade elástica de uma taxa de transferência e armazenamento globais. Uma das primeiras manifestações é o [RU/m](request-units-per-minute.md), mas anunciaremos mais recursos nessas áreas. Esses novos recursos ajudam a reduzir os custos para nossos clientes para várias cargas de trabalho. Fizemos vários aprimoramentos fundamentais para o subsistema de distribuição global. Um dos muitos desenvolvedores que encaram manifestações desse trabalho é o modelo de consistência de prefixo consistente (totalizando em cinco modelos consistentes bem-definidos). No entanto, há muitos recursos mais interessantes, que serão lançados quando estiverem perfeitamente desenvolvidos. 

## <a name="what-do-i-need-to-do-to-ensure-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>O que é devo fazer para garantir que os meus recursos do DocumentDB sejam executados no Azure Cosmos DB?

Nada. Você não precisa fazer nenhuma alteração. Agora, os recursos do DocumentDB são recursos do Azure Cosmos DB, e não houve interrupção durante a transição do serviço.

## <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>Quais alterações devem ser feitas para que o meu aplicativo funcione no Azure Cosmos DB?

Você não precisa fazer nenhuma alteração. Nomes de pacote do NuGet, namespaces e classes não foram alterados. Como sempre, é recomendável que você mantenha seus SDKs atualizados para tirar proveito dos recursos e aprimoramentos mais recentes. 

## <a name="whats-changed-in-the-azure-portal"></a>O que mudou no portal do Azure?

O Azure DocumentDB não aparece mais no portal como um serviço do Azure. Em vez disso, o Azure Cosmos DB aparece com um novo ícone, conforme mostrado na imagem a seguir. Todas as coleções estão disponíveis, como sempre estiveram, e você ainda pode dimensionar a taxa de transferência, consistência de alteração e monitorar as SLAs. Os recursos do **Data Explorer (versão prévia)** foram aprimorados. Agora, você pode exibir e editar documentos, criar e executar consultas e trabalhar com procedimentos armazenados, gatilhos e UDF de uma página, conforme mostrado na imagem a seguir. 

![Como exibir e copiar uma chave de acesso no portal do Azure e folha chaves](./media/welcome-documentdb-customers/cosmos-db-data-explorer.png)

## <a name="are-there-changes-to-pricing"></a>Há alterações de preços?

Não, o custo da execução do aplicativo no Azure Cosmos DB continua o mesmo. No entanto, você pode se beneficiar da nova **unidade de solicitação de recurso por minuto** que anunciamos. Para obter mais informações, confira o artigo [Escala de taxa de transferência por minuto](request-units-per-minute.md).

## <a name="are-there-changes-to-the-service-level-agreements-slas"></a>Há alterações nos Contratos de nível de serviço (SLAs)?

Não, os SLAs para disponibilidade, consistência, latência e taxa de transferência continuam os mesmos e são exibidos no portal. Para obter detalhes sobre os SLAs, confira [SLA para o Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/).
   
![Aplicativo de tarefas pendentes com dados de exemplo](./media/welcome-documentdb-customers/azure-cosmosdb-portal-metrics-slas.png)

## <a name="whats-next-with-azure-cosmos-db"></a>O que vem a seguir com o Azure Cosmos DB?

O Azure Cosmos DB é um serviço de banco de dados em evolução constante. Todos os novos recursos são validados em aplicativos de larga escala dentro da Microsoft, subsequentemente expostos aos principais clientes externos e, finalmente, lançados para o mundo. 

O novo suporte de API de tabela e Gremlin são só os primeiros modelos de dados e APIs populares suportados pelo Azure Cosmos DB e estamos ansiosos para compartilhar novos modelos de dados com você conforme forem anunciados. Além disso, continuamos a fazer grandes avanços no desempenho e armazenamento em escala global. 

Como sempre, agradecemos por você ser cliente do Azure DocumentDB e estamos felizes em compartilhar o Azure Cosmos DB com vocês, que são nossos desenvolvedores e clientes no mundo inteiro. A nossa missão é ser o serviço de banco de dados mais confiável do mundo e permitir que você crie aplicativos maravilhosos em escala no Cosmos com facilidade. Experimente os novos recursos no Azure Cosmos DB e envie os seus comentários!  Mal podemos esperar para ver as suas criações com o Cosmos DB.

— Seus amigos do Azure Cosmos DB [@AzureCosmosDB](https://twitter.com/AzureCosmosDB)

