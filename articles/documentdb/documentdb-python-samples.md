<properties 
	pageTitle="Exemplos do Python em NoSQL para o Banco de Dados de Documentos | Microsoft Azure" 
	description="Encontre exemplos do Python em NoSQL no GitHub para tarefas comuns no Banco de Dados de Documentos, incluindo operações CRUD para documentos JSON em bancos de dados NoSQL." 
	keywords="exemplos do python"
	services="documentdb" 
	authors="aliuy" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter="python"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/18/2016" 
	ms.author="andrl"/>


# Exemplos do Python do Banco de Dados de Documentos

> [AZURE.SELECTOR]
- [Exemplos do .NET](documentdb-dotnet-samples.md)
- [Exemplos do Node.js](documentdb-nodejs-samples.md)
- [Exemplos do Python](documentdb-python-samples.md)
- [Galeria de Exemplos de Código do Azure](https://azure.microsoft.com/documentation/samples/?service=documentdb)

Soluções de exemplo que executam operações CRUD e outras operações comuns em recursos do Banco de Dados de Documentos estão incluídas no repositório GitHub [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python/tree/master/samples). Esse artigo fornece:

- Links para as tarefas em cada um dos arquivos do projeto de exemplo do Node.js. 
- Links para o conteúdo de referência da API relacionada.

**Pré-requisitos**

1. Você precisa de uma conta do Azure para usar esses exemplos do Python:
    - Você pode [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/): você recebe créditos que podem ser usados para experimentar serviços pagos do Azure e, mesmo após eles serem utilizados, você pode manter a conta e usar os serviços gratuitos do Azure, como os Sites. Seu cartão de crédito nunca será cobrado, a menos que você altere explicitamente suas configurações, solicitando esse tipo de cobrança.
   - É possível [ativar os benefícios para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/): todos os meses, sua assinatura do Visual Studio concede créditos que podem ser usados para experimentar serviços pagos do Azure.
2. Também é necessário ter o [SDK do Python](documentdb-sdk-python.md). 

    > [AZURE.NOTE] Cada exemplo é independente, eles se configuram e fazem a limpeza sozinhos. Assim, as amostras emitem várias chamadas para [document\_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html). Cada vez que isso é feito, sua assinatura será cobrada por 1 hora de uso por nível de desempenho da coleção que está sendo criada.

## Exemplos de banco de dados

O arquivo [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement/Program.py) do projeto [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement) mostra como executar as tarefas a seguir.

Tarefa | Referência de API
--- | ---
[Criar um banco de dados](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) | [document\_client.CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Consultar uma conta para um banco de dados](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) | [document\_client.QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Ler um banco de dados por ID](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) | [document\_client.ReadDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Listar bancos de dados para uma conta](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) | [document\_client.ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Excluir um banco de dados](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) | [document\_client.DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)

## Exemplos de coleção 

O arquivo [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement/Program.py) do projeto [CollectionManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement) mostra como executar as tarefas a seguir.

Tarefa | Referência de API
--- | ---
[Criar uma coleção](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) | [document\_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Ler uma lista de todas as coleções em um banco de dados](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L225) | [document\_client.ListCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Obter uma coleção por ID](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) | [document\_client.ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Obter o nível de desempenho de uma coleção](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L139-L161) | [DocumentQueryable.QueryOffers](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Alterar o nível de desempenho de uma coleção](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L163-L175) | [document\_client.ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Excluir uma coleção](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) | [document\_client.DeleteCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)

<!---HONumber=AcomDC_0420_2016-->