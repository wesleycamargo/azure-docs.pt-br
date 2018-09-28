---
title: Usar o Azure Cosmos DB de vários mestres com bancos de dados NoSQL de software livre
description: ''
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ea3a16cf6b5aa4e46ad401e59aacb4d936a7429a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963893"
---
# <a name="using-azure-cosmos-db-multi-master-with-open-source-nosql-databases"></a>Usar o Azure Cosmos DB de vários mestres com bancos de dados NoSQL de software livre

O suporte a vários mestres do Azure Cosmos DB é uma implementação nativa do lado do servidor, que está disponível para todas as ofertas de NoSQL de software livre compatíveis com o Cosmos DB. Ele também é acessível por todos os SDKs compatíveis com o Cosmos DB.

O Azure Cosmos DB é o primeiro serviço do mundo a dar suporte a vários mestres para esses bancos de dados NoSQL de software livre.

|Modelo  |Suporte  |
|---------|---------|
|MongoDB  | Ativo-ativo  |
|Grafo  | Ativo-ativo |
|Cassandra  | Ativo-ativo   |

## <a name="use-mongodb-clients-with-multi-master"></a>Usar clientes do MongoDB com vários mestres

O recurso de vários mestres está habilitado para contas da API do MongoDB, da mesma forma que ele está habilitado para outras APIs do Azure Cosmos DB. Depois de habilitar o recurso de vários mestres para contas da API do MongoDB, cada instância de um aplicativo cliente pode selecionar sua região preferencial para leituras e gravações. Da perspectiva de driver do MongoDB, a região preferencial aparece para o cliente como o primário do conjunto de réplicas. Dessa forma, todas as regiões do seu banco de dados distribuído podem agir como o primário do conjunto de réplicas. O recurso de vários mestres do Azure Cosmos DB permite que você reduza significativamente as latências de gravação para aplicativos do MongoDB distribuídos globalmente. 

### <a name="set-the-primary-region"></a>Definir a região primária

Cada instância de um cliente do MongoDB pode selecionar a região primária acrescentando `@<preferred_primary_region_name>` ao campo "nome do aplicativo", aceito pelo driver do cliente MongoDB. A maioria dos drivers aceita isso na cadeia de conexão, assim como:

`mongodb://fabrikam:KEY@fabrikam.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

Depois de se conectar, pode ocorrer um dos seguintes casos:

* Se o nome da região preferencial está disponível como uma região de gravação, o Azure Cosmos DB a seleciona como a região primária.

* Se um nome de região preferencial não for fornecido, o Azure Cosmos DB selecionará uma região padrão como a região primária.

* Se for um nome de região preferida for fornecido mas não estiver disponível como uma região de gravação para a conta de banco de dados, o Azure Cosmos DB selecionará a região de gravação mais próxima que estiver disponível como a região primária.

Determinados drivers, tais como o driver NodeJS, sempre emitirão gravações primeiro para o host especificado na cadeia de conexão inicial. No caso desses drivers, para garantir que as gravações sejam direcionadas para a região preferencial, além do nome de aplicativo, você deve modificar o nome DNS dentro da cadeia de conexão para incluir o nome da região. Verifique se você especificou o nome da região sem espaços. Por exemplo: 

`mongodb://fabrikam:KEY@fabrikam-westus.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

### <a name="conflict-resolution-mode"></a>Modo de resolução de conflitos

O modo de resolução de conflitos para contas de API do MongoDB do Azure Cosmos DB é sempre do tipo LWW (prevalência da última gravação), usando o carimbo de data/hora do servidor regional que aceitou a gravação.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o suporte ao recurso de vários mestres para contas de API do MongoDB do Azure Cosmos DB. Em seguida, dê uma olhada nos recursos abaixo:

* [Como habilitar o recurso de vários mestres para contas do Azure Cosmos DB](enable-multi-master.md)

* [Noções básicas sobre a resolução de conflitos no Azure Cosmos DB](multi-master-conflict-resolution.md)
