---
title: "BCDR (continuidade dos negócios e recuperação de desastre): regiões emparelhadas do Azure | Microsoft Docs"
description: Os pares regionais do Azure garantem que os aplicativos sejam resilientes durante as falhas dos data centers.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c2d0a21c-2564-4d42-991a-bc31723f61a4
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 780e47f68dea6ff4ba85761c3465871694b2b736


---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Continuidade dos negócios e recuperação de desastres (BCDR): Regiões Emparelhadas do Azure
## <a name="what-are-paired-regions"></a>O que são regiões emparelhadas?
O Azure opera em várias regiões geográficas em todo o mundo. Uma geografia do Azure é uma área definida do mundo que contém, pelo menos, uma Região do Azure. Uma região do Azure é uma área dentro de uma região geográfica que contém um ou mais data centers.

Cada região do Azure é emparelhada com outra região na mesma área geográfica, formando juntas um par regional. A exceção é o Sul do Brasil, que é associado a uma região fora de sua área geográfica.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Figura 1 – Diagrama do par da região do Azure

| painel Geografia do app&#39;s selecionado | Regiões emparelhadas |  |
|:--- |:--- |:--- |
| América do Norte |Centro-Norte dos EUA |Centro-Sul dos Estados Unidos |
| América do Norte |Leste dos EUA |Oeste dos EUA |
| América do Norte |Leste dos EUA 2 |Centro dos EUA |
| América do Norte |Oeste dos EUA 2 |Centro-Oeste dos EUA |
| Europa |Norte da Europa |Europa Ocidental |
| Ásia |Sudeste da Ásia |Ásia Oriental |
| China |China Oriental |Norte da China |
| Japão |Leste do Japão |Oeste do Japão |
| Brasil |Sul do Brasil (1) |Centro-Sul dos Estados Unidos |
| Austrália |Leste da Austrália |Sudeste da Austrália |
| Governo dos EUA |Gov do Iowa nos EUA |Gov. dos EUA – Virgínia |
| Índia |Índia Central |Sul da Índia |
| Canadá |Canadá Central |Leste do Canadá |
| Reino Unido |Oeste do Reino Unido |Sul do Reino Unido |

Tabela 1 - Mapeamento de pares regionais do Azure

> (1) O Sul do Brasil é exclusivo porque ele está associado a uma região fora de sua própria região geográfica. Observe que a região secundária do Sul do Brasil é o Centro-Sul dos EUA. No entanto, a região secundária do Centro-Sul dos EUA não é o Sul do Brasil.
> 
> 

É recomendável que você replique as cargas de trabalho entre os pares regionais para se beneficiar das políticas de isolamento e a disponibilidade do Azure. Por exemplo, as atualizações do sistema Azure planejadas são implantadas em sequência (não ao mesmo tempo) em regiões emparelhadas. Isso significa que, mesmo no caso de uma atualização falhar, ambas as regiões não serão afetadas simultaneamente. Além disso, no caso improvável de uma interrupção ampla, a recuperação de pelo menos uma região de cada par é priorizada.

## <a name="an-example-of-paired-regions"></a>Um exemplo de regiões emparelhadas
A Figura 2 a seguir mostra um aplicativo hipotético, que usa o par regional para recuperação de desastre. Os números verdes realçam as atividades entre regiões dos três serviços do Azure (computação do Azure, armazenamento e banco de dados) e como eles são configurados para replicar entre regiões. As vantagens exclusivas de implantação entre regiões emparelhadas são realçadas pelos números laranja.

![Visão geral dos benefícios das regiões emparelhadas](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figura 2 – par da região do Azure hipotético

## <a name="cross-region-activities"></a>Atividades entre regiões
Como mencionado na Figura 2.

![1Verde](./media/best-practices-availability-paired-regions/1Green.png) **Computação do Azure (PaaS)** – Você deve provisionar recursos de computação adicionais com antecedência para garantir que recursos estejam disponíveis em outra região durante um desastre. Para saber mais, consulte [Diretrizes técnicas de resiliência do Azure](resiliency/resiliency-technical-guidance.md).

![2Verde](./media/best-practices-availability-paired-regions/2Green.png) **Armazenamento do Azure** - Geo-Redundant storage (GRS) is configured by default when an Armazenamento do Azure account is created. Com o GRS, seus dados são replicados automaticamente três vezes na região primária e três vezes na região emparelhada. Para saber mais, consulte [Opções de redundância do Armazenamento do Azure](storage/storage-redundancy.md).

![3Verde](./media/best-practices-availability-paired-regions/3Green.png)**Bancos de dados SQL do Azure** – Com a replicação geográfica padrão do SQL do Azure, você pode configurar a replicação assíncrona de transações para uma região emparelhada. Com replicação geográfica premium, você pode configurar a replicação para qualquer região do mundo; no entanto, é recomendável que esses recursos sejam implantados em uma região emparelhada para a maioria dos cenários de recuperação de desastre. Para saber mais, confira [Replicação geográfica no Banco de Dados SQL do Azure](sql-database/sql-database-geo-replication-overview.md).

![4Verde](./media/best-practices-availability-paired-regions/4Green.png) **ARM (Gerenciador de Recursos do Azure)** - o ARM fornece inerentemente isolamento lógico dos componentes do gerenciamento de serviço entre regiões. Isso significa que falhas lógicas em uma região são apresentam menor probabilidade de afetar as outras.

## <a name="benefits-of-paired-regions"></a>Benefícios de uma região emparelhada
Como mencionado na Figura 2.  

![5Orange](./media/best-practices-availability-paired-regions/5Orange.png)
**Isolamento físico** – sempre que possível, o Azure prefere pelo menos 300 milhas de separação entre os datacenters em um par regional, embora isso não seja possível nem conveniente em todas as áreas geográficas. A separação de data center físico reduz a probabilidade de desastres naturais, conflitos civis, quedas de energia ou interrupções de rede física que afetem as duas regiões ao mesmo tempo. Isolamento está sujeito às restrições dentro da região geográfica (tamanho da região geográfica, disponibilidade da infraestrutura de rede/alimentação, normas, etc.).  

![6Orange](./media/best-practices-availability-paired-regions/6Orange.png)
**Replicação fornecida pela plataforma** – Alguns serviços, como o armazenamento com redundância geográfica, fornecem replicação automática para a região emparelhada.

![7Orange](./media/best-practices-availability-paired-regions/7Orange.png)
**Pedido de recuperação da região** – no caso de uma interrupção ampla, é priorizada a recuperação de uma região de cada par. Os aplicativos que são implantados em regiões emparelhadas são garantidos para terem uma das regiões recuperadas com prioridade. Se um aplicativo é implantado em regiões que não são emparelhadas, pode haver um atraso na recuperação – no pior caso, as regiões escolhidas podem ser as duas últimas a serem recuperadas.

![8Orange](./media/best-practices-availability-paired-regions/8Orange.png)
**Atualizações sequenciais** – as atualizações planejadas do sistema Azure são distribuídas em regiões emparelhadas sequencialmente (não ao mesmo tempo) para minimizar o tempo de inatividade, o efeito de erros e as falhas lógicas na eventualidade de uma atualização incorreta.

![9Orange](./media/best-practices-availability-paired-regions/9Orange.png)
**Residência de dados** – uma região reside na mesma região geográfica que seu par (com exceção do Sul do Brasil) para atender aos requisitos de residência de dados para fins de jurisdição de vigência fiscal e legal.




<!--HONumber=Nov16_HO3-->


