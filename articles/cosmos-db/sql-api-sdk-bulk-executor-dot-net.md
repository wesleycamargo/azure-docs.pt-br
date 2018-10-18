---
title: 'Azure Cosmos DB: executor em massa .NET API, SDK & resources | Microsoft Docs'
description: Saiba tudo sobre a API .NET e o SDK do Bulk Executor, incluindo datas de lançamento, datas de aposentadoria e alterações feitas entre cada versão do SDK do .NET do Azure Cosmos DB Bulk Executor.
services: cosmos-db
author: tknandu
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/14/2018
ms.author: ramkris
ms.openlocfilehash: ffd8f438429cd8769ac0dbff7f489327166e0000
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294451"
---
# <a name="net-bulk-executor-library-download-information"></a>Biblioteca do .NET em massa Executor: informações sobre o Download 

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
> * [Executor em massa - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa - Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**Descrição**</td><td>A biblioteca do Executor em massa permite que os aplicativos clientes executem operações em massa nas contas do Azure Cosmos DB. Biblioteca de Executor em massa fornece BulkImport, BulkUpdate e BulkDelete namespaces. O módulo BulkImport pode importar em massa documentos de forma otimizada, de modo que a taxa de transferência provisionada para uma coleção seja consumida até seu limite máximo. O módulo BulkUpdate pode atualizar em massa dados existentes nos contêineres do Azure Cosmos DB como patches. O módulo BulkDelete pode excluir documentos em massa de forma otimizada, de modo que o rendimento provisionado para uma coleção seja consumido em sua extensão máxima.</td></tr>

<tr><td>**Baixe o SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/)</td></tr>

<tr><td>**Biblioteca BulkExecutor no GitHub**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)</td></tr>

<tr><td>**Documentação da API**</td><td>[Documentação de referência de API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)</td></tr>

<tr><td>**Introdução**</td><td>[Introdução ao SDK do .NET da biblioteca do Executor em massa](bulk-executor-dot-net.md)</td></tr>

<tr><td>**Framework atualmente com suporte**</td><td><ul><li>[Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (versão> = 2.0.0)</li><li>
[Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/)(versão > = 9.0.1)
</li></ul></td></tr>
</table></br>

## <a name="release-notes"></a>Notas de versão

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Adicionado suporte para a operação de BulkDelete para contas de API de SQL do Azure Cosmos DB.
* Adicionado suporte para a operação de BulkImport para contas de API do MongoDB do Azure Cosmos DB.
* E aumentado a dependência do SDK .NET do DocumentDB para a versão 2.0.0. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* Adicionado suporte para a operação de BulkImport para contas de API do Gremlin do Azure Cosmos DB.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Pequena correção de bug para a operação BulkImport das contas da API SQL do Azure Cosmos DB.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Adicionado suporte para operações BulkImport e BulkUpdate para contas da API SQL do Azure Cosmos DB.
