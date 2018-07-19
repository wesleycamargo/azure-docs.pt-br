---
title: Camadas do serviço do Banco de Dados SQL do Azure - DTU | Microsoft Docs
description: Saiba mais sobre as camadas de serviço para bancos de dados únicos e em pool para fornecer níveis de desempenho e tamanhos de armazenamento.
services: sql-database
author: sachinpMSFT
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/28/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: d6dc641123e2bf840940f6246245a89fdd792db5
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131830"
---
# <a name="choosing-a-dtu-based-service-tier-performance-level-and-storage-resources"></a>Escolha uma camada de serviço com base em DTU, nível de desempenho e recursos de armazenamento 

As camadas de serviço são diferenciadas pelos intervalos de níveis de desempenho com uma quantidade fixa de armazenamento incluído, período de retenção fixo para backups e preço fixo. Todas as camadas de serviço fornecem flexibilidade para alterar os níveis de desempenho sem tempo de inatividade. Os bancos de dados individuais e os pools elásticos são cobrados por hora com base na camada de serviço e no nível de desempenho.

> [!IMPORTANT]
> A Instância Gerenciada do Banco de Dados SQL, atualmente em visualização pública, não dá suporte a um modelo de compra baseado em DTU. Para obter mais informações, consulte [Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance.md). 

## <a name="choosing-a-dtu-based-service-tier"></a>Escolhendo uma camada de serviço com base em DTU

Escolher uma camada de serviço depende principalmente da continuidade dos negócios, armazenamento e requisitos de desempenho.
||Basic|Standard|Premium|
| :-- | --: |--:| --:| --:| 
|Carga de trabalho de destino|Desenvolvimento e produção|Desenvolvimento e produção|Desenvolvimento e produção||
|SLA de tempo de atividade|99,99%|99,99%|99,99%|N/d enquanto estiver em versão prévia|
|Retenção de backup|7 dias|35 dias|35 dias|
|CPU|Baixo|Baixo, Médio, Alto|Médio, Alto|
|Taxa de transferência de E/S (aproximada) |2.5 IOPS por DTU| 2.5 IOPS por DTU | 48 IOPS por DTU|
|Latência de E/S (aproximada)|5 ms (leitura), 10 ms (gravação)|5 ms (leitura), 10 ms (gravação)|2 ms (leitura/gravação)|
|Indexação ColumnStore |N/D|S3 e acima|Com suporte|
|OLTP na memória|N/D|N/D|Com suporte|
|||||

## <a name="single-database-dtu-and-storage-limits"></a>DTU de banco de dados único e limite de armazenamento

Os níveis de desempenho são expressos em termos de DTUs (Unidades de transação de banco de dados) para bancos de dados individuais, e eDTUs (Unidades de transação de banco de dados elásticos) para pools elásticos. Para saber mais sobre DTUs e eDTUs, consulte [O que são DTUs e eDTUs](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)?

||Basic|Standard|Premium|
| :-- | --: | --: | --: | --: |
| Tamanho máximo de armazenamento | 2 GB | 1 TB | 4 TB  | 
| Máximo de DTUs | 5 | 3000 | 4000 | |
||||||

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>EDTU de pool elástico, armazenamento e limites de banco de dados em pool

| | **Básico** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Tamanho máximo de armazenamento por banco de dados  | 2 GB | 1 TB | 1 TB | 
| Tamanho máximo de armazenamento por pool | 156 GB | 4 TB | 4 TB | 
| Máximo de eDTUs por banco de dados | 5 | 3000 | 4000 | 
| Máximo de eDTUs por pool | 1600 | 3000 | 4000 | 
| Número máximo de bancos de dados por pool | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> Mais de 1 TB de armazenamento na camada Premium está disponível atualmente em todas as regiões, exceto as seguintes: , Centro-Oeste dos EUA, Leste da China, USDoDCentral, USGov Iowa, Alemanha Central, USDoDEast, US Gov Sudoeste, Nordeste da Alemanha, Norte da China. Em outras regiões, o armazenamento máximo na camada Premium é limitado a 1 TB. Consulte [Limitações atuais de P11-P15](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

## <a name="next-steps"></a>Próximas etapas

- Para obter detalhes sobre níveis de desempenho específicos e opções de tamanho de armazenamento disponíveis para bancos de dados individuais, consulte [Limites de recursos baseados em DTU do Banco de Dados SQL para bancos de dados individuais](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels).
- Para obter detalhes sobre níveis de desempenho específicos e opções de tamanho de armazenamento disponíveis para pools elásticos, consulte [Limites de recursos baseados em DTU do Banco de Dados SQL ](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels).
