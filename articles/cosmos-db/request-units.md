---
title: Unidades de solicitação e taxa de transferência no Azure Cosmos DB
description: Saiba mais sobre como especificar e estimar os requisitos da unidade de solicitação no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: rimman
ms.openlocfilehash: eabfe503d9b92252ada0014eba4c83390dd6fd97
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51236068"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unidades no banco de dados do Azure Cosmos de solicitação

Com o Azure Cosmos DB, você paga pela taxa de transferência provisionada e pelo armazenamento que consome por hora. A taxa de transferência deve ser provisionada para garantir que recursos do sistema suficientes estejam disponíveis para o banco de dados Cosmos o tempo todo para atender ou exceder o SLA do banco de dados do Cosmos.

O Cosmos DB suporta uma variedade de APIs (SQL, MongoDB, Cassandra, Gremlin e Table). Cada API possui seu próprio conjunto de operações de banco de dados, desde simples leituras e gravações de pontos a consultas complexas. Cada operação de banco de dados consome recursos do sistema, dependendo da complexidade da operação.  O custo de todas as operações do banco de dados é normalizado pelo Cosmos DB e é expresso em termos de Request Units (RUs). O custo para ler um item de 1 KB é 1 Unidade de Solicitação (1 RU). Todas as outras operações de banco de dados são atribuídas de maneira similar a um custo em termos de URs. Independentemente da API que você está usando para interagir com o contêiner Cosmos e a operação do banco de dados (gravação, leitura, consulta), os custos são sempre medidos em termos de RUs.

Você pode pensar em RU / s como a moeda para o rendimento. RU / s é uma moeda baseada em taxa, que abstrai os recursos do sistema, como CPU, IOPS e memória, necessários para executar as operações do banco de dados suportadas pelo Cosmos DB. A imagem a seguir mostra as unidades de solicitação consumidas por diferentes operações do banco de dados:

![Operações de banco de dados consomem unidades de solicitação](./media/request-units/request-units.png)

Para gerenciar e planejar a capacidade, o Cosmos DB garante que o número de RUs para uma determinada operação de banco de dados em um determinado conjunto de dados seja determinístico. Você pode rastrear o número de RUs consumidos por qualquer operação de banco de dados, examinando o cabeçalho de resposta. Depois de entender os fatores que afetam as cobranças da unidade de solicitação e os requisitos de taxa de transferência do seu aplicativo, você poderá executar seu aplicativo de maneira econômica.

Enquanto você é cobrado de hora em hora, provisiona o número de RUs para seu aplicativo em uma base por segundo em incrementos de 100 RU/s. Para dimensionar a taxa de transferência provisionada para seu aplicativo, você pode aumentar ou diminuir o número de RUs (nos incrementos ou decrementos de 100 RUs) a qualquer momento, seja de forma programática ou usando o portal do Azure.

Você pode provisionar a taxa de transferência em duas granularidades distintas: 

* **Contêineres**. Para obter mais informações, consulte [como provisionar a taxa de transferência em um contêiner Cosmos.](how-to-provision-container-throughput.md)
* **Bancos de dados**. Para obter mais informações, consulte [como provisionar a taxa de transferência em um banco de dados Cosmos.](how-to-provision-database-throughput.md)

## <a name="request-unit-considerations"></a>Considerações sobre unidades de solicitação

Ao estimar o número de RU / s para provisão, é importante considerar os seguintes fatores:

* **Tamanho do item** - À medida que o tamanho de um item aumenta, o número de RUs consumidos para ler ou gravar o item também aumenta.

* **Indexação de documentos** - Por padrão, cada item é indexado automaticamente. Menos unidades de solicitação são consumidas se você optar por não indexar alguns de seus itens em um contêiner.

* **Contagem de propriedades do item** - Assumindo a indexação padrão em todas as propriedades, o número de RUs consumidas para gravar um item aumenta à medida que a contagem da propriedade do item aumenta.

* **Propriedades indexadas** - Uma política de índice em cada contêiner determina quais propriedades são indexadas por padrão. Você pode reduzir o consumo de unidade de solicitação para operações de gravação, limitando o número de propriedades indexadas.

* **Consistência de dados** - Os níveis de consistência forte e limitada do staleness consomem aproximadamente 2X mais RUs durante a execução de operações de leitura, quando comparados com outros níveis de consistência relaxada.

* **Padrões de consulta** - A complexidade de uma consulta afeta quantas unidades de solicitação são consumidas para uma operação. O número de resultados da consulta, o número de predicados, a natureza dos predicados, o número de funções definidas pelo usuário, o tamanho dos dados de origem, o tamanho do conjunto de resultados e as projeções afetam o custo das operações de consulta. O Cosmos DB garante que a mesma consulta nos mesmos dados sempre irá custar o mesmo número de RUs em execuções repetidas.

* **Uso de script** - Assim como em consultas, procedimentos armazenados e acionadores consomem RUs com base na complexidade das operações que estão sendo executadas. À medida que você desenvolve sua aplicação, inspecione o cabeçalho de cobrança da solicitação para entender melhor a capacidade da unidade de solicitação consumida por cada operação.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Taxa de transferência de provisionamento para contêineres e bancos de dados do Cosmos DB](set-throughput.md)
* Saiba mais sobre [Partições lógicas](partition-data.md)
* Saiba mais sobre [Colocação em escala de taxa de transferência](scaling-throughput.md)
* Aprenda [como provisionar a taxa de transferência em um contêiner Cosmos](how-to-provision-container-throughput.md)
* Aprenda [como provisionar a taxa de transferência em um banco de dados Cosmos](how-to-provision-database-throughput.md)
