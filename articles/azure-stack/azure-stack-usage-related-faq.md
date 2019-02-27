---
title: Perguntas frequentes relacionadas ao uso de API | Microsoft Docs
description: Lista de medidores do Azure Stack, em comparação com a API de uso do Azure, o tempo de uso e o tempo relatado, códigos de erro.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.lastreviewed: 02/26/2019
ms.openlocfilehash: 00fb888f201f4bb8d211050b288516021d837dc5
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888756"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Perguntas frequentes na API de uso do Azure Stack

Este artigo responde algumas perguntas frequentes sobre a API de uso do Azure Stack.

## <a name="what-meter-ids-can-i-see"></a>Quais as IDs do medidor pode ver?
O uso é relatado para os provedores de recursos a seguir:

### <a name="network"></a>Rede
  
**ID do medidor**: F271A8A388C44D93956A063E1D2FA80B  
**Nome do medidor**: Uso de endereço IP estático  
**Unidade**: Endereços IP  
**Observações**: Contagem de endereços IP usados. Se você chamar a API de uso com uma granularidade diária, o medidor retorna o endereço IP multiplicado pelo número de horas.  
  
**ID do medidor**: 9E2739BA86744796B465F64674B822BA  
**Nome do medidor**: Uso de endereço IP dinâmico  
**Unidade**: Endereços IP  
**Observações**: Contagem de endereços IP usados. Se você chamar a API de uso com uma granularidade diária, o medidor retorna o endereço IP multiplicado pelo número de horas.  
  
### <a name="storage"></a>Armazenamento
  
**ID do medidor**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Nome do medidor**: TableCapacity  
**Unidade**: GB\*horas  
**Observações**: Capacidade total consumida por tabelas.  
  
**ID do medidor**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Nome do medidor**: PageBlobCapacity  
**Unidade**: GB\*horas  
**Observações**: Capacidade total consumida por blobs de página.  
  
**ID do medidor**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Nome do medidor**: QueueCapacity  
**Unidade**: GB\*horas  
**Observações**: Capacidade total consumida por fila.  
  
**ID do medidor**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Nome do medidor**: BlockBlobCapacity  
**Unidade**: GB\*horas  
**Observações**: Capacidade total consumida por blobs de blocos.  
  
**ID do medidor**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Nome do medidor**: TableTransactions  
**Unidade**: Contagem de solicitações em 10, milhares  
**Observações**: Solicitações de serviço de tabela (em 10.000s).  
  
**ID do medidor**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Nome do medidor**: TableDataTransIn  
**Unidade**: Dados de entrada em GB  
**Observações**: Entrada de dados de serviço de tabela em GB.  
  
**ID do medidor**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Nome do medidor**: TableDataTransOut  
**Unidade**: Egresso em GB  
**Observações**: Saída de dados de serviço de tabela em GB  
  
**ID do medidor**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Nome do medidor**: BlobTransactions  
**Unidade**: Contagem de solicitações em 10, milhares  
**Observações**: Solicitações de serviço de blob (em 10.000s).  
  
**ID do medidor**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Nome do medidor**: BlobDataTransIn  
**Unidade**: Dados de entrada em GB  
**Observações**: Entrada de dados de serviço de blob em GB.  
  
**ID do medidor**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Nome do medidor**: BlobDataTransOut  
**Unidade**: Egresso em GB  
**Observações**: Saída de dados de serviço de blob em GB.  
  
**ID do medidor**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Nome do medidor**: QueueTransactions  
**Unidade**: Contagem de solicitações em 10, milhares  
**Observações**: Solicitações de serviço de fila (em 10.000s).  
  
**ID do medidor**: E518E809-E369-4A45-9274-2017B29FFF25  
**Nome do medidor**: QueueDataTransIn  
**Unidade**: Dados de entrada em GB  
**Observações**: Entrada de dados de serviço de fila em GB.  
  
**ID do medidor**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Nome do medidor**: QueueDataTransOut  
**Unidade**: Egresso em GB  
**Observações**: Saída de dados de serviço de fila em GB  

### <a name="compute"></a>Computação 
  
**ID do medidor**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Nome do medidor**: Horas de tamanho VM de base  
**Unidade**: Horas de núcleo virtual  
**Observações**: Número de núcleos virtuais multiplicado por horas em que a VM foi executada.  
  
**ID do medidor**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Nome do medidor**: Horas de tamanho VM do Windows  
**Unidade**: Horas de núcleo virtual  
**Observações**: Número de núcleos virtuais multiplicado por horas que a VM foi executada.  
  
**ID do medidor**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Nome do medidor**: Horas de tamanho VM  
**Unidade**: Horas de VM  
**Observações**: Captura a VM de Base e o Windows. Não se ajustará para os núcleos.  
  
### <a name="managed-disks"></a>Managed Disks

**ID do medidor**: 380874f9-300c-48e0-95a0-d2d9a21ade8f   
**Nome do medidor**: S4   
**Unidade**: Contagem de discos\*mês   
**Observações**: Standard Managed Disk – 32 GB 

**ID do medidor**: 1b77d90f-427b-4435-b4f1-d78adec53222   
**Nome do medidor**: S6   
**Unidade**: Contagem de discos\*mês   
**Observações**: Standard Managed Disk – 64 GB 

**ID do medidor**: d5f7731b-f639-404a-89d0-e46186e22c8d   
**Nome do medidor**: S10   
**Unidade**: Contagem de discos\*mês   
**Observações**: Disco – 128 GB gerenciado padrão 

**ID do medidor**: ff85ef31-da5b-4eac-95dd-a69d6f97b18a   
**Nome do medidor**: S15   
**Unidade**: Contagem de discos\*mês   
**Observações**: Standard Managed Disk – 256 GB 

**ID do medidor**: 88ea9228-457a-4091-adc9-ad5194f30b6e   
**Nome do medidor**: S20   
**Unidade**: Contagem de discos\*mês      
**Observações**: Standard Managed Disk – 512 GB 

**ID do medidor**: 5b1db88a-8596-4002-8052-347947c26940   
**Nome do medidor**: S30   
**Unidade**: Contagem de discos\*mês   
**Observações**: Standard Managed Disk – 1024 GB 

**ID do medidor**: 7660b45b-b29d-49cb-b816-59f30fbab011   
**Nome do medidor**: P4   
**Unidade**: Contagem de discos\*mês   
**Observações**: Premium Managed Disk – 32 GB 

**ID do medidor**: 817007fd-a077-477f-bc01-b876f27205fd   
**Nome do medidor**: P6   
**Unidade**: Contagem de discos\*mês   
**Observações**: Disco – 64 GB gerenciado Premium 

**ID do medidor**: e554b6bc-96cd-4938-a5b5-0da990278519   
**Nome do medidor**: P10   
**Unidade**: Contagem de discos\*mês   
**Observações**: Disco – 128 GB gerenciado Premium  

**ID do medidor**: cdc0f53a-62a9-4472-a06c-e99a23b02907   
**Nome do medidor**: P15  
**Unidade**: Contagem de discos\*mês   
**Observações**: Premium Managed Disk – 256 GB 

**ID do medidor**: b9cb2d1a-84c2-4275-aa8b-70d2145d59aa   
**Nome do medidor**: P20   
**Unidade**: Contagem de discos\*mês   
**Observações**: Premium Managed Disk – 512 GB 

**ID do medidor**: 06bde724-9f94-43c0-84c3-d0fc54538369   
**Nome do medidor**: P30   
**Unidade**: Contagem de discos\*mês   
**Observações**: Premium Managed Disk – 1024 GB 

**ID do medidor**: 7ba084ec-ef9c-4d64-a179-7732c6cb5e28   
**Nome do medidor**: ActualStandardDiskSize   
**Unidade**: GB\*mês      
**Observações**: O tamanho real no disco do disco gerenciado standard  

**ID do medidor**: daef389a-06e5-4684-a7f7-8813d9f792d5  
**Nome do medidor**: ActualPremiumDiskSize   
**Unidade**: GB\*mês      
**Observações**: O tamanho real em disco Premium um disco gerenciado 

**ID do medidor**: 75d4b707-1027-4403-9986-6ec7c05579c8  
**Nome do medidor**: ActualStandardSnapshotSize   
**Unidade**: GB\*mês   
**Observações**: O tamanho real no disco do instantâneo padrão gerenciado.  

**ID do medidor**: 5ca1cbb9-6f14-4e76-8be8-1ca91547965e   
**Nome do medidor**: ActualPremiumSnapshotSize   
**Unidade**: GB\*mês   
**Observações**: O tamanho real em disco gerenciado Premium.   

**ID do medidor**: 5d76e09f-4567-452a-94cc-7d1f097761f0   
**Nome do medidor**: S4   
**Unidade**: Contagem de discos\*horas   
**Observações**: Standard Managed Disk – 32 GB (preterido) 

**ID do medidor**: dc9fc6a9-0782-432a-b8dc-978130457494   
**Nome do medidor**: S6   
**Unidade**: Contagem de discos\*horas   
**Observações**: Disco – 64 GB (preterido) gerenciado padrão 

**ID do medidor**: e5572fce-9f58-49d7-840c-b168c0f01fff   
**Nome do medidor**: S10   
**Unidade**: Contagem de discos\*horas   
**Observações**: Standard Managed Disk – 128 GB (preterido) 

**ID do medidor**: 9a8caedd-1195-4cd5-80b4-a4c22f9302b8   
**Nome do medidor**: S15   
**Unidade**: Contagem de discos\*horas   
**Observações**: Disco – 256 GB (preterido) gerenciado padrão 

**ID do medidor**: 5938f8da-0ecd-4c48-8d5a-c7c6c23546be   
**Nome do medidor**: S20   
**Unidade**: Contagem de discos\*horas      
**Observações**: Standard Managed Disk – 512 GB (preterido) 

**ID do medidor**: 7705a158-bd8b-4b2b-b4c2-0782343b81e6   
**Nome do medidor**: S30   
**Unidade**: Contagem de discos\*horas   
**Observações**: Standard Managed Disk – 1024 GB (preterido) 

**ID do medidor**: 5c105f5f-cbdf-435c-b49b-3c7174856dcc   
**Nome do medidor**: P4   
**Unidade**: Contagem de discos\*horas   
**Observações**: Premium Managed Disk – 32 GB (preterido) 

**ID do medidor**: 518b412b-1927-4f25-985f-4aea24e55c4f   
**Nome do medidor**: P6   
**Unidade**: Contagem de discos\*horas   
**Observações**: Premium Managed Disk – 64 GB (preterido) 

**ID do medidor**: 5cfb1fed-0902-49e3-8217-9add946fd624   
**Nome do medidor**: P10   
**Unidade**: Contagem de discos\*horas   
**Observações**: Premium Managed Disk – 128 GB (preterido)  

**ID do medidor**: 8de91c94-f740-4d9a-b665-bd5974fa08d4   
**Nome do medidor**: P15  
**Unidade**: Contagem de discos\*horas   
**Observações**: Premium Managed Disk – 256 GB (preterido) 

**ID do medidor**: c7e7839c-293b-4761-ae4c-848eda91130b   
**Nome do medidor**: P20   
**Unidade**: Contagem de discos\*horas   
**Observações**: Premium Managed Disk – 512 GB (preterido) 

**ID do medidor**: 9f502103-adf4-4488-b494-456c95d23a9f   
**Nome do medidor**: P30   
**Unidade**: Contagem de discos\*horas   
**Observações**: Premium Managed Disk – 1024 GB (preterido) 

**ID do medidor**: 8a409390-1913-40ae-917b-08d0f16f3c38   
**Nome do medidor**: ActualStandardDiskSize   
**Unidade**: Bytes\*horas      
**Observações**: O tamanho real no disco do disco gerenciado standard (preterido)  

**ID do medidor**: 1273b16f-8458-4c34-8ce2-a515de551ef6  
**Nome do medidor**: ActualPremiumDiskSize   
**Unidade**: Bytes\*horas      
**Observações**: O tamanho real em disco do premium managed disk (preterido) 

**ID do medidor**: 89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**Nome do medidor**: ActualStandardSnapshotSize   
**Unidade**: Bytes\*horas   
**Observações**: O tamanho real no disco do instantâneo padrão gerenciado (preterido) 

**ID do medidor**: 95b0c03f-8a82-4524-8961-ccfbf575f536   
**Nome do medidor**: ActualPremiumSnapshotSize   
**Unidade**: Bytes\*horas   
**Observações**: O tamanho real em disco gerenciado Premium (preterido) 

### <a name="sql-rp"></a>Sql RP
  
**ID do medidor**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Nome do medidor**: DatabaseSizeHourSqlMeter  
**Unidade**: MB\*horas  
**Observações**: Capacidade total do banco de dados no momento da criação. Se você chamar a API de uso com uma granularidade diária, o medidor retorna MB multiplicado pelo número de horas.  
  
### <a name="mysql-rp"></a>MySql RP   
  
**ID do medidor**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Nome do medidor**: DatabaseSizeHourMySqlMeter  
**Unidade**: MB\*horas  
**Observações**: Capacidade total do banco de dados no momento da criação. Se você chamar a API de uso com uma granularidade diária, o medidor retorna MB multiplicado pelo número de horas.    
### <a name="key-vault"></a>Key Vault   
  
**ID do medidor**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Nome do medidor**: Transações de Key Vault  
**Unidade**: Contagem de solicitações em 10, milhares  
**Observações**: Número de solicitações recebidas pelo plano de dados do Cofre de chaves da API REST.  
  
**ID do medidor**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Nome do medidor**: Transações de chaves avançadas  
**Unidade**:  10 mil transações  
**Observações**: Transações da chave RSA 3 mil/4 mil, ECC. (visualização).  
  
### <a name="app-service"></a>serviço de aplicativo   
  
**ID do medidor**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Nome do medidor**: Serviço de Aplicativo  
**Unidade**: Horas de núcleo virtual  
**Observações**: Número de núcleos virtuais usados para executar o serviço de aplicativo. Observação: A Microsoft usa esse medidor cobrar o serviço de aplicativo no Azure Stack. Provedores de serviços de nuvem pode usar serviço de aplicativo metros (abaixo) para calcular o uso de seus locatários.  
  
**ID do medidor**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Nome do medidor**: Solicitações de funções  
**Unidade**: 10 solicitações  
**Observações**: Número total de execuções solicitados (por 10 execuções). Execuções são contadas toda vez que uma função é executada em resposta a um evento ou é disparada por uma associação.  
  
**ID do medidor**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Nome do medidor**: Funções – computação  
**Unidade**:  GB-s  
**Observações**:  Consumo de recursos é medido em gigabyte segundos (GB/s). **Observado o consumo de recursos** é calculado pela multiplicação do tamanho médio da memória em GB pelo tempo em milissegundos que leva para executar a função. Memória usada por uma função é medida arredondando-se para a 128 MB mais próximo, até o tamanho máximo de memória de 1.536 MB, com o tempo de execução calculado arredondando até o mais próximo 1 ms. O tempo de execução mínimo e a memória para uma única execução de função é 100 ms e 128 mb, respectivamente.  
  
**ID do medidor**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Nome do medidor**: Horas de serviço de aplicativo compartilhado  
**Unidade**: 1 hora  
**Observações**: Conforme a utilização de hora do fragmento plano do serviço de aplicativo. Planos são medidos em uma base por aplicativo.  
  
**ID do medidor**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Nome do medidor**: Horas de serviço de aplicativo gratuito  
**Unidade**: 1 hora  
**Observações**: Conforme a utilização de hora do plano de serviço de aplicativo gratuito. Planos são medidos em uma base por aplicativo.  
  
**ID do medidor**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Nome do medidor**: Horas de serviço de aplicativo Standard pequeno  
**Unidade**: 1 hora  
**Observações**: Calculado com base no tamanho e número de instâncias.  
  
**ID do medidor**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Nome do medidor**: Horas de serviço de aplicativo Standard médio  
**Unidade**: 1 hora  
**Observações**: Calculado com base no tamanho e número de instâncias.  
  
**ID do medidor**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Nome do medidor**: Horas de serviço de aplicativo Standard grande  
**Unidade**: 1 hora  
**Observações**: Calculado com base no tamanho e número de instâncias.  
  
### <a name="custom-worker-tiers"></a>Camadas de trabalhador personalizado   
  
**ID do medidor**: *Camadas de trabalhador personalizado*  
**Nome do medidor**: Camadas de trabalhador personalizado  
**Unidade**: Horas  
**Observações**: ID do medidor determinística é criado com base na SKU e o nome da camada de trabalhador personalizado. Essa ID do medidor é exclusivo para cada camada de trabalho personalizado.  
  
**ID do medidor**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Nome do medidor**: SSL SNI  
**Unidade**: Por associação SSL de SNI  
**Observações**: O serviço de aplicativo dá suporte a dois tipos de conexões de SSL: Conexões SSL de SNI (Indicação de Nome de Servidor) e Conexões SSL de Endereço IP. O SSL baseado em SNI funciona em navegadores modernos, enquanto o SSL baseado em IP funciona em todos os navegadores.  
  
**ID do medidor**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Nome do medidor**: IP SSL  
**Unidade**: Por IP com base em associação de SSL  
**Observações**: O serviço de aplicativo dá suporte a dois tipos de conexões de SSL: Conexões SSL de SNI (Indicação de Nome de Servidor) e Conexões SSL de Endereço IP. O SSL baseado em SNI funciona em navegadores modernos, enquanto o SSL baseado em IP funciona em todos os navegadores.  
  
**ID do medidor**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Nome do medidor**:  Processo da Web  
**Unidade**:  
**Observações**: Calculado por site do Active Directory por hora.  
  
**ID do medidor**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Nome do medidor**: Largura de banda de saída externa  
**Unidade**: GB  
**Observações**: Bytes de resposta de solicitação de entrada total + solicitação de saída total bytes + de FTP de entrada total de solicitação web de entrada total + de bytes de resposta implanta bytes de resposta de solicitação.  
  

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsdocsmicrosoftcomazurebillingbilling-usage-rate-card-overviewazure-resource-usage-api-preview-currently-in-public-preview"></a>Como fazer o uso do Azure Stack APIs comparam com o [API de uso do Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-usage-api-preview) (atualmente em versão prévia)?
* A API de uso do locatário é consistente com a API do Azure, com uma exceção: o *showDetails* sinalizador atualmente não tem suporte no Azure Stack.
* A API de uso do provedor se aplica somente ao Azure Stack.
* Atualmente, o [API RateCard](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-ratecard-api-preview) que é disponível no Azure não está disponível no Azure Stack.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Qual é a diferença entre o tempo de uso e a hora relatada?
Relatórios de dados de uso tem dois valores de hora principal:

* **Tempo informado**. A hora quando o evento de uso entrou no sistema de uso
* **Tempo de uso**. O tempo quando o recurso do Azure Stack foi consumido

Talvez você veja uma discrepância nos valores de tempo de uso e a hora relatada para um evento de uso específico. O atraso pode ser várias horas em qualquer ambiente.

No momento, você pode consultar somente pelo *tempo relatados*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>O que significam esses códigos de erro da API de uso?
| **Código de status HTTP** | **Código de erro** | **Descrição** |
| --- | --- | --- |
| Solicitação inválida/400 |*NoApiVersion* |O *api-version* parâmetro de consulta está ausente. |
| Solicitação inválida/400 |*InvalidProperty* |Uma propriedade está ausente ou tem um valor inválido. A mensagem no código de erro no corpo da resposta identifica a propriedade ausente. |
| Solicitação inválida/400 |*RequestEndTimeIsInFuture* |O valor para *ReportedEndTime* está no futuro. Valores no futuro não são permitidos para esse argumento. |
| Solicitação inválida/400 |*SubscriberIdIsNotDirectTenant* |Uma chamada de API do provedor tem usado uma ID de assinatura não é um locatário válido do chamador. |
| Solicitação inválida/400 |*SubscriptionIdMissingInRequest* |A ID da assinatura do chamador está ausente. |
| Solicitação inválida/400 |*InvalidAggregationGranularity* |Uma granularidade de agregação inválida foi solicitada. Os valores válidos são diariamente e por hora. |
| 503 |*ServiceUnavailable* |Erro com nova tentativa porque o serviço está ocupado ou a chamada está sendo limitada. |

## <a name="what-is-the-policy-for-charging-for-vms"></a>Qual é a política para cobrar para VMs?

As VMs em execução e interrompidas geram dados de uso. Consistente com o Azure, desalocação é necessária para interromper a emissão de dados de uso. No caso em que o portal não está disponível, mas o provedor de recursos de computação ainda está em execução, o uso será emitido.

## <a name="next-steps"></a>Próximas etapas
[Cobrança do cliente e recuperação no Azure Stack](azure-stack-billing-and-chargeback.md)

[API de uso de recursos do provedor](azure-stack-provider-resource-api.md)

[API de uso de recurso de locatário](azure-stack-tenant-resource-usage-api.md)
