---
title: Testes de escala e desempenho do Azure Cosmos DB | Microsoft Docs
description: Saiba como realizar testes de desempenho e escala com o Azure Cosmos DB
keywords: testes de desempenho
services: cosmos-db
author: arramac
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f4c96ebd-f53c-427d-a500-3f28fe7b11d0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: a3f2e8634981ac158312e6b9700db92415e9e6c5
ms.contentlocale: pt-br
ms.lasthandoff: 06/20/2017


---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Teste de desempenho e escala com o Azure Cosmos DB
O teste de desempenho e escalabilidade é uma etapa importante no desenvolvimento de aplicativos. Para muitos aplicativos, a camada de banco de dados tem um impacto significativo sobre o desempenho e a escalabilidade geral e, portanto, é um componente essencial do teste de desempenho. O [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) foi desenvolvido para fins de escala elástica e desempenho previsível e, portanto, é uma excelente opção para aplicativos que precisam de uma camada de banco de dados de alto desempenho. 

Este artigo é uma referência para desenvolvedores que implementam conjuntos de testes de desempenho para suas cargas de trabalho do Cosmos DB ou que avaliam o Cosmos DB para cenários de aplicativos de alto desempenho. Ele se concentra principalmente nos testes de desempenho de banco de dados isolados, mas também inclui práticas recomendadas para aplicativos de produção.

Depois de ler este artigo, você poderá responder as seguintes perguntas:   

* Onde posso encontrar um aplicativo cliente do .NET de exemplo para testar o desempenho do Azure Cosmos DB? 
* Como fazer para obter altos níveis de produtividade com o Azure Cosmos DB em meu aplicativo cliente?

Para começar a codificar, baixe o projeto na [Exemplo de teste de desempenho do Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> A meta desse aplicativo é demonstrar as melhores práticas para extrair um melhor desempenho do Cosmos DB com um número menor de computadores cliente. Ele não foi feito para demonstrar a capacidade máxima do serviço, que pode ser dimensionada sem limites.
> 
> 

Se você estiver procurando opções de configuração do lado do cliente para melhorar o desempenho do Cosmos DB, consulte [Dicas de desempenho do Azure Cosmos DB](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Execute o aplicativo de teste de desempenho
A maneira mais rápida de começar é compilar e executar o exemplo do .NET abaixo, conforme descrito nas etapas abaixo. Você também pode examinar o código-fonte e implementar configurações semelhantes no seus aplicativos clientes.

**Etapa 1:** baixe o projeto no [Exemplo de teste de desempenho do Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark) ou bifurque o repositório GitHub.

**Etapa 2:** modifique as configurações de EndpointUrl, AuthorizationKey, CollectionThroughput e DocumentTemplate (opcional) em App.config.

> [!NOTE]
> Antes de provisionar as coleções com alta produtividade, confira a [Página de Preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para estimar os custos por coleção. O Cosmos DB cobra o armazenamento e a produtividade de forma independente e por hora. Portanto, você pode economizar excluindo ou reduzindo a produtividade de suas coleções do DocumentDB após o teste.
> 
> 

**Etapa 3:** compile e execute o aplicativo de console da linha de comando. Você verá algo semelhante ao mostrado a seguir:

    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://docdb-scale-demo.documents.azure.com:443/
    Collection : db.testdata at 50000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: 500
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Creating database db
    Creating collection testdata
    Creating metric collection metrics
    Retrying after sleeping for 00:03:34.1720000
    Starting Inserts with 500 tasks
    Inserted 661 docs @ 656 writes/s, 6860 RU/s (18B max monthly 1KB reads)
    Inserted 6505 docs @ 2668 writes/s, 27962 RU/s (72B max monthly 1KB reads)
    Inserted 11756 docs @ 3240 writes/s, 33957 RU/s (88B max monthly 1KB reads)
    Inserted 17076 docs @ 3590 writes/s, 37627 RU/s (98B max monthly 1KB reads)
    Inserted 22106 docs @ 3748 writes/s, 39281 RU/s (102B max monthly 1KB reads)
    Inserted 28430 docs @ 3902 writes/s, 40897 RU/s (106B max monthly 1KB reads)
    Inserted 33492 docs @ 3928 writes/s, 41168 RU/s (107B max monthly 1KB reads)
    Inserted 38392 docs @ 3963 writes/s, 41528 RU/s (108B max monthly 1KB reads)
    Inserted 43371 docs @ 4012 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 48477 docs @ 4035 writes/s, 42282 RU/s (110B max monthly 1KB reads)
    Inserted 53845 docs @ 4088 writes/s, 42845 RU/s (111B max monthly 1KB reads)
    Inserted 59267 docs @ 4138 writes/s, 43364 RU/s (112B max monthly 1KB reads)
    Inserted 64703 docs @ 4197 writes/s, 43981 RU/s (114B max monthly 1KB reads)
    Inserted 70428 docs @ 4216 writes/s, 44181 RU/s (115B max monthly 1KB reads)
    Inserted 75868 docs @ 4247 writes/s, 44505 RU/s (115B max monthly 1KB reads)
    Inserted 81571 docs @ 4280 writes/s, 44852 RU/s (116B max monthly 1KB reads)
    Inserted 86271 docs @ 4273 writes/s, 44783 RU/s (116B max monthly 1KB reads)
    Inserted 91993 docs @ 4299 writes/s, 45056 RU/s (117B max monthly 1KB reads)
    Inserted 97469 docs @ 4292 writes/s, 44984 RU/s (117B max monthly 1KB reads)
    Inserted 99736 docs @ 4192 writes/s, 43930 RU/s (114B max monthly 1KB reads)
    Inserted 99997 docs @ 4013 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 100000 docs @ 3846 writes/s, 40304 RU/s (104B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 100000 docs @ 3834 writes/s, 40180 RU/s (104B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.


**Etapa 4 (se necessário):** a produtividade relatada (RU/s) da ferramenta deverá ser igual ou maior que a produtividade provisionada da coleção. Caso contrário, o aumento do DegreeOfParallelism em pequenos incrementos pode ajudá-lo a atingir o limite. Se a taxa de transferência do seu aplicativo cliente estagnar, iniciar várias instâncias do aplicativo nas mesmas máquinas ou em máquinas diferentes ajudará você atingir o limite configurado em instâncias diferentes. Se precisar de ajuda com esta etapa, escreva um email para askcosmosdb@microsoft.com ou preencha um tíquete de suporte no [Portal do Azure](https://portal.azure.com).

Uma vez que o aplicativo estiver em execução, você poderá experimentar [Políticas de indexação](indexing-policies.md) e [Níveis de consistência](consistency-levels.md) diferentes para entender seu impacto na produtividade e na latência. Você também pode examinar o código-fonte e implementar configurações semelhantes no seus pacotes de teste ou aplicativos de produção.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, analisamos como você pode realizar testes de desempenho e escala com o Cosmos DB usando um aplicativo de console .NET. Consulte os links abaixo para obter mais informações sobre como trabalhar com o Azure Cosmos DB.

* [Amostra de teste de desempenho do Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Opções de configuração do cliente para melhorar o desempenho do Azure Cosmos DB](performance-tips.md)
* [Particionamento do lado do servidor no Azure Cosmos DB](partition-data.md)



