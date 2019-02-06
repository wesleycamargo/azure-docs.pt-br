---
title: Particionamento no BD Cosmos do Azure
description: Visão geral do particionamento no Azure Cosmos DB
ms.author: mjbrown
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: d9bd1bec6d1df6fab634c5d929cb778abbd3d13d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478399"
---
# <a name="partitioning-in-azure-cosmos-db---overview"></a>Particionamento no Azure Cosmos DB – visão geral

O particionamento é a técnica usada pelo Cosmos DB para dimensionar contêineres individuais em um banco de dados para atender às necessidades de desempenho de seu aplicativo. Ao usar o particionamento, os itens em um contêiner são divididos em subconjuntos distintos, chamados de partições lógicas. As partições lógicas são criadas com base no valor de uma propriedade de chave de partição associada a cada item.

Uma partição lógica é um subconjunto distinto de itens em um contêiner. Os itens em uma partição lógica são identificados pelo valor da chave de partição que é compartilhado por todos os itens na partição lógica.  Por exemplo, considere um contêiner que contém documentos e cada documento tem uma propriedade `UserID`.  Se `UserID` for a chave de partição dos itens em um contêiner e houver 1000 valores `UserID` exclusivos, 1000 partições lógicas serão criadas para o contêiner.

Cada item em um contêiner possui uma **chave de partição** que determina a partição **lógica do item** e cada item também possui um **ID de item** (que é exclusivo dentro de uma partição lógica).  O **índice** de um item identifica exclusivamente e ela é formada combinando a chave de partição e a id do item.

A escolha de uma chave de partição é uma decisão importante que afetará o desempenho do seu aplicativo.  Para saber mais, consulte [Escolhendo um artigo da chave de partição](partitioning-overview.md#choose-partitionkey) para obter orientações detalhadas.

## <a name="logical-partition-management"></a>Gerenciamento de partição lógica

O Cosmos DB gerencia de forma transparente e automática o posicionamento de partições lógicas em partições físicas (infraestrutura de servidor) para atender com eficiência as necessidades de escalabilidade e desempenho do contêiner. À medida que os requisitos de taxa de transferência e armazenamento do aplicativo aumentam, o Cosmos DB move as partições lógicas para distribuir automaticamente a carga por um maior número de servidores. Para saber mais sobre como o Cosmos DB gerencia partições, consulte o artigo [partições lógicas](partition-data.md). Não é necessário para p reconhecimento desses detalhes para criar ou executar seus aplicativos.

O Cosmos DB usa particionamento baseado em hash para distribuir partições lógicas em partições físicas.  O valor da chave de partição de um item é codificado pelo Cosmos DB e o resultado com hash determina a partição física. O Cosmos DB aloca o espaço de chave dos hashes de chave de partição uniformemente entre as partições físicas 'N'.

Consultas que acessam dados em uma única partição são mais econômicas do que as consultas que acessam várias partições. Transações (em procedimentos armazenados ou gatilhos) são permitidas somente em itens dentro de uma única partição lógica.  

## <a id="choose-partitionkey"></a>Escolhendo uma chave de partição

Considere os detalhes a seguir ao escolher uma chave de partição:

* Uma única partição lógica tem permissão para um limite superior de 10 GB de armazenamento.  

* Os contêineres particionados são configurados com um rendimento mínimo de 400 RU / s. Solicitações para a mesma chave de partição não podem exceder o rendimento alocado para uma partição. Se eles excederem a taxa de transferência alocada, as solicitações serão limitadas por taxa. Portanto, é importante escolher uma chave de partição que não resulte em "pontos de acesso" em seu aplicativo.

* Escolha uma chave de partição que distribua a carga de trabalho uniformemente em todas as partições e uniformemente ao longo do tempo.  Sua escolha da chave de partição deve equilibrar a necessidade de consultas e / ou transações de partição eficientes com o objetivo de distribuir itens em várias partições para obter escalabilidade.

* Escolha uma chave de partição que tenha uma ampla gama de valores e padrões de acesso distribuídos uniformemente em partições lógicas. A ideia básica é distribuir os dados e a atividade em seu contêiner através do conjunto de partições lógicas, para que os recursos para armazenamento de dados e taxa de transferência possam ser distribuídos entre as partições lógicas.

* Os candidatos a chaves de partição podem incluir as propriedades que aparecem com frequência como um filtro nas suas consultas. Consultas podem ser roteadas com eficiência, incluindo a chave de partição no predicado de filtro.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [partições](partition-data.md)
* Saiba mais sobre a [produtividade provisionada no DB Cosmos do Azure](request-units.md)
* Saiba mais sobre [distribuição global no BD Cosmos do Azure](distribute-data-globally.md)
