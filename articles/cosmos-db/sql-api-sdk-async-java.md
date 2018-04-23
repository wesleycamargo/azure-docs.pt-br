---
title: 'Azure Cosmos DB: API Java Assíncrona SQL, SDK e recursos| Microsoft Docs'
description: Saiba tudo sobre o SDK e a API Java Assíncrona SQL, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK Java Assíncrono SQL do Azure Cosmos DB.
services: cosmos-db
documentationcenter: java
author: SnehaGunda
manager: kfile
ms.assetid: a452ffa2-c15d-4b0a-a8c1-ec9b750ce52b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 02/28/2018
ms.author: sngun
ms.openlocfilehash: a8e4ac763b9a16210d62fc080aaf8917c1eefc33
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>SDK Java Assíncrono SQL do Azure Cosmos DB para a API SQL: notas de versão e recursos
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
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

O SDK Java Assíncrono de API SQL difere do SDK Java de API SQL fornecendo operações assíncronas com o suporte da [biblioteca Netty](http://netty.io/). O [SDK Java de API SQL](sql-api-sdk-java.md) preexistente não oferece suporte a operações assíncronas. 

<table>

<tr><td>**Baixe o SDK**</td><td>[Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**Documentação da API**</td><td>[Documentação de referência de API Java](https://azure.github.io/azure-cosmosdb-java/)</td></tr>

<tr><td>**Contribuir para o SDK**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Introdução**</td><td>[Introdução ao SDK Java Assíncrono](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**Exemplo de código**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Dicas de desempenho**</td><td>[Leiame do GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Tempo de execução mínimo com suporte**</td><td>[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de versão

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK de GA com suporte de ponta a ponta para E/S sem bloqueio usando a [biblioteca Netty](http://netty.io/) no modo de gateway. 

## <a name="release-and-retirement-dates"></a>Datas de lançamento e desativação
A Microsoft fornecerá uma notificação pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente.

Qualquer solicitação feita ao Cosmos DB usando um SDK desativado será rejeitada pelo serviço.

<br/>

| Versão | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [1.0.0](#1.0.0) |27 de fevereiro de 2018|--- |

## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

