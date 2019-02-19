---
title: Unidades de Solicitação e taxa de transferência no Azure Cosmos DB
description: Saiba mais sobre como especificar e estimar os requisitos da Unidade de Solicitação no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: rimman
ms.openlocfilehash: 3801c19fbef70bf5d67670c4d9acc950291853e6
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990148"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unidades de Solicitação no Azure Cosmos DB

Com o Azure Cosmos DB, você paga pela taxa de transferência provisionada e pelo armazenamento que consome por hora. A taxa de transferência deverá ser provisionada para garantir que recursos do sistema suficientes sempre estejam disponíveis para o banco de dados do Azure Cosmos DB. Recursos suficientes são necessários para cumprir ou exceder o SLA do banco de dados do Azure Cosmos DB.

O Azure Cosmos DB dá suporte a muitas APIs como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem seu próprio conjunto de operações de banco de dados. Essas operações variam de simples leituras e gravações de pontos a consultas complexas. Cada operação de banco de dados consome recursos do sistema com base na complexidade da operação. 

O custo de todas as operações do banco de dados é normalizado pelo Azure Cosmos DB e expresso por RUs (Unidades de Solicitação). O custo para leitura de um item de 1 KB é 1 RU (Unidade de Solicitação). Todas as outras operações do banco de dados são atribuídas de maneira semelhante a um custo usando RUs. Independentemente da API usada para interagir com o contêiner do Azure Cosmos DB, os custos sempre serão medidos por RUs. Independentemente da operação do banco de dados ser uma gravação, leitura ou consulta, os custos sempre serão medidos em RUs.

É possível pensar em RUs por segundo como a moeda para a taxa de transferência. RUs por segundo é uma moeda baseada em taxa. Elas extraem os recursos do sistema como CPU, IOPS e memória que são necessários para executar as operações do banco de dados com suporte no Azure Cosmos DB. A imagem a seguir mostra as RUs consumidas por diferentes operações do banco de dados:

![Operações do banco de dados consomem Unidades de Solicitação](./media/request-units/request-units.png)

Para gerenciar e planejar a capacidade, o Azure Cosmos DB garante que o número de RUs para uma determinada operação de banco de dados em um determinado conjunto de dados seja determinístico. Examine o cabeçalho de resposta para rastrear o número de RUs consumidas por qualquer operação de banco de dados. Após reconhecer os fatores que afetam os encargos de RU e os requisitos de taxa de transferência do aplicativo, você poderá executar o custo do aplicativo de forma eficiente.

Você é cobrado por hora. Você provisiona o número de RUs para o aplicativo em uma base por segundo em incrementos de 100 RUs por segundo. Para dimensionar a taxa de transferência provisionada ao aplicativo, você poderá aumentar ou diminuir o número de RUs a qualquer momento. Faça as alterações em incrementos ou decrementos de 100 RUs. É possível fazer as alterações programaticamente ou usando o portal do Azure.

Você pode provisionar a taxa de transferência em duas granularidades distintas: 

* **Contêineres**. Para obter mais informações, consulte [Provisionar taxa de transferência em um contêiner do Azure Cosmos DB](how-to-provision-container-throughput.md).
* **Bancos de dados**. Para obter mais informações, consulte [Provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Considerações sobre Unidade de Solicitação

Enquanto você estima o número de RUs por segundo para provisionar, considere os seguintes fatores:

* **Tamanho do item**: Na medida em que o tamanho de um item aumentar, o número de RUs consumidas para leitura ou gravação do item também aumentará.

* **Indexação de itens**: Por padrão, cada item é indexado automaticamente. Menos RUs serão consumidas se você optar por não indexar alguns dos itens em um contêiner.

* **Contagem de propriedades do item**: Assumindo a indexação padrão em todas as propriedades, o número de RUs consumidas para gravar um item aumentará na medida em que a contagem da propriedade do item aumentar.

* **Propriedades indexadas**: Uma política de índice em cada contêiner determina quais propriedades são indexadas por padrão. Para reduzir o consumo de RU para operações de gravação, limite o número de propriedades indexadas.

* **Consistência de dados**: Os níveis de consistência de desatualização limitada e forte consomem aproximadamente duas vezes mais RUs durante a execução de operações de leitura, quando comparados com outros níveis de consistência flexíveis.

* **Padrões de consulta**: A complexidade de uma consulta afetará quantas RUs serão consumidas por uma operação. Fatores que afetam o custo das operações de consulta incluem: 
    
    - O número de resultados da consulta.
    - O número de predicados.
    - A natureza dos predicados.
    - O número de funções definidas pelo usuário.
    - O tamanho dos dados de origem.
    - O tamanho do conjunto de resultados.
    - Projeções.

  O Azure Cosmos DB garante que a mesma consulta nos mesmos dados sempre custa o mesmo número de RUs em execuções repetidas.

* **Uso de script**: Assim como acontece com as consultas, os procedimentos armazenados e gatilhos consomem RUs com base na complexidade das operações que são executadas. Conforme você desenvolve o aplicativo, inspecione o cabeçalho de encargo de solicitação para reconhecer melhor a capacidade de RU que cada operação consome.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como [provisionar taxa de transferência para contêineres e bancos de dados do Azure Cosmos DB](set-throughput.md).
* Saiba mais sobre [partições lógicas](partition-data.md).
* Saiba mais sobre como [dimensionar taxa de transferência provisionada globalmente](scaling-throughput.md).
* Saiba como [provisionar taxa de transferência em um contêiner do Azure Cosmos DB](how-to-provision-container-throughput.md).
* Saiba como [provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).
