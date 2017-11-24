---
title: "Tutorial de distribuição global do Azure Cosmos DB para a API de Tabela | Microsoft Docs"
description: "Saiba como configurar a distribuição global do Azure Cosmos DB usando a API de Tabela."
services: cosmos-db
keywords: "distribuição global, Tabela"
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 93e75429d66a30bfc4588a3070e32d58eec0df4b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-table-api"></a>Como configurar a distribuição global do Azure Cosmos DB usando a API de Tabela

Este artigo aborda as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global usando o Portal do Azure
> * Configurar a distribuição global usando a [API de Tabela](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Conectar-se a uma região preferencial usando a API de Tabela

Para aproveitar a [distribuição global](distribute-data-globally.md), os aplicativos cliente podem especificar a lista de preferências ordenadas de regiões a serem usadas para executar operações de documento. Isso pode ser feito definindo o valor de configuração `TablePreferredLocations` no app.config para o SDK da API de Tabela do Azure Cosmos DB. O SDK da API de Tabela do Azure Cosmos DB escolherá o melhor ponto de extremidade para se comunicar com base em configuração de conta, disponibilidade regional atual e a lista de preferência fornecido.

O `TablePreferredLocations` deve conter uma lista separada por vírgulas de locais (hospedagem múltipla) preferenciais para leituras. Cada instância do cliente pode especificar um subconjunto dessas regiões na ordem preferida para leituras de baixa latência. As regiões devem ser nomeadas usando seus [nomes de exibição](https://msdn.microsoft.com/library/azure/gg441293.aspx), por exemplo, `West US`.

Todas as leituras serão enviadas para a primeira região disponível na lista `TablePreferredLocations`. Se a solicitação falhar, o cliente não fará o envio para a próxima região da lista, e assim por diante.

Os SDKs tentarão ler apenas das regiões especificadas em `TablePreferredLocations`. Desse modo, se a Conta do Banco de Dados estiver disponível em três regiões, por exemplo, mas o cliente especificar apenas duas das regiões de não gravação para `TablePreferredLocations`, nenhuma leitura será atendida fora da região de gravação, mesmo no caso de failover.

O SDK enviará automaticamente todas as gravações para a região de gravação atual.

Se a propriedade `TablePreferredLocations` não estiver definida, todas as solicitações serão atendidas na região de gravação atual.

```xml
    <appSettings>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>           
    </appSettings>
```

Assim, concluímos este tutorial. Aprenda a gerenciar a consistência de sua conta globalmente replicada lendo [Níveis de consistência no Azure Cosmos DB](consistency-levels.md). E para saber mais sobre como a replicação de banco de dados global funciona no Azure Cosmos DB, veja [Distribuir dados globalmente com o Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Configurar a distribuição global usando o Portal do Azure
> * Configurar a distribuição global usando a API de Tabela Azure Cosmos DB

