---
title: Serviço do Banco de Dados SQL do Azure - DTU | Microsoft Docs
description: Saiba mais sobre as camadas de serviço para bancos de dados únicos e em pool para fornecer níveis de desempenho e tamanhos de armazenamento.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 05/22/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 075e04db27006719ec7d6e08eb2696436d1010f4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648642"
---
# <a name="dtu-based-purchasing-model-for-azure-sql-database"></a>Modelo de compra baseado em DTU para o Banco de Dados SQL do Azure 


O [Banco de Dados SQL do Azure ](sql-database-technical-overview.md) oferece dois modelos de compra para recursos de E/S, armazenamento e computação: um modelo de compra baseado em DTU e um modelo de compra baseado em vCore (versão prévia). A tabela e o gráfico a seguir comparam e contrastam esses dois modelos de compra.

> [!IMPORTANT]
> Para [Modelo de compra baseado em vCore (versão prévia)](sql-database-service-tiers-vcore.md), consulte Modelo de compra baseado em vCore (versão prévia)


|**Modelo de compra**|**Descrição**|**Mais adequado para**|
|---|---|---|
|Modelo baseado em DTU|Esse modelo é baseado em uma medida em pacote de recursos de E/S, armazenamento e computação. Os níveis de desempenho são expressos em termos de DTUs (Unidades de transação de banco de dados) para bancos de dados individuais, e eDTUs (Unidades de transação de banco de dados elásticos) para pools elásticos. Para saber mais sobre DTUs e eDTUs, consulte [O que são DTUs e eDTUs](sql-database-what-is-a-dtu.md)?|Mais adequado para clientes que desejam opções de recursos simples e pré-configuradas.| 
|Modelo baseado em vCore|Esse modelo permite escalar recursos de armazenamento e computação de maneira independente. Além disso, permite usar o Benefício Híbrido do Azure para SQL Server para obter redução de custos.|Mais adequado para clientes que valorizam flexibilidade, controle e transparência.|
||||  

![modelo de preços](./media/sql-database-service-tiers/pricing-model.png)

## <a name="dtu-based-purchasing-model"></a>Modelo de compra com base em DTU

A DTU (Unidade de Transmissão de Dados) representa uma medida combinada de CPU, memória, leituras e gravações. O modelo de compra baseado em DTU oferece um conjunto de pacotes pré-configurados de recursos de computação e inclui armazenamento para impulsionar diferentes níveis de desempenho do aplicativo. Clientes que preferem a simplicidade de um pacote pré-configurado e pagamentos fixos por mês podem localizar o modelo baseado em DTU mais adequado às suas necessidades. No modelo de compra baseado em DTU, os clientes podem escolher entre as camadas de serviço **Básico**, **Standard** e **Premium** para ambos os [bancos de dados individuais](sql-database-single-database-resources.md) e [pools elásticos](sql-database-elastic-pool.md). As camadas de serviço são diferenciadas pelos intervalos de níveis de desempenho com uma quantidade fixa de armazenamento incluído, período de retenção fixo para backups e preço fixo. Todas as camadas de serviço fornecem flexibilidade para alterar os níveis de desempenho sem tempo de inatividade. Os bancos de dados individuais e os pools elásticos são cobrados por hora com base na camada de serviço e no nível de desempenho.

> [!IMPORTANT]
> A Instância Gerenciada do Banco de Dados SQL, atualmente em visualização pública, não dá suporte a um modelo de compra baseado em DTU. Para obter mais informações, consulte [Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance.md). 

## <a name="choosing-a-service-tier-in-the-dtu-based-purchasing-model"></a>Escolher uma camada de serviço no modelo de compra baseado em DTU

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

## <a name="performance-level-and-storage-size-limits-in-the-dtu-based-purchasing-model"></a>Nível de desempenho e limites de tamanho de armazenamento no modelo de compra baseado em DTU

Os níveis de desempenho são expressos em termos de DTUs (Unidades de transação de banco de dados) para bancos de dados individuais, e eDTUs (Unidades de transação de banco de dados elásticos) para pools elásticos. Para saber mais sobre DTUs e eDTUs, consulte [O que são DTUs e eDTUs](sql-database-what-is-a-dtu.md)?

### <a name="single-databases"></a>Bancos de dados únicos

||Basic|Standard|Premium|
| :-- | --: | --: | --: | --: |
| Tamanho máximo de armazenamento* | 2 GB | 1 TB | 4 TB  | 
| Máximo de DTUs | 5 | 3000 | 4000 | |
||||||

Para obter detalhes sobre níveis de desempenho específicos e opções de tamanho de armazenamento disponíveis para bancos de dados individuais, consulte [Limites de recursos baseados em DTU do Banco de Dados SQL para bancos de dados individuais](sql-database-dtu-resource-limits.md#single-database-storage-sizes-and-performance-levels).

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
> - Tamanhos de armazenamento maiores que a quantidade de armazenamento incluída estão em versão prévia e aplicam-se custos extras. Para obter detalhes, confira [Preços de Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). 
> - Mais de 1 TB de armazenamento na camada Premium está disponível em todas as regiões, exceto as seguintes: Norte do Reino Unido, Centra-oeste dos EUA, Sul do Reino 2, Leste da China, US DoD Central, Alemanha Central, US DoD Leste, US Gov Sudoeste, US Gov Centro-Sul, Nordeste da Alemanha, Norte da China, US Gov Leste. Há planos para uma disponibilidade mais ampla. Em outras regiões, o armazenamento máximo na camada Premium é limitado a 1 TB. Consulte [Limitações atuais de P11-P15](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 
Para obter detalhes sobre níveis de desempenho específicos e opções de tamanho de armazenamento disponíveis para pools elásticos, consulte [Limites de recursos baseados em DTU do Banco de Dados SQL ](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).



## <a name="next-steps"></a>Próximas etapas

- Para obter detalhes sobre níveis de desempenho específicos e opções de tamanho de armazenamento disponíveis, consulte [Limites de recursos baseados em DTU do Banco de Dados SQL](sql-database-dtu-resource-limits.md) e [Limites de recursos baseados em vCore do Banco de Dados SQL](sql-database-vcore-resource-limits.md).
- Consulte [Perguntas frequentes sobre Banco de Dados SQL](sql-database-faq.md) para obter respostas para as perguntas frequentes.
- Saiba mais sobre a [Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md)
