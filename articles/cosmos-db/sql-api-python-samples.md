---
title: Exemplos de Python da API do SQL para o Azure Cosmos DB | Microsoft Docs
description: Encontre exemplos do Python no GitHub para tarefas comuns no Azure Cosmos DB, incluindo operações CRUD.
keywords: Exemplos do Python
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 03/14/2018
ms.author: sngun
ms.openlocfilehash: 87d1e9a988de30d217058ee17cdba876d24ff3e9
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2018
ms.locfileid: "41919474"
---
# <a name="azure-cosmos-db-python-examples"></a>Exemplos do Python no Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Exemplos do .NET](sql-api-dotnet-samples.md)
> * [Exemplos de Java](sql-api-java-samples.md)
> * [Exemplos de Async Java](sql-api-async-java-samples.md)
> * [Exemplos do Node.js](sql-api-nodejs-samples.md)
> * [Exemplos do Python](sql-api-python-samples.md)
> * [Galeria de Exemplos de Código do Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Soluções de exemplo que executam operações CRUD e outras operações comuns em recursos do Azure Cosmos DB estão incluídas no repositório GitHub [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python). Esse artigo fornece:

* Links para as tarefas em cada um dos arquivos do projeto de exemplo do Node.js. 
* Links para o conteúdo de referência da API relacionada.

**Pré-requisitos**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- É possível [ativar os benefícios para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): todos os meses, sua assinatura do Visual Studio concede créditos que podem ser usados para experimentar serviços pagos do Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Também é necessário ter o [SDK do Python](sql-api-sdk-python.md). 
   
   > [!NOTE]
   > Cada exemplo é autossuficiente, eles se configuram e fazem sua limpeza sozinhos. As amostras emitem várias chamadas para [document_client.CreateCollection](https://docs.microsoft.com/python/api/pydocumentdb.document_client.documentclient#createcollection). Cada vez que isso for feito, sua assinatura é cobrada por uma hora de uso. Para obter mais informações sobre a cobrança do Azure Cosmos DB, veja [Preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).
   > 
   > 

## <a name="database-examples"></a>Exemplos de banco de dados
O arquivo [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py) do projeto [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement) mostra como executar as tarefas a seguir:

| Tarefa | Referência de API |
| --- | --- |
| [Criar um banco de dados](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) |[document_client.CreateDatabase](https://docs.microsoft.com/en-us/python/api/pydocumentdb.document_client.documentclient?view=azure-python#createdatabase) |
| [Ler um banco de dados por ID](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) |[document_client.ReadDatabase](https://docs.microsoft.com/en-us/python/api/pydocumentdb.document_client.documentclient?view=azure-python#readdatabase) |
| [Listar bancos de dados para uma conta](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) |[document_client.ReadDatabases](https://docs.microsoft.com/en-us/python/api/pydocumentdb.document_client.documentclient?view=azure-python#readdatabases) |
| [Excluir um banco de dados](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) |[document_client.DeleteDatabase](https://docs.microsoft.com/en-us/python/api/pydocumentdb.document_client.documentclient?view=azure-python#deletedatabase) |

## <a name="collection-examples"></a>Exemplos de coleção
O arquivo [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py) do projeto [CollectionManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement) mostra como executar as tarefas a seguir:

| Tarefa | Referência de API |
| --- | --- |
| [Criar uma coleção](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) |[document_client.CreateCollection](https://docs.microsoft.com/en-us/python/api/pydocumentdb.document_client.documentclient?view=azure-python#createcollection) |
| [Ler uma lista de todas as coleções em um banco de dados](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L209) |[document_client.ReadCollections](https://docs.microsoft.com/en-us/python/api/pydocumentdb.document_client.documentclient?view=azure-python#readcollections) |
| [Obter uma coleção por ID](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) |[document_client.ReadCollection](https://docs.microsoft.com/en-us/python/api/pydocumentdb.document_client.documentclient?view=azure-python#readcollection) |
| [Alterar a taxa de transferência de uma coleção](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L168-L172) | [document_client.ReplaceOffer](https://docs.microsoft.com/en-us/python/api/pydocumentdb.document_client.documentclient?view=azure-python#replaceoffer)|
| [Excluir uma coleção](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) |[document_client.DeleteCollection](https://docs.microsoft.com/en-us/python/api/pydocumentdb.document_client.documentclient?view=azure-python#deletecollection) |

## <a name="document-examples"></a>Exemplos de documento
O arquivo [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py) do projeto [DocumentManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement) mostra como executar as tarefas a seguir:

| Tarefa | Referência de API |
| --- | --- |
| [Criar um documento](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[document_client.CreateDocument](https://docs.microsoft.com/en-us/python/api/pydocumentdb.document_client.documentclient?view=azure-python#createdocument) |
| [Criar uma coleção de documentos](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[document_client.CreateDocuments](https://docs.microsoft.com/en-us/python/api/pydocumentdb.document_client.documentclient?view=azure-python) |
| [Ler um documento por ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L69-L78) |[document_client.ReadDocument](https://docs.microsoft.com/en-us/python/api/pydocumentdb.document_client.documentclient?view=azure-python#readdocument) |
| [Ler todos os documentos em uma coleção](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L81-L92) |[document_client.ReadDocuments](https://docs.microsoft.com/en-us/python/api/pydocumentdb.document_client.documentclient?view=azure-python#readdocuments) |

## <a name="indexing-examples"></a>Exemplos de indexação
O arquivo [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py) do projeto [IndexManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement) mostra como executar as tarefas a seguir:

| Tarefa | Referência de API |
| --- | --- |
| [Usar indexação manual (em vez de automática)](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L245-L246) |[IndexingPolicy.Automatic](https://docs.microsoft.com/en-us/python/api/pydocumentdb.documents?view=azure-python) |
| [Excluir caminhos do documento especificado do índice](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L294-L367) |[IndexingPolicy.ExcludedPaths](https://docs.microsoft.com/en-us/python/api/pydocumentdb.documents.indexingdirective?view=azure-python) |
| [Excluir um documento do índice](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L204-L210) |[documents.IndexingDirective.Exclude](https://docs.microsoft.com/en-us/python/api/pydocumentdb.documents.indexingdirective?view=azure-python) |
| [Definir o modo de indexação](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L533) |[documents.IndexingMode](https://docs.microsoft.com/en-us/python/api/pydocumentdb.documents.indexingmode?view=azure-python) |
| [Usar índices de intervalo em cadeias de caracteres](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L440-L456) |[IndexingPolicy.IncludedPaths](https://docs.microsoft.com/en-us/python/api/pydocumentdb.documents.indexingdirective?view=azure-python) |
| [Executar uma transformação de índice](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L514-L559) |[document_client.ReplaceCollection](https://docs.microsoft.com/en-us/python/api/pydocumentdb.document_client.documentclient?view=azure-python#replacecollection) |

## <a name="query-examples"></a>Exemplos de consulta
Os projetos de exemplo também mostram como executar as seguintes tarefas de consulta:

| Tarefa | Referência de API |
| --- | --- |
| [Consultar uma conta para um banco de dados](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) |[document_client.QueryDatabases](https://docs.microsoft.com/en-us/python/api/pydocumentdb.document_client.documentclient?view=azure-python#querydatabases) |
| [Consulta de documentos](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L152-L169) |[document_client.QueryDocuments](https://docs.microsoft.com/en-us/python/api/pydocumentdb.document_client.documentclient?view=azure-python#querydocuments) |
| [Forçar uma operação de verificação de intervalo em um caminho indexado de hash](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L409-L415) |[http_constants.HttpHeaders.EnableScanInQuery](https://docs.microsoft.com/en-us/python/api/pydocumentdb.http_constants.httpheaders?view=azure-python#enablescaninquery) |
