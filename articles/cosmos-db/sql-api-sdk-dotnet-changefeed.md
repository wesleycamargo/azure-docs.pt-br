---
title: 'Azure Cosmos DB: Recursos, SDK e API do Processador do Feed de Alterações do .NET | Microsoft Docs'
description: Saiba tudo sobre o SDK e a API do processador de feed de alterações, incluindo as datas de lançamento, as datas de desativação e as alterações feitas entre cada versão do SDK do processador de feed de alterações do .NET.
services: cosmos-db
documentationcenter: .net
author: ealsur
manager: kfile
ms.assetid: f2dd9438-8879-4f74-bb6c-e1efc2cd0157
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/19/2018
ms.author: maquaran
ms.openlocfilehash: 7ed5772df4d8677fe878d7ced831dc15bbe8cac0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885129"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>SDK do processador do feed de alterações do .NET: download e notas de versão
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

|   |   |
|---|---|
|**Baixe o SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Documentação da API**|[Alterar a documentação de referência de API da biblioteca do Processador de Feed](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Introdução**|[Introdução ao SDK .NET do processador do feed de alterações](change-feed.md)|
|**Framework atualmente com suporte**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Notas de versão

### <a name="stable-builds"></a>Builds estáveis

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* Correções na estimativa de trabalho pendente.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* Aprimoramentos de estabilidade.
  * Correção para lidar com problemas de tarefas canceladas que podem levar a observadores parados em algumas partições.
* Suporte para o ponto de verificação manual.
* Compatível com o [SDK do .NET para SQL](sql-api-sdk-dotnet.md) versões 1.21 e superiores.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Adiciona suporte para o .NET Standard 2.0. O pacote agora dá suporte a monikers de estrutura `netstandard2.0` e `net451`.
* Compatível com o [SDK do .NET para SQL](sql-api-sdk-dotnet.md) versões 1.17.0 e superiores.
* Compatível com o [SDK do .NET Core para SQL](sql-api-sdk-dotnet-core.md) versões 1.5.1 e superiores.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Corrige um problema com o cálculo da estimativa de trabalho restante quando o feed de alterações estava vazio ou nenhum trabalho estava pendente.
* Compatível com o [SDK do .NET para SQL](sql-api-sdk-dotnet.md) versões 1.13.2 e superiores.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Adição de um método para obter uma estimativa do trabalho restante a ser processado no Feed de Alterações.
* Compatível com o [SDK do .NET para SQL](sql-api-sdk-dotnet.md) versões 1.13.2 e superiores.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK DO GA
* Compatível com o [SDK do .NET para SQL](sql-api-sdk-dotnet.md) versões 1.14.1 e inferiores.

### <a name="pre-release-builds"></a>Builds de pré-lançamento

### <a name="a-name202-prerelease202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2 - pré-lançamento
* Pequenas alterações na API:
  * Removido ChangeFeedProcessorOptions.IsAutoCheckpointEnabled que foi marcado como obsoleto.

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-pré-lançamento
* Aprimoramentos de estabilidade:
  * Melhor tratamento da inicialização do armazenamento de concessão. Quando o armazenamento de concessão estiver vazio, apenas uma instância do processador poderá inicializá-lo, os outros aguardarão.
  * Lançamento/renovação da concessão mais estável/eficiente. Renovar e liberar uma concessão de uma partição independe de outras renovações. Na v1 isso foi feito sequencialmente para todas as partições.
* Nova API v2:
  * Padrão de construtor para construção flexível do processador: a classe ChangeFeedProcessorBuilder.
    * Pode ser qualquer combinação de parâmetros.
    * Pode usar a instância de DocumentClient para monitoramento e/ou coleta de concessão (não disponível em v1).
  * IChangeFeedObserver.ProcessChangesAsync agora utiliza CancellationToken.
  * IRemainingWorkEstimator - o avaliador de trabalho restante pode ser usado separadamente do processador.
  * Novos pontos de extensibilidade:
    * IParitionLoadBalancingStrategy - para balanceamento de carga personalizado de partições entre instâncias do processador.
    * ILease, ILeaseManager - para gerenciamento de concessão personalizado.
    * IPartitionProcessor - para processamento personalizado de alterações em uma partição.
* Registro em log - usa a biblioteca [LibLog](https://github.com/damianh/LibLog).
* 100% compatível com versões anteriores com API v1.
* Nova base de código.
* Compatível com o [SQL .NET SDK](sql-api-sdk-dotnet.md) versões 1.21.1 e superiores.

## <a name="release--retirement-dates"></a>Datas de lançamento e desativação
A Microsoft fornecerá uma notificação pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente. 

Qualquer solicitação feita ao Cosmos DB com o uso de um SDK desativado será rejeitada pelo serviço.

<br/>

| Versão | Data do lançamento | Data de desativação |
| --- | --- | --- |
| [1.3.2](#1.3.2) |18 de abril de 2018 |--- |
| [1.3.1](#1.3.1) |13 de março de 2018 |--- |
| [1.2.0](#1.2.0) |31 de outubro de 2017 |--- |
| [1.1.1](#1.1.1) |29 de agosto de 2017 |--- |
| [1.1.0](#1.1.0) |13 de agosto de 2017 |--- |
| [1.0.0](#1.0.0) |07 de julho de 2017 |--- |


## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

