---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 06/20/2018
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 43408ebc65d4acf581b612e8ecfb9d00679cc078
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37066094"
---
O Data factory é um serviço multilocatário que tem os seguintes limites padrão em vigor para certificar-se de que as assinaturas de clientes são protegidas das cargas de trabalho umas das outras. Entrando em contato com o suporte, é possível aumentar muitos dos limites facilmente em sua assinatura até o limite máximo.

### <a name="version-2"></a>Versão 2

| Recurso | Limite padrão | Limite máximo |
| -------- | ------------- | ------------- |
| Data factories em uma assinatura do Azure | 50 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Número total de entidades (Pipeline, Conjuntos de Dados, Triggers, Serviços Vinculados, tempos de execução de integração) dentro de um data factory | 5.000 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Total de núcleos de CPU para Tempo(s) de Execução de Integração do Azure-SSIS em uma assinatura | 100 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Execuções de pipeline simultâneas por pipeline | 100 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Execuções de pipeline simultâneas por data factory | 10.000  | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Máximo de atividades por pipeline (inclui atividades internas para contêineres) | 40 | 40 |
| Máximo de parâmetros por pipeline | 50 | 50 |
| Itens ForEach | 100.000 | 100.000 |
| Para cada paralelismo | 20 | 50 |
| Caracteres por expressão | 8.192 | 8.192 |
| Intervalo de Disparo da Janela de Caída Mínima | 15 min | 15 min |
| Tempo limite máximo para atividades de pipeline | 7 dias | 7 dias |
| Bytes por objeto para objetos do pipeline <sup>1</sup> | 200 KB | 200 KB |
| Bytes por objeto para objetos de conjunto de dados e serviço vinculado <sup>1</sup> | 100 KB | 2000 KB |
| Unidades de integração de dados por atividade de cópia são executadas <sup>3</sup> | 256 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Gravar chamadas à API | 2500/hr<br/><br/> Esse limite é imposto pelo Azure Resource Manager, não pelo Azure Data Factory. | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Ler chamadas à API | 12.500/hr<br/><br/> Esse limite é imposto pelo Azure Resource Manager, não pelo Azure Data Factory. | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |


### <a name="version-1"></a>Versão 1

| **Recurso** | **Limite padrão** | **Limite máximo** |
| --- | --- | --- |
| Data factories em uma assinatura do Azure |50 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Pipelines em um data factory |2500 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Conjuntos de dados em um data factory |5.000 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Fatias simultâneas por conjunto de dados |10 |10 |
| Bytes por objeto para objetos do pipeline <sup>1</sup> |200 KB |200 KB |
| Bytes por objeto para objetos de conjunto de dados e serviço vinculado <sup>1</sup> |100 KB |2000 KB |
| núcleos de cluster sob demanda HDInsight em uma assinatura <sup>2</sup> |60 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| As unidades de movimentação de dados da nuvem por atividade de cópia são executadas <sup>3</sup> |32 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Contagem novas tentativas de execução de atividade do pipeline |1000 |MaxInt (32 bits) |

<sup>1</sup> Objetos de pipeline, conjunto de dados e serviço vinculado representam um agrupamento lógico de sua carga de trabalho. Os limites desses objetos não estão relacionados à quantidade de dados que você pode mover e processar com o serviço Azure Data Factory. O data factory foi desenvolvido para ser dimensionado de modo a lidar com petabytes de dados.

<sup>2</sup> Os núcleos sob demanda HDInsight são alocados da assinatura que contém o data factory. Como resultado, o limite acima é o limite de núcleo imposto pelo Data Factory para núcleos HDInsight sob demanda e é diferente do limite de núcleo associado à assinatura do Azure.

<sup>3</sup> Unidade de Integração de Dados (DIU) para v2 ou Unidade de Movimentação de Dados em Nuvem (DMU) para v1 está sendo usada em uma operação de cópia de nuvem para nuvem. É uma medida que representa a potência (uma combinação de CPU, memória e alocação de recursos da rede) de uma unidade única no Data Factory. Você pode obter uma taxa de transferência de cópia mais alta usando mais DMUs em alguns cenários. Consulte a seção [Unidades de integração de dados (V2)](../articles/data-factory/copy-activity-performance.md#data-integration-units) e [unidades de movimentação de dados da nuvem (V1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) em detalhes e [página de preços do Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/) para implicações de cobrança.

<sup>4</sup> O IR (tempo de execução de integração) é a infraestrutura de computação usada pelo Azure Data Factory para fornecer as seguintes funcionalidades de integração de dados entre diferentes ambientes de rede: movimentação de dados, atividades de expedição para serviços de computação, execução de pacotes de SSIS. Para obter mais informações, consulte [Visão geral do Integration Runtime](../articles/data-factory/concepts-integration-runtime.md).

| **Recurso** | **Limite inferior padrão** | **Limite mínimo** |
| --- | --- | --- |
| Intervalo de agendamento |15 minutos |15 minutos |
| Intervalo entre novas tentativas |1 segundo |1 segundo |
| Valor de tempo limite de nova tentativa |1 segundo |1 segundo |

#### <a name="web-service-call-limits"></a>Limites de chamada de serviço Web
O Azure Resource Manager tem limites para chamadas à API. Você pode fazer chamadas de API em um intervalo dentro dos [limites de API do Gerenciador de Recursos do Azure](../articles/azure-subscription-service-limits.md#resource-group-limits).
