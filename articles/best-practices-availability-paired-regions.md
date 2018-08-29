---
title: 'BCDR (continuidade dos negócios e recuperação de desastre): regiões emparelhadas do Azure | Microsoft Docs'
description: Saiba mais sobre os pares regionais do Azure, que garantem que os aplicativos sejam resilientes durante falhas de data centers.
author: rayne-wiselman
ms.service: multiple
ms.topic: article
ms.date: 07/03/2018
ms.author: raynew
ms.openlocfilehash: 4084a5bd8cb82442eb37844f88f2ff6dd166b5ee
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42446057"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Continuidade dos negócios e recuperação de desastres (BCDR): Regiões Emparelhadas do Azure

## <a name="what-are-paired-regions"></a>O que são regiões emparelhadas?

O Azure opera em várias regiões geográficas em todo o mundo. Uma geografia do Azure é uma área definida do mundo que contém, pelo menos, uma Região do Azure. Uma região do Azure é uma área dentro de uma região geográfica que contém um ou mais datacenters.

Cada região do Azure é emparelhada com outra região na mesma área geográfica, formando juntas um par regional. A exceção é o Sul do Brasil, que está associado a uma região fora de sua área geográfica. Entre os pares de região, o Azure serializará atualizações da plataforma (manutenção planejada) para que apenas uma região emparelhada seja atualizada por vez. Além disso, em caso de interrupção que afete várias regiões, pelo menos uma região de cada par será priorizada para recuperação.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Figura 1 – Pares regionais do Azure

| painel Geografia do app&#39;s selecionado | Regiões emparelhadas |  |
|:--- |:--- |:--- |
| Ásia |Ásia Oriental |Sudeste Asiático |
| Austrália |Leste da Austrália |Sudeste da Austrália |
| Austrália |Austrália Central |Austrália Central 2 |
| Brasil |Sul do Brasil 2 |Centro-Sul dos Estados Unidos |
| Canadá |Canadá Central |Leste do Canadá |
| China |Norte da China |Leste da China|
| Europa |Norte da Europa |Europa Ocidental |
| Alemanha |Alemanha Central |Nordeste da Alemanha |
| Índia |Índia Central |Sul da Índia |
| Índia |Índia Ocidental (1) |Sul da Índia |
| Japão |Leste do Japão |Oeste do Japão |
| Coreia do Sul |Coreia Central |Sul da Coreia |
| América do Norte |Leste dos EUA |Oeste dos EUA |
| América do Norte |Leste dos EUA 2 |Centro dos EUA |
| América do Norte |Centro-Norte dos EUA |Centro-Sul dos Estados Unidos |
| América do Norte |Oeste dos EUA 2 |Centro-Oeste dos EUA 
| Reino Unido |Oeste do Reino Unido |Sul do Reino Unido |
| Departamento de Defesa dos EUA |DoD do Leste dos EUA |DoD Central dos EUA |
| Governo dos EUA |Governo dos EUA do Arizona |Governo dos EUA do Texas |
| Governo dos EUA |US Gov Iowa (3) |Gov. dos EUA – Virgínia |
| Governo dos EUA |Gov. EUA - Virgínia (4) |Governo dos EUA do Texas |

Tabela 1 – mapeamento de pares regionais do Azure

- (1) Índia Ocidental é diferente porque ele está associado a outra região em apenas uma direção. A região secundária da Índia Ocidental é o Sul da Índia, mas a região secundária do Sul da Índia é a Índia Central.
- (2) O Sul do Brasil é exclusivo porque ele está associado a uma região fora de sua própria região geográfica. A região secundária do Sul do Brasil é o Centro-Sul dos EUA. No entanto, a região secundária do Centro-Sul dos EUA não é o Sul do Brasil.
- (3) A região secundária do US Gov Iowa é US Gov Virginia, mas região secundária de US Gov Virginia não está no US Gov Iowa.
- (4) A região secundária do Gov. EUA Virgínia é Gov. EUA Texas, mas região secundária de Gov. EUA Texas não está no Gov. EUA Virgínia.


É recomendável que você configure a recuperação de desastre de continuidade de negócios (BCDR) entre os pares regionais para se beneficiar das políticas de isolamento e a disponibilidade do Azure. Para aplicativos que dão suporte a várias regiões do Active Directory, recomendamos usar ambas as regiões em um par de regiões, sempre que possível. Isso irá garantir a disponibilidade ideal para aplicativos e tempo de recuperação minimizado em caso de desastre. 

## <a name="an-example-of-paired-regions"></a>Um exemplo de regiões emparelhadas
A Figura 2 a seguir mostra um aplicativo hipotético, que usa o par regional para recuperação de desastre. Os números verdes realçam as atividades entre regiões dos três serviços do Azure (computação, armazenamento e banco de dados do Azure) e como eles são configurados para serem replicados entre as regiões. As vantagens exclusivas de implantação entre regiões emparelhadas são realçadas pelos números laranja.

![Visão geral dos benefícios das regiões emparelhadas](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figura 2 – par da região do Azure hipotético

## <a name="cross-region-activities"></a>Atividades entre regiões
Como mencionado na Figura 2.

![PaaS](./media/best-practices-availability-paired-regions/1Green.png) **Computação do Azure (PaaS)** – Você deve provisionar recursos de computação adicionais com antecedência para garantir que os recursos estejam disponíveis em outra região durante um desastre. Para saber mais, confira as [Orientação técnica de resiliência do Azure](resiliency/resiliency-technical-guidance.md).

![Armazenamento](./media/best-practices-availability-paired-regions/2Green.png) **Armazenamento do Azure** – O GRS (armazenamento com redundância geográfica) é configurado por padrão quando uma conta de armazenamento do Azure é criada. Com o GRS, seus dados são replicados automaticamente três vezes na região primária e três vezes na região emparelhada. Para saber mais, consulte [Opções de redundância do Armazenamento do Azure](storage/common/storage-redundancy.md).

![SQL Azure](./media/best-practices-availability-paired-regions/3Green.png) **Bancos de Dados SQL do Azure** – Com a replicação geográfica padrão do SQL Azure, você pode configurar a replicação assíncrona de transações como uma região emparelhada. Com a replicação geográfica premium, você pode configurar a replicação para qualquer região do mundo. No entanto, é recomendável que esses recursos sejam implantados em uma região emparelhada para a maioria dos cenários de recuperação de desastre. Para saber mais, confira [Replicação geográfica no Banco de Dados SQL do Azure](sql-database/sql-database-geo-replication-overview.md).

![Resource Manager](./media/best-practices-availability-paired-regions/4Green.png) **Azure Resource Manager** – O Resource Manager fornece de maneira inerente isolamento lógico dos componentes do gerenciamento de serviço entre as regiões. Isso significa que falhas lógicas em uma região são apresentam menor probabilidade de afetar as outras.

## <a name="benefits-of-paired-regions"></a>Benefícios de uma região emparelhada
Como mencionado na Figura 2.  

![Isolamento](./media/best-practices-availability-paired-regions/5Orange.png)
**Isolamento físico** – Sempre que possível, o Azure prefere pelo menos 300 milhas de separação entre os datacenters em um par regional, embora isso não seja possível nem conveniente em todas as áreas geográficas. A separação de data center físico reduz a probabilidade de desastres naturais, conflitos civis, quedas de energia ou interrupções de rede física que afetem as duas regiões ao mesmo tempo. Isolamento está sujeito às restrições dentro da região geográfica (tamanho da região geográfica, disponibilidade da infraestrutura de rede/alimentação, normas, etc.).  

![Replicação](./media/best-practices-availability-paired-regions/6Orange.png)
**Replicação fornecida pela plataforma** – Alguns serviços, como o armazenamento com redundância geográfica, fornecem replicação automática para a região emparelhada.

![Recuperação](./media/best-practices-availability-paired-regions/7Orange.png)
**Pedido de recuperação da região** – No caso de uma interrupção ampla, é priorizada a recuperação de uma região de cada par. Os aplicativos que são implantados em regiões emparelhadas são garantidos para terem uma das regiões recuperadas com prioridade. Se um aplicativo é implantado em regiões que não são emparelhadas, pode haver um atraso na recuperação – no pior caso, as regiões escolhidas podem ser as duas últimas a serem recuperadas.

![Atualizações](./media/best-practices-availability-paired-regions/8Orange.png)
**Atualizações sequenciais** – As atualizações planejadas do sistema Azure são distribuídas em regiões emparelhadas sequencialmente (não ao mesmo tempo) para minimizar o tempo de inatividade, o efeito de erros e as falhas lógicas na eventualidade de uma atualização incorreta.

![Dados](./media/best-practices-availability-paired-regions/9Orange.png)
**Residência dos dados** – Uma região reside na mesma região geográfica que seu par (com exceção do Sul do Brasil) para atender aos requisitos de residência de dados para fins de jurisdição de imposição fiscal e legal.
