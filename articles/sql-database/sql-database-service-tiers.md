---
title: "Serviço do Banco de Dados SQL do Azure | Microsoft Docs"
description: "Saiba mais sobre as camadas de serviço para bancos de dados únicos e em pool para fornecer níveis de desempenho e tamanhos de armazenamento."
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 02/12/2018
ms.author: carlrab
ms.openlocfilehash: c0dc3181d3cd5c642dfca1c0f6031b83726478c0
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2018
---
# <a name="what-are-azure-sql-database-service-tiers"></a>Quais são as camadas de serviço do Banco de Dados SQL do Azure?

[Banco de Dados SQL](sql-database-technical-overview.md) do Azure oferece as camadas de serviço **Basic**, **Standard**, e  **Premium** para ambos [os bancos de dados únicos ](sql-database-single-database-resources.md) e [pools elásticos](sql-database-elastic-pool.md). As camadas de serviço são diferenciadas principalmente por uma gama de níveis de desempenho e opções de tamanho de armazenamento, além do preço.  Todas as camadas de serviço fornecem flexibilidade na alteração do tamanho de armazenamento e no nível de desempenho.  Bancos de dados individuais e pools elásticos são cobrados por hora com base na camada de serviço, nível de desempenho e tamanho de armazenamento.   

## <a name="choosing-a-service-tier"></a>Como escolher uma camada de serviço

Escolher uma camada de serviço depende principalmente da continuidade dos negócios, armazenamento e requisitos de desempenho.
| | **Básico** | **Standard** |**Premium**  |
| :-- | --: |--:| --:| --:| 
|Carga de trabalho de destino|Desenvolvimento e produção|Desenvolvimento e produção|Desenvolvimento e produção||
|SLA de tempo de atividade|99,99%|99,99%|99,99%|N/d enquanto estiver em versão prévia|
|Retenção de backup|7 dias|35 dias|35 dias|
|CPU|Baixo|Baixo, Médio, Alto|Médio, Alto|
|Taxa de transferência de E/S|Baixo  | Média | Ordem de magnitude maior do que o Standard|
|Latência de E/S|Maior do que o Premium|Maior do que o Premium|Menor do que o Básico e Standard|
|Indexação de ColumnStore e OLTP in-memory|N/D|N/D|Com suporte|
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

