---
title: Perguntas frequentes relacionados à utilização de API | Microsoft Docs
description: Lista de metros de pilha do Azure, em comparação com a API de uso do Azure, o tempo de uso e a hora de relatado, códigos de erro.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: ac22ba34bff1d5321c05bc0a0a1b14ca742079a7
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051485"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Perguntas frequentes no uso da pilha do Azure API

Este artigo responde algumas perguntas frequentes sobre a API de uso de pilha do Azure.

## <a name="what-meter-ids-can-i-see"></a>O medidor IDs pode ver?
Uso é relatado para os provedores de recursos a seguir:

**Rede**  
  
**Monitorar o ID**: F271A8A388C44D93956A063E1D2FA80B  
**Nome do medidor**: uso de endereço IP estático  
**Unidade**: endereços IP  
**Notas**: contagem de endereços IP usados. Se você chamar o API de uso com uma granularidade diária, o medidor retorna o endereço IP multiplicado pelo número de horas.  
  
**Monitorar o ID**: 9E2739BA86744796B465F64674B822BA  
**Nome do medidor**: uso de endereço IP dinâmico  
**Unidade**: endereços IP  
**Notas**: contagem de endereços IP usados. Se você chamar o API de uso com uma granularidade diária, o medidor retorna o endereço IP multiplicado pelo número de horas.  
  
**Armazenamento**  
  
**Monitorar o ID**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Nome do medidor**: TableCapacity  
**Unidade**: GB\*horas  
**Notas**: capacidade consumida por tabelas Total.  
  
**Monitorar o ID**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Nome do medidor**: PageBlobCapacity  
**Unidade**: GB\*horas  
**Notas**: Total de capacidade consumida por blobs de página.  
  
**Monitorar o ID**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Nome do medidor**: QueueCapacity  
**Unidade**: GB\*horas  
**Notas**: Total de capacidade consumida pela fila.  
  
**Monitorar o ID**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Nome do medidor**: BlockBlobCapacity  
**Unidade**: GB\*horas  
**Notas**: Total de capacidade consumida por blobs de bloco.  
  
**Monitorar o ID**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Nome do medidor**: TableTransactions  
**Unidade**: Solicitar contagem em 10, milhares  
**Notas**: solicitações de serviço de tabela (por 10.000).  
  
**Monitorar o ID**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Nome do medidor**: TableDataTransIn  
**Unidade**: dados de entrada em GB  
**Notas**: tabela de entrada de dados de serviço em GB.  
  
**Monitorar o ID**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Nome do medidor**: TableDataTransOut  
**Unidade**: saída GB  
**Notas**: tabela de saída de dados de serviço em GB  
  
**Monitorar o ID**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Nome do medidor**: BlobTransactions  
**Unidade**: contagem de solicitações em 10, milhares  
**Notas**: solicitações de serviço de Blob (por 10.000).  
  
**Monitorar o ID**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Nome do medidor**: BlobDataTransIn  
**Unidade**: dados de entrada em GB  
**Notas**: Blob de entrada de dados de serviço em GB.  
  
**Monitorar o ID**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Nome do medidor**: BlobDataTransOut  
**Unidade**: saída GB  
**Notas**: Blob de saída de dados de serviço em GB.  
  
**Monitorar o ID**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Nome do medidor**: QueueTransactions  
**Unidade**: contagem de solicitações em 10, milhares  
**Notas**: fila de solicitações de serviço (por 10.000).  
  
**Monitorar o ID**: E518E809-E369-4A45-9274-2017B29FFF25  
**Nome do medidor**: QueueDataTransIn  
**Unidade**: dados de entrada em GB  
**Notas**: fila de entrada de dados de serviço em GB.  
  
**Monitorar o ID**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Nome do medidor**: QueueDataTransOut  
**Unidade**: saída GB  
**Notas**: fila de saída de dados de serviço em GB  
  
**RP SQL**  
  
**Monitorar o ID**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Nome do medidor**: DatabaseSizeHourSqlMeter  
**Unidade**: MB\*horas  
**Notas**: capacidade de banco de dados Total no momento da criação. Se você chamar o API de uso com uma granularidade diária, o medidor retorna MB multiplicado pelo número de horas.  
  
**MySql RP**  
  
**Monitorar o ID**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Nome do medidor**: DatabaseSizeHourMySqlMeter  
**Unidade**: MB\*horas  
**Notas**: capacidade de banco de dados Total no momento da criação. Se você chamar o API de uso com uma granularidade diária, o medidor retorna MB multiplicado pelo número de horas.  
  
**Computação**  
  
**Monitorar o ID**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Nome do medidor**: horas de tamanho de VM de Base  
**Unidade**: horas de núcleo Virtual  
**Notas**: número de núcleos virtuais multiplicado por horas em que a VM foi executada.  
  
**Monitorar o ID**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Nome do medidor**: horas de tamanho de VM do Windows  
**Unidade**: horas de núcleo Virtual  
**Notas**: número de núcleos virtuais multiplicado por horas a VM foi executada.  
  
**Monitorar o ID**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Nome do medidor**: horas de tamanho VM  
**Unidade**: horas VM  
**Notas**: captura Base e a VM do Windows. Não ajustar de núcleos.  
  
**Key Vault**  
  
**Monitorar o ID**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Nome do medidor**: transações de Cofre de chaves  
**Unidade**: Solicitar contagem em 10, milhares  
**Notas**: solicitações de número da API REST recebidas pelo plano de dados do Cofre de chaves.  
  
**Monitorar o ID**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Nome do medidor**: Advanced transações de chaves  
**Unidade**: 10 mil transações  
**Notas**: RSA 3/4 KB, transações de chave de ECC. (visualização).  
  
*Serviço de aplicativo**  
  
**Monitorar o ID**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Nome do medidor**: serviço de aplicativo  
**Unidade**: horas de núcleo Virtual  
**Notas**: número de núcleos virtuais usadas para executar o serviço de aplicativo. Observação: A Microsoft usa esse medidor para cobrar o serviço de aplicativo na pilha do Azure. Provedores de serviços de nuvem pode usar o serviço do aplicativo metros (abaixo) para calcular o uso de seus locatários.  
  
**Monitorar o ID**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Nome do medidor**: solicitações de funções  
**Unidade**: 10 solicitações  
**Notas**: Número Total de execuções solicitados (por 10 execuções). Execuções são contadas sempre que uma função é executado em resposta a um evento, ou é disparada por uma associação.  
  
**Monitorar o ID**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Nome do medidor**: funções - de computação  
**Unidade**: s GB  
**Notas**: consumo de recursos é medido em segundos de gigabyte (GB/s). **Observado o consumo de recursos** é calculada multiplicando o tamanho médio de memória em GB, o tempo em milissegundos que leva para executar a função. Memória usada por uma função é medida, arredondando até mais próximo 128 MB, até o tamanho máximo de memória de 1,536 MB, com tempo de execução calculado pelo arredondamento até o mais próximo 1 ms. O tempo de execução mínimo e a memória para a execução de uma única função é 100 ms e 128 mb respectivamente.  
  
**Monitorar o ID**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Nome do medidor**: horas de serviço de aplicativo compartilhado  
**Unidade**: 1 hora  
**Notas**: por uso de hora de fragmento de plano do serviço de aplicativo. Planos são limitados em uma base por aplicativo.  
  
**Monitorar o ID**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Nome do medidor**: horas gratuitas do serviço de aplicativo  
**Unidade**: 1 hora  
**Notas**: por uso de hora do plano de serviço de aplicativo gratuito. Planos são limitados em uma base por aplicativo.  
  
**Monitorar o ID**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Nome do medidor**: pequeno horas de serviço de aplicativo Standard  
**Unidade**: 1 hora  
**Notas**: calculado com base no tamanho e número de instâncias.  
  
**Monitorar o ID**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Nome do medidor**: médio horas de serviço de aplicativo Standard  
**Unidade**: 1 hora  
**Notas**: calculado com base no tamanho e número de instâncias.  
  
**Monitorar o ID**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Nome do medidor**: grande horas de serviço de aplicativo Standard  
**Unidade**: 1 hora  
**Notas**: calculado com base no tamanho e número de instâncias.  
  
**Camadas de trabalhador personalizado**  
  
**ID de medidor**: *camadas de trabalhador personalizado*  
**Nome do medidor**: camadas de trabalhador personalizado  
**Unidade**: horas  
**Notas**: ID de medidor determinística é criado com base no SKU e o nome da camada de trabalho personalizado. Essa ID de medidor é exclusivo para cada camada de trabalho personalizado.  
  
**Monitorar o ID**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Nome do medidor**: SSL SNI  
**Unidade**: por associação SSL de SNI  
**Notas**: serviço de aplicativo oferece suporte a dois tipos de conexões SSL: conexões SSL de indicação de nome de servidor (SNI) e conexões de SSL de endereço IP. O SSL baseado em SNI funciona em navegadores modernos, enquanto o SSL baseado em IP funciona em todos os navegadores.  
  
**Monitorar o ID**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Nome do medidor**: SSL de IP  
**Unidade**: por IP com base em associação de SSL  
**Notas**: serviço de aplicativo oferece suporte a dois tipos de conexões SSL: conexões SSL de indicação de nome de servidor (SNI) e conexões de SSL de endereço IP. O SSL baseado em SNI funciona em navegadores modernos, enquanto o SSL baseado em IP funciona em todos os navegadores.  
  
**Monitorar o ID**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Nome do medidor**: o processo da Web  
**Unidade**:  
**Notas**: calculada por site ativo por hora.  
  
**Monitorar o ID**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Nome do medidor**: largura de banda de saída externa  
**Unidade**: GB  
**Notas**: solicitação de entrada Total bytes de resposta + solicitação de saída total bytes + FTP de entrada total solicitar bytes de resposta + web de entrada total implanta bytes de resposta de solicitação.  
  

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Como fazer o uso da pilha do Azure APIs comparam com o [uso do Azure API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (atualmente em visualização pública)?
* A API de uso do locatário é consistente com a API do Azure, com uma exceção: o *showDetails* sinalizador atualmente não tem suporte na pilha do Azure.
* A API de uso do provedor só se aplica a pilha do Azure.
* Atualmente, o [RateCard API](https://msdn.microsoft.com/library/azure/mt219004.aspx) que é disponível no Azure não está disponível na pilha do Azure.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Qual é a diferença entre o tempo de uso e o tempo relatado?
Relatórios de uso de dados têm dois valores de tempo principal:

* **Tempo informado**. A hora quando o evento de uso entrou no sistema de uso
* **Tempo de uso**. A hora em que o recurso de pilha do Azure foi consumido

Talvez você veja uma discrepância nos valores de tempo de uso e a hora relatados para um evento de uso específicos. O atraso pode ser várias horas em qualquer ambiente.

No momento, você pode consultar somente pelo *tempo relatados*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>O que significam esses códigos de erro da API de uso?
| **Código de status HTTP** | **Código de erro** | **Descrição** |
| --- | --- | --- |
| Solicitação incorreta/400 |*NoApiVersion* |O *api-version* parâmetro de consulta está ausente. |
| Solicitação incorreta/400 |*InvalidProperty* |Uma propriedade está ausente ou tem um valor inválido. A mensagem no código de erro no corpo da resposta identifica a propriedade ausente. |
| Solicitação incorreta/400 |*RequestEndTimeIsInFuture* |O valor de *ReportedEndTime* está no futuro. Os valores no futuro não são permitidos para esse argumento. |
| Solicitação incorreta/400 |*SubscriberIdIsNotDirectTenant* |Uma chamada de API do provedor tem usado uma ID de assinatura não é um locatário válido do chamador. |
| Solicitação incorreta/400 |*SubscriptionIdMissingInRequest* |A ID da assinatura do chamador está ausente. |
| Solicitação incorreta/400 |*InvalidAggregationGranularity* |Foi solicitada uma granularidade de agregação inválida. Os valores válidos são diariamente e por hora. |
| 503 |*ServiceUnavailable* |Um erro reproduzível ocorreu porque o serviço está ocupado ou a chamada está sendo limitada. |

## <a name="next-steps"></a>Próximas etapas
[Cliente de cobrança e estorno na pilha do Azure](azure-stack-billing-and-chargeback.md)

[Uso do provedor de recursos API](azure-stack-provider-resource-api.md)

[API de uso de recursos de locatário](azure-stack-tenant-resource-usage-api.md)
