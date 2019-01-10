---
title: Expirar dados no Azure Cosmos DB com Vida Útil
description: Com a TTL, o Microsoft Azure Cosmos DB fornece a capacidade de limpar documentos automaticamente do sistema após determinado período.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: fbbaefc62adedc2374c47fd0736368d3dec3e6a4
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043423"
---
# <a name="time-to-live-for-azure-cosmos-db-data"></a>Vida Útil dos dados do Azure Cosmos DB

Com a "Vida útil" ou TTL, o Azure Cosmos DB fornece a capacidade de excluir itens automaticamente de um contêiner após um determinado período de tempo. Por padrão, é possível definir a Vida Útil no nível do contêiner e substituir o valor em uma base por item. Após definir a Vida Útil em um nível de item ou contêiner, o Azure Cosmos DB removerá automaticamente esses itens após o período de tempo, desde a hora em que foram modificados pela última vez. O valor de Vida Útil é configurado em segundos. Ao configurar a Vida Útil, o sistema excluirá automaticamente os itens expirados com base no valor de TTL, diferentemente de uma operação de exclusão explicitamente emitida pelo aplicativo cliente.

## <a name="time-to-live-for-containers-and-items"></a>Vida Útil para contêineres e itens

O valor de Vida Útil é definido em segundos e é interpretado como um delta a partir do momento em que um item foi modificado pela última vez. É possível definir a Vida Útil em um contêiner ou um item dentro do contêiner:

1. **Vida Útil em um contêiner** (definir usando `DefaultTimeToLive`):

   - Se ausentes (ou definidos como nulo), os itens não serão expirados automaticamente.

   - Se estiverem presentes e o valor estiver definido como "-1", será igual a infinito – os itens não expiram por padrão.

   - Se estiverem presentes e o valor estiver definido para algum número ("n") – os itens expirarão em "n" segundos após o último horário de modificação.

2. **Vida Útil em um item** (definir usando `TimeToLive`):

   - Essa Propriedade será aplicável somente se `DefaultTimeToLive` estiver presente e não estiver definido como nulo para o contêiner pai.

   - Se presente, substituirá o valor `DefaultTimeToLive` do contêiner pai.

## <a name="time-to-live-configurations"></a>Configurações de Vida Útil

* Se a TTL estiver definida como 'n' em um contêiner, os itens desse contêiner expirarão após n segundos.  Se houver itens no mesmo contêiner que tenham sua própria vida útil, definida como -1 (indicando que não expiram) ou se alguns itens substituíram a configuração de vida útil por um número diferente, esses itens expirarão com base na configuração do valor de TTL. 

* Se a TTL não estiver definida em um contêiner, o tempo de vida de um item nesse contêiner não terá efeito. 

* Se a TTL em um contêiner for definida como -1, um item nesse contêiner que tenha a vida útil definida como n expirará após n segundos, e os itens restantes não expirarão. 

A exclusão de itens com base na TTL é gratuita. Não haverá custos adicionais (isto é, nenhuma RU adicional será consumida) quando o item for excluído como resultado da expiração de TTL.

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar a Vida Útil, consultando os seguintes artigos:

* [Como configurar a Vida Útil](how-to-time-to-live.md)
