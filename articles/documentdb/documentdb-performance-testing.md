---
title: Testes de desempenho e escalabilidade do DocumentDB | Microsoft Docs
description: Aprenda a executar testes de desempenho e escalabilidade com o Banco de Dados de Documentos do Azure
keywords: testes de desempenho
services: documentdb
author: arramac
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f4c96ebd-f53c-427d-a500-3f28fe7b11d0
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 86bd591c26a58200dab9872e07e6e8bdf2522df9
ms.lasthandoff: 03/29/2017


---
# <a name="performance-and-scale-testing-with-azure-documentdb"></a>Teste de desempenho e escalabilidade com o Banco de Dados de Documentos do Azure
O teste de desempenho e escalabilidade é uma etapa importante no desenvolvimento de aplicativos. Para muitos aplicativos, a camada de banco de dados tem um impacto significativo sobre o desempenho e a escalabilidade geral e, portanto, é um componente essencial do teste de desempenho. [Banco de Dados de Documentos](https://azure.microsoft.com/services/documentdb/) foi desenvolvido para fins de escala elástica e desempenho previsível e, portanto, é uma excelente opção para aplicativos que precisam de uma camada de banco de dados de alto desempenho. 

Este artigo é uma referência para desenvolvedores implementarem conjuntos de testes de desempenho para suas cargas de trabalho de Banco de Dados de Documentos ou avaliarem Banco de Dados de Documentos para cenários de aplicativos de alto desempenho. Ele se concentra principalmente nos testes de desempenho de banco de dados isolados, mas também inclui práticas recomendadas para aplicativos de produção.

Depois de ler este artigo, você poderá responder as seguintes perguntas:   

* Onde posso encontrar um aplicativo cliente do .NET de exemplo para testar o desempenho do Banco de Dados de Documentos do Azure? 
* Como posso obter altos níveis de taxa de transferência com o Banco de Dados de Documentos do Azure do meu aplicativo cliente?

Para começar com o código, baixe o projeto do [Exemplo de teste de desempenho do DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> A meta desse aplicativo é demonstrar práticas recomendadas para extrair melhor desempenho do Banco de Dados de Documentos com um número menor de computadores cliente. Ele não foi feito para demonstrar a capacidade máxima do serviço, que pode ser dimensionada sem limites.
> 
> 

Se você estiver procurando opções de configuração do cliente para melhorar o desempenho de Banco de Dados de Documentos, consulte [Dicas de desempenho de Banco de Dados de Documentos](documentdb-performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Execute o aplicativo de teste de desempenho
A maneira mais rápida de começar é compilar e executar o exemplo do .NET abaixo, conforme descrito nas etapas abaixo. Você também pode examinar o código-fonte e implementar configurações semelhantes no seus aplicativos clientes.

**Etapa 1:** baixe o projeto do [Exemplo de Teste de Desempenho do DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)ou divida o repositório GitHub.

**Etapa 2:** modifique as configurações de EndpointUrl, AuthorizationKey, CollectionThroughput e DocumentTemplate (opcional) em App.config.

> [!NOTE]
> Antes de provisionar as coleções com alta produtividade, confira a [Página de Preços](https://azure.microsoft.com/pricing/details/documentdb/) para estimar os custos por coleção. O Banco de Dados de Documentos cobra o armazenamento e a taxa de transferência de forma independente e por hora. Portanto, você pode salvar os custos excluindo ou reduzindo a produtividade de suas coleções do Banco de Dados de Documentos depois do teste.
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


**Etapa 4 (se necessário):** a produtividade relatada (RU/s) da ferramenta deverá ser igual ou maior que a produtividade provisionada da coleção. Caso contrário, o aumento do DegreeOfParallelism em pequenos incrementos pode ajudá-lo a atingir o limite. Se a taxa de transferência do seu aplicativo cliente estagnar, iniciar várias instâncias do aplicativo nas mesmas máquinas ou em máquinas diferentes ajudará você atingir o limite configurado em instâncias diferentes. Se precisar de ajuda com esta etapa, escreva um email para askdocdb@microsoft.com ou preencha um tíquete de suporte no [Portal do Azure](https://portal.azure.com).

Uma vez que o aplicativo estiver em execução, você poderá experimentar [Políticas de indexação](documentdb-indexing-policies.md) e [Níveis de consistência](documentdb-consistency-levels.md) diferentes para entender seu impacto na produtividade e na latência. Você também pode examinar o código-fonte e implementar configurações semelhantes no seus pacotes de teste ou aplicativos de produção.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, analisamos como você pode executar testes de documentos e dimensionamento com o Banco de Dados de Documentos usando um aplicativo de console .NET. Consulte os links abaixo para saber mais sobre como trabalhar com o Banco de Dados de Documentos.

* [Exemplo de teste de desempenho do Banco de Dados de Documentos](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Opções de configuração do cliente para melhorar o desempenho do Banco de Dados de Documentos](documentdb-performance-tips.md)
* [Particionamento do lado servidor no Banco de Dados de Documentos](documentdb-partition-data.md)
* [Coleções e níveis de desempenho do Banco de Dados de Documentos](documentdb-performance-levels.md)
* [Documentação do SDK .NET do Banco de Dados de Documentos no MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Amostras do .NET do Banco de Dados de Documentos](https://github.com/Azure/azure-documentdb-net)
* [Blog do Banco de Dados de Documentos sobre dicas de desempenho](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)


