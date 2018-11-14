---
title: O Armazenamento com redundância geográfica (GRS) para durabilidade entre regiões no Armazenamento do Azure | Microsoft Docs
description: O Armazenamento com redundância geográfica (GRS) replica seus dados entre duas regiões que estão a centenas de milhas de distância. O GRS protege contra falhas de hardware no datacenter, além de desastres regionais.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 10/20/2018
ms.author: jeking
ms.component: common
ms.openlocfilehash: 85d69db2f94e4bddf1258233c34c64dcf78a3eeb
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219215"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>GRS (armazenamento com redundância geográfica): replicação inter-regional para Armazenamento do Microsoft Azure
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>Armazenamento com redundância geográfica com acesso de leitura
O RA-GRS (armazenamento com redundância geográfica com acesso de leitura) maximiza a disponibilidade para sua conta de armazenamento. RA-GRS fornece acesso somente leitura aos dados no local secundário, além de replicação geográfica em duas regiões.

Quando você habilita o acesso somente leitura aos dados na região secundária, os dados ficam disponíveis em um ponto de extremidade secundário, bem como no ponto de extremidade primário de sua conta de armazenamento. O ponto de extremidade secundário é semelhante ao ponto de extremidade primário, mas anexa o sufixo `–secondary` ao nome da conta. Por exemplo, se o ponto de extremidade primário para o serviço Blob for `myaccount.blob.core.windows.net`, seu ponto de extremidade secundário será `myaccount-secondary.blob.core.windows.net`. As chaves de acesso para sua conta de armazenamento são as mesmas para os pontos de extremidade primário e secundário.

Algumas considerações relacionadas ao uso do RA-GRS:

* Seu aplicativo precisa gerenciar com qual ponto de extremidade está interagindo ao usar o RA-GRS.
* Como a replicação assíncrona envolve um atraso, as alterações que ainda não foram replicadas para a região secundária poderão ser pedidas se os dados não puderem ser recuperados na região primária.
* Você pode verificar o Horário da Última Sincronização da sua conta de armazenamento. A Hora da Última Sincronização é um valor de data/hora GMT. Todas as gravações primárias antes da Hora da Última Sincronização foram gravadas com êxito no local secundário, o que significa que estão disponíveis para serem lidas no local secundário. As gravações primárias após a hora da última sincronização podem ou não estarem disponíveis para leituras. Consulte esse valor usando o [portal do Azure](https://portal.azure.com/), o [Azure PowerShell](storage-powershell-guide-full.md) ou uma das bibliotecas de clientes do Armazenamento do Azure.
* Se a Microsoft iniciar um failover para a região secundária, você terá acesso de leitura e gravação aos dados após o failover ter sido concluído. Para obter mais informações, consulte [Diretrizes de recuperação de desastre](storage-disaster-recovery-guidance.md).
* Para obter informações sobre como alternar para a região secundária, consulte [O que fazer se ocorrer uma interrupção do Armazenamento do Azure](storage-disaster-recovery-guidance.md).
* O RA-GRS é para fins de alta disponibilidade. Para obter diretrizes de escalabilidade, examine a [lista de verificação de desempenho](storage-performance-checklist.md).
* Para sugestões sobre como projetar para alta disponibilidade com o RA-GRS, consulte [Projetando Aplicativos Altamente Disponíveis usando o armazenamento RA-GRS](storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>O que é RPO e RTO com GRS?
**Objetivo do ponto de recuperação (RPO):** Em GRS e RA-GRS, o serviço de armazenamento replica de forma assíncrona os dados do local primário para o secundário. No caso de um desastre regional grave na região primária, a Microsoft fará um failover para a região secundária. Se ocorrer um failover, as alterações recentes que ainda não foram replicadas geograficamente poderão ser perdidas. O número de minutos de dados possíveis perdidos é conhecido como o RPO. O RPO indica o ponto no tempo em que os dados podem ser recuperados. Normalmente, o Armazenamento do Microsoft Azure tem um RPO inferior a 15 minutos, embora atualmente não exista nenhum SLA sobre quanto tempo leva a replicação geográfica.

**RTO (Objetivo de Tempo de Recuperação):** o RTO é uma medida de quanto tempo é necessário para fazer o failover e colocar a conta de armazenamento novamente online. O tempo para fazer o failover inclui as seguintes ações:

   * O tempo exigido pela Microsoft para determinar se os dados podem ser recuperados no local primário ou se um failover é necessário
   * O tempo para fazer o failover da conta de armazenamento, alterando as entradas DNS primárias para que elas apontem para o local secundário

A Microsoft leva a sério a responsabilidade de preservar seus dados. Se houver uma possibilidade de recuperação dos dados na região primária, a Microsoft atrasará o failover e se concentrará na recuperação de seus dados. 

## <a name="paired-regions"></a>Regiões emparelhadas 
Quando você cria uma conta de armazenamento, pode selecionar a região primária para a conta. A região secundária emparelhada é determinada com base na região primária e não pode ser alterada. Para obter informações atualizadas sobre regiões suportadas pelo Azure, consulte [Continuidade de negócios e recuperação de desastre (BCDR): regiões emparelhadas do Azure](../../best-practices-availability-paired-regions.md).

## <a name="see-also"></a>Consulte também
- [Replicação de Armazenamento do Azure](storage-redundancy.md)
- [LRS (armazenamento com redundância local): redundância de dados de baixo custo para Armazenamento do Microsoft Azure](storage-redundancy-lrs.md)
- [ZRS (armazenamento com redundância de zona): aplicativos de Armazenamento do Microsoft Azure altamente disponíveis](storage-redundancy-zrs.md)