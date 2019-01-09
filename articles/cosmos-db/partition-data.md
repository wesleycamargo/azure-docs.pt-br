---
title: Particionamento e escala horizontal no Azure Cosmos DB
description: Saiba mais sobre como o particionamento funciona no DB Cosmos do Azure, como configurar o particionamento e as chaves de partição e como escolher a chave de partição correta para seu aplicativo.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: 38f587fc24478beff3ab236207de3ed8a892c915
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998941"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Particionamento e escala horizontal no Azure Cosmos DB

Este artigo explica sobre partições físicas e lógicas no Azure Cosmos DB e as melhores práticas para dimensionamento e particionamento. 

## <a name="logical-partitions"></a>Partições lógicas

Uma partição lógica é composta por um conjunto de itens com a mesma chave de partição. Por exemplo, considere um contêiner em que todos os itens contêm uma propriedade `City`, então, use `City` como a chave de partição para o contêiner. Grupos de itens com valores específicos para o `City`, como "Londres", "Paris", "NYC" etc. formará uma partição lógica distinta.

No Azure Cosmos DB, um contêiner é a unidade fundamental de escalabilidade. Os dados adicionados ao contêiner e a taxa de transferência que você provisiona no contêiner são automaticamente particionados (horizontalmente) em um conjunto de partições lógicas. Eles são particionados com base na chave de partição especificada para o contêiner do Cosmos. Para saber mais, confira o artigo [como especificar a chave de partição para seu contêiner do Cosmos](how-to-create-container.md).

Uma partição lógica define o escopo das transações de banco de dados. Você pode atualizar itens dentro de uma partição lógica usando uma transação com isolamento de instantâneo.

Quando novos itens são adicionados ao contêiner, ou se a taxa de transferência provisionada no contêiner aumentar, novas partições lógicas serão transparentemente criadas pelo sistema.

## <a name="physical-partitions"></a>Partições físicas

Um contêiner do Cosmos é dimensionado pela distribuição de dados e da taxa de transferência em um grande número de partições lógicas. Internamente, uma ou mais partições lógicas são mapeadas para uma **partição física** composta por um conjunto de réplicas, também conhecido como um conjunto de réplicas. Cada conjunto de réplicas hospeda uma instância do mecanismo de banco de dados do Cosmos. Um conjunto de réplicas torna os dados armazenados dentro da partição física durável, altamente disponível e consistente. Uma partição física oferece suporte a uma quantidade máxima e fixa de armazenamento e RUs. Cada réplica que compõem a partição física herda a cota de armazenamento. E todas as réplicas de uma partição física dão suporte, coletivamente, à taxa de transferência alocada para a partição física. A imagem a seguir mostra como as partições lógicas são mapeadas para partições físicas distribuídas globalmente:

![Particionamento no Azure Cosmos DB](./media/partition-data/logical-partitions.png)

A taxa de transferência provisionada para um contêiner é dividida igualmente entre as partições físicas. Portanto, o design de uma chave de partição que não distribui as solicitações de taxa de transferência uniformemente pode criar partições "dinâmicas". Partições dinâmicas podem resultar no uso ineficiente e limitação de taxa de produtividade provisionada.

Ao contrário das partições lógicas, as partições físicas são uma implementação interna do sistema. Você não pode controlar o tamanho, o posicionamento, a contagem ou o mapeamento entre as partições lógicas e as partições físicas. No entanto, você pode controlar o número de partições lógicas e a distribuição de dados e a taxa de transferência escolhendo a chave de partição correta.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, fornecemos uma visão geral do particionamento de dados e melhores práticas para o dimensionamento e particionamento no Azure Cosmos DB. 

* Saiba mais sobre a [produtividade provisionada no DB Cosmos do Azure](request-units.md)
* Saiba mais sobre [distribuição global no BD Cosmos do Azure](distribute-data-globally.md)
* Saiba mais sobre [escolhendo uma chave de partição](partitioning-overview.md#choose-partitionkey)
* Saiba [como provisionar a taxa de transferência em um contêiner do Cosmos](how-to-provision-container-throughput.md)
* Aprenda [como provisionar a taxa de transferência em um banco de dados Cosmos](how-to-provision-database-throughput.md)
