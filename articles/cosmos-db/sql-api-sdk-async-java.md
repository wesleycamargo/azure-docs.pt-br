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
ms.date: 05/18/2018
ms.author: sngun
ms.openlocfilehash: 9dae401bc007b78d8ee3c6993735650e3b26b9d1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359519"
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
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

O SDK Java Assíncrono de API SQL difere do SDK Java de API SQL fornecendo operações assíncronas com o suporte da [biblioteca Netty](http://netty.io/). O [SDK Java de API SQL](sql-api-sdk-java.md) preexistente não oferece suporte a operações assíncronas. 

<table>

<tr><td>**Baixe o SDK**</td><td>[Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**Documentação da API**</td><td>[Documentação de referência de API Java](https://azure.github.io/azure-cosmosdb-java/)</td></tr>

<tr><td>**Contribuir para o SDK**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Introdução**</td><td>[Introdução ao SDK Java Assíncrono](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**Exemplo de código**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Dicas de desempenho**</td><td>[Leiame do GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Tempo de execução mínimo com suporte**</td><td>[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de versão

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* Suporte adicionado para Política de Índice Exclusivo.
* Suporte adicionado para limitação do tamanho do token de continuação do resposta em opções de feed.
* Suporte adicionado para a Divisão de Partição na consulta de Partição Cruzada.
* Correção de bug na Serialização de carimbo de data/hora Json ([github #32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Correção de bug na serialização enum Json.
* Correção de bug no gerenciamento de documentos de tamanho de 2MB ([github 33 #](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Dependência com.fasterxml.jackson.core:jackson-databind atualizado para 2.9.5 devido a um bug ([jackson databind: github 1599 #](https://github.com/FasterXML/jackson-databind/issues/1599))
* Dependência rxjava-extras atualizado para 0.8.0.17 devido a um bug ([rxjava extras: github 30 #](https://github.com/davidmoten/rxjava-extras/issues/30)).
* A descrição de metadados no arquivo pom atualizada para estar embutido com o restante da documentação.
* Aperfeiçoamento de sintaxe ([github #41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([github 40 #](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Suporte de pressão contrária adicionado na consulta.
* Suporte adicionado para ID de intervalo de chaves de partição na consulta.
* Correção para permitir token de continuação maior no cabeçalho de solicitação (bugfix github #24).
* Dependência netty atualizada para 4.1.22.Final para garantir que a JVM será desligada após a conclusão do thread principal.
* Correção para evitar transmitir o token de sessão ao ler recursos mestres.
* Mais exemplos adicionados.
* Mais cenários de parâmetros de comparação adicionados.
* Arquivos de cabeçalho de Java fixos para a geração de java doc apropriada.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK de GA com suporte de ponta a ponta para E/S sem bloqueio usando a [biblioteca Netty](http://netty.io/) no modo de gateway. 

## <a name="release-and-retirement-dates"></a>Datas de lançamento e desativação
A Microsoft fornecerá uma notificação pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Novos recursos e funcionalidade e otimizações são adicionados apenas ao SDK atual. Portanto, é recomendável que você sempre atualize para a versão mais recente do SDK o quanto antes.

Qualquer solicitação feita ao Cosmos DB com o uso de um SDK desativado será rejeitada pelo serviço.

<br/>

| Versão | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [1.0.2](#1.0.2) |18 de maio de 2018|--- |
| [1.0.1](#1.0.1) |20 de abril de 2018|--- |
| [1.0.0](#1.0.0) |27 de fevereiro de 2018|--- |

## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

