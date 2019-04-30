---
title: 'Azure Cosmos DB: API Java, SDK e recursos do executor em massa'
description: Saiba tudo sobre o SDK e a API Java de bulk executor, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK Java de bulk executor do Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/21/2018
ms.author: ramkris
ms.openlocfilehash: 51499b86aa44b89442f84a345cba6e9093f8c274
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129478"
---
# <a name="java-bulk-executor-library-download-information"></a>Biblioteca do executor em massa Java: Baixar informações

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed de alterações do .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provedor de recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [bulk executor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [bulk executor – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Descrição**|A biblioteca bulk executor permite que aplicativos cliente executem operações em massa em contas do Azure Cosmos DB. a biblioteca bulk executor fornece os namespaces BulkImport e BulkUpdate. O módulo BulkImport pode importar em massa documentos de forma otimizada, de modo que a taxa de transferência provisionada para uma coleção seja consumida até seu limite máximo. O módulo BulkUpdate pode atualizar em massa dados existentes nos contêineres do Azure Cosmos DB como patches.|
|**Baixe o SDK**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Biblioteca BulkExecutor no GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **Documentação da API**| [Documentação de referência de API .NET](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Introdução**|[Introdução ao SDK Java da biblioteca bulk executor](bulk-executor-java.md)|
|**Tempo de execução mínimo com suporte**|[Java Development Kit (JDK) 7 +](https://aka.ms/azure-jdks)|

