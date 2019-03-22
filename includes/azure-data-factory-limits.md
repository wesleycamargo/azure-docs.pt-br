---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 1/10/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 42c1856f30484532e1ace2e84187bcaaacdf4c72
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553215"
---
O Azure Data Factory é um serviço multilocatário que tem os seguintes limites padrão em vigor para certificar-se de assinaturas de clientes são protegidas contra umas das outras cargas de trabalho. Para aumentar os limites até o máximo de sua assinatura, contate o suporte.

### <a name="version-2"></a>Versão 2

| Recurso | Limite padrão | Limite máximo |
| -------- | ------------- | ------------- |
| Data factories em uma assinatura do Azure | 50 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Número total de entidades, como pipelines, conjuntos de dados, gatilhos, serviços vinculados e tempos de execução de integração, em um data factory | 5.000 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Total de núcleos de CPU para tempos de execução de integração do Azure-SSIS em uma assinatura | 256 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Execuções de pipeline simultâneas por data factory que é compartilhado entre todos os pipelines na fábrica | 10.000  | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Máximo atividades por pipeline, que inclui atividades internas para contêineres | 40 | 40 |
| Número máximo de tempos de execução de integração vinculada que podem ser criados em um único integration runtime auto-hospedado | 20 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Máximo de parâmetros por pipeline | 50 | 50 |
| Itens ForEach | 100.000 | 100.000 |
| Para cada paralelismo | 20 | 50 |
| Caracteres por expressão | 8.192 | 8.192 |
| Intervalo do gatilho de janela em cascata mínimo | 15 min | 15 min |
| Tempo limite máximo para a atividade de pipeline é executado | 7 dias | 7 dias |
| Bytes por objeto para objetos do pipeline<sup>1</sup> | 200 KB | 200 KB |
| Bytes por objeto de conjunto de dados e objetos de serviço vinculado<sup>1</sup> | 100 KB | 2.000 KB |
| Unidades de integração de dados por execução de atividade de cópia<sup>3</sup> | 256 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gravar chamadas à API | 2.500/h<br/><br/> Esse limite é imposto pelo Azure Resource Manager, não pelo Azure Data Factory. | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Ler chamadas à API | 12,500/h<br/><br/> Esse limite é imposto pelo Azure Resource Manager, não pelo Azure Data Factory. | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Consultas de monitoramento por minuto | 1.000 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operações CRUD de entidade por minuto | 50 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |


### <a name="version-1"></a>Versão 1

| **Recurso** | **Limite padrão** | **Limite máximo** |
| --- | --- | --- |
| Data factories em uma assinatura do Azure |50 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Pipelines em um data factory |2.500 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Conjuntos de dados em um data factory |5.000 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Fatias simultâneas por conjunto de dados |10 |10 |
| Bytes por objeto para objetos do pipeline<sup>1</sup> |200 KB |200 KB |
| Bytes por objeto para dados de definir e objetos de serviço vinculados<sup>1</sup> |100 KB |2.000 KB |
| Núcleos de cluster sob demanda do HDInsight do Azure dentro de uma assinatura<sup>2</sup> |60 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unidades de movimentação de dados por execução de atividade de cópia de nuvem<sup>3</sup> |32 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Contagem novas tentativas de execução de atividade do pipeline |1.000 |MaxInt (32 bits) |

<sup>1</sup>pipeline, conjunto de dados e objetos de serviço vinculado representam um agrupamento lógico de sua carga de trabalho. Os limites desses objetos não estão relacionadas à quantidade de dados que você pode mover e processar com o Azure Data Factory. Data Factory é projetado para dimensionar para lidar com petabytes de dados.

<sup>2</sup>Os núcleos sob demanda HDInsight são alocados da assinatura que contém o data factory. Como resultado, o limite anterior é o limite de núcleo imposto pelo Data Factory para núcleos do HDInsight sob demanda. Ele é diferente do limite de núcleo que está associada com sua assinatura do Azure.

<sup>3</sup>a unidade de integração de dados (DIU) para a versão 2 ou a unidade de movimentação de dados de nuvem (DMU) para a versão 1 é usada em uma operação de cópia de nuvem para a nuvem. É uma medida que representa a potência de uma unidade única no Data Factory. Ele combina a CPU, memória e as alocações de recursos de rede. Você pode obter uma taxa de transferência de cópia mais alta usando mais DMUs em alguns cenários. Para obter mais informações, consulte [unidades de dados de integração (versão 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units) e [unidades de movimentação de dados (versão 1) na nuvem](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Para obter informações sobre a cobrança, consulte [preços do Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>4</sup>o integration runtime (IR) é a infraestrutura de computação usada pelo Azure Data Factory para fornecer funcionalidades de integração de dados entre ambientes de rede diferente, como a movimentação de dados, expedir atividades para serviços de computação, e execução de pacotes do SSIS. Para obter mais informações, consulte [visão geral de tempo de execução de integração](../articles/data-factory/concepts-integration-runtime.md).

| **Recurso** | **Limite inferior padrão** | **Limite mínimo** |
| --- | --- | --- |
| Intervalo de agendamento |15 minutos |15 minutos |
| Intervalo entre novas tentativas |1 segundo |1 segundo |
| Valor de tempo limite de nova tentativa |1 segundo |1 segundo |

#### <a name="web-service-call-limits"></a>Limites de chamada de serviço Web
O Azure Resource Manager tem limites para chamadas à API. Você pode fazer chamadas de API em um intervalo dentro dos [limites de API do Gerenciador de Recursos do Azure](../articles/azure-subscription-service-limits.md#resource-group-limits).
