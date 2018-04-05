---
title: Serviço do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre as camadas de serviço para bancos de dados únicos e em pool para fornecer níveis de desempenho e tamanhos de armazenamento.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 03/21/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 3fe7add4ad4c5563a6307db0dec27c658913429b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="what-are-azure-sql-database-service-tiers"></a>Quais são as camadas de serviço do Banco de Dados SQL do Azure?

[Banco de Dados SQL](sql-database-technical-overview.md) do Azure oferece as camadas de serviço **Basic**, **Standard**, e  **Premium** para ambos [os bancos de dados únicos ](sql-database-single-database-resources.md) e [pools elásticos](sql-database-elastic-pool.md). O Banco de Dados SQL oferece uma camada de serviço de Uso Geral para a [Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance.md#managed-instance-service-tier). As camadas de serviço são diferenciadas principalmente por uma gama de níveis de desempenho e opções de tamanho de armazenamento, além do preço.  Todas as camadas de serviço fornecem flexibilidade na alteração do tamanho de armazenamento e no nível de desempenho.  Bancos de dados individuais e pools elásticos são cobrados por hora com base na camada de serviço, nível de desempenho e tamanho de armazenamento.   

> [!IMPORTANT]
> A Instância Gerenciada do Banco de Dados SQL, atualmente em visualização pública, oferece uma única camada de serviço de uso geral. Para obter mais informações, consulte [Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance.md). O restante deste artigo não se aplica à Instância Gerenciada.

## <a name="choosing-a-service-tier"></a>Como escolher uma camada de serviço

Escolher uma camada de serviço depende principalmente da continuidade dos negócios, armazenamento e requisitos de desempenho.
| | **Básico** | **Standard** |**Premium**  |
| :-- | --: |--:| --:| --:| 
|Carga de trabalho de destino|Desenvolvimento e produção|Desenvolvimento e produção|Desenvolvimento e produção||
|SLA de tempo de atividade|99,99%|99,99%|99,99%|N/d enquanto estiver em versão prévia|
|Retenção de backup|7 dias|35 dias|35 dias|
|CPU|Baixo|Baixo, Médio, Alto|Médio, Alto|
|Taxa de transferência de E/S (aproximada) |2.5 IOPS por DTU  | 2.5 IOPS por DTU | 48 IOPS por DTU|
|Latência de E/S (aproximada)|5 ms (leitura), 10 ms (gravação)|5 ms (leitura), 10 ms (gravação)|2 ms (leitura/gravação)|
|Indexação ColumnStore |N/D|S3 e acima|Com suporte|
|OLTP na memória|N/D|N/D|Com suporte|
|||||

## <a name="performance-level-and-storage-size-limits"></a>Limites de tamanho de armazenamento e nível de desempenho

Os níveis de desempenho são expressos em termos de DTUs (Unidades de transação de banco de dados) para bancos de dados individuais, e eDTUs (Unidades de transação de banco de dados elásticos) para pools elásticos. Para saber mais sobre DTUs e eDTUs, confira [O que são DTUs e eDTUs?](sql-database-what-is-a-dtu.md)

### <a name="single-databases"></a>Bancos de dados únicos

|  | **Básico** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Tamanho máximo de armazenamento* | 2 GB | 1 TB | 4 TB  | 
| Máximo de DTUs | 5 | 3000 | 4000 | |
||||||

### <a name="elastic-pools"></a>Pools elásticos

| | **Básico** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Tamanho máximo de armazenamento por banco de dados*  | 2 GB | 1 TB | 1 TB | 
| Tamanho máximo de armazenamento por pool* | 156 GB | 4 TB | 4 TB | 
| Máximo de eDTUs por banco de dados | 5 | 3000 | 4000 | 
| Máximo de eDTUs por pool | 1600 | 3000 | 4000 | 
| Número máximo de bancos de dados por pool | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> \* Tamanhos de armazenamento maiores que a quantidade de armazenamento incluída estão em versão prévia e aplicam-se custos extras. Para obter detalhes, confira [Preços de Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). 
>
> \* Na camada Premium, mais de 1 TB de armazenamento está disponível atualmente nas seguintes regiões: Leste da Austrália, Sudeste da Austrália, Sul do Brasil, Central do Canadá, Leste do Canadá, Centro dos EUA, França Central, Centro da Alemanha, Leste do Japão, Oeste do Japão, Coreia Central, Centro-Norte dos EUA, Europa Setentrional, Centro-Sul dos EUA, Sudeste Asiático, Sul do Reino Unido, Oeste do Reino Unido, Leste dos EUA 2, Oeste dos EUA, Gov. EUA - Virgínia e Europa Ocidental. Consulte [Limitações atuais de P11-P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 

Para obter detalhes sobre os níveis de desempenho específicos e opções de tamanho de armazenamento disponíveis, consulte [Limites de recursos do Banco de Dados SQL](sql-database-resource-limits.md).


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Recursos de banco de dados individual](sql-database-single-database-resources.md).
- Saiba mais sobre pools elásticos em [Pools elásticos](sql-database-elastic-pool.md).
- Saiba mais sobre a [Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md)
* Saiba mais sobre [DTUs e eDTUs](sql-database-what-is-a-dtu.md).
* Saiba mais sobre como monitorar o uso de DTU em [Monitoramento e ajuste de desempenho](sql-database-troubleshoot-performance.md).

