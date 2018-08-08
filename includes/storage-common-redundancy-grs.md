---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: f5b6e0e74bbab33b9ae6fbacca5c55ea434d3e41
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399950"
---
O armazenamento com redundância geográfica (GRS) foi desenvolvido para fornecer pelo menos 99.99999999999999% (16 9’s) durabilidade dos objetos em um determinado ano, replicando dados para uma região secundária situada a centenas de milhas de distância da região primária. Se sua conta de armazenamento tem GRS habilitado, seus dados serão duráveis mesmo no caso de uma interrupção regional completa ou um desastre no qual a região principal não possa ser recuperada.

Se você optar pelo GRS, você tem duas opções relacionadas para escolher:

* O GRS replica seus dados para outro datacenter em uma região secundária, mas esses dados estão disponíveis para serem lidos somente se a Microsoft iniciar um failover da região primária para a secundária. 
* O armazenamento com redundância geográfica de acesso de leitura (RA-GRS) é baseado no GRS. O RA-GRS replica seus dados para outro datacenter em uma região secundária e também fornece a opção de ler a partir da região secundária. Com o RA-GRS, você pode ler a partir do secundário, independentemente de a Microsoft iniciar um failover do primário para o secundário. 

Para uma conta de armazenamento com GRS ou RA-GRS habilitada, todos os dados são primeiro replicados com armazenamento redundante localmente (LRS). Uma atualização primeiro é confirmada para o local primário e replicados usando o LRS. A atualização, em seguida, é replicada assincronamente para a região secundária usando GRS. Quando dados são gravados para o local secundário, ela também é replicada dentro desse local usando o LRS. 

Ambas as regiões primárias e secundárias gerenciam réplicas entre domínios de falha separados e atualizar domínios dentro de uma unidade de escala de armazenamento. A unidade de escala de armazenamento é a unidade de replicação básica dentro do datacenter. A replicação com esse nível é fornecida por LRS; Para obter mais informações, consulte [armazenamento localmente redundante (LRS): redundância de dados de baixo custo para armazenamento do Azure](../articles/storage/common/storage-redundancy-lrs.md).

Lembre-se esses pontos ao decidir qual opção de replicação para usar:

* Armazenamento com redundância de zona (ZRS) fornece alta disponibilidade com replicação síncrona e pode ser uma escolha melhor em alguns cenários que GRS ou RA-GRS. Para obter mais informações sobre o ZRS, consulte [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Como a replicação assíncrona envolve um atraso, no caso de um desastre regional, é possível que as alterações que ainda não foram replicadas para a região secundária sejam perdidas se os dados não puderem ser recuperados da região primária.
* Com o GRS, a réplica não está disponível, a menos que a Microsoft inicie o failover na região secundária. Se a Microsoft iniciar um failover para a região secundária, você terá acesso de leitura e gravação aos dados após o failover ter sido concluído. Para obter mais informações, confira [Guia de Recuperação de Desastres](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Se seu aplicativo precisar ler a partir da região secundária, ative o RA-GRS.

## <a name="read-access-geo-redundant-storage"></a>Armazenamento com redundância geográfica com acesso de leitura

O RA-GRS (armazenamento com redundância geográfica com acesso de leitura) maximiza a disponibilidade para sua conta de armazenamento. RA-GRS fornece acesso somente leitura aos dados no local secundário, além de replicação geográfica em duas regiões.

Quando você habilita o acesso somente leitura aos dados na região secundária, os dados ficam disponíveis em um ponto de extremidade secundário, bem como no ponto de extremidade primário de sua conta de armazenamento. O ponto de extremidade secundário é semelhante ao ponto de extremidade primário, mas anexa o sufixo `–secondary` ao nome da conta. Por exemplo, se o ponto de extremidade primário para o serviço Blob for `myaccount.blob.core.windows.net`, seu ponto de extremidade secundário será `myaccount-secondary.blob.core.windows.net`. As chaves de acesso para sua conta de armazenamento são as mesmas para os pontos de extremidade primário e secundário.

Algumas considerações relacionadas ao uso do RA-GRS:

* Seu aplicativo precisa gerenciar com qual ponto de extremidade está interagindo ao usar o RA-GRS.
* Como a replicação assíncrona envolve um atraso, as alterações que ainda não foram replicadas para a região secundária poderão ser pedidas se os dados não puderem ser recuperados na região primária, por exemplo, no caso de um desastre regional.
* Você pode verificar o Horário da Última Sincronização da sua conta de armazenamento. A Hora da Última Sincronização é um valor de data/hora GMT. Todas as gravações primárias antes da Hora da Última Sincronização foram gravadas com êxito no local secundário, o que significa que estão disponíveis para serem lidas no local secundário. As gravações primárias após a hora da última sincronização podem ou não estarem disponíveis para leituras. Consulte esse valor usando o [portal do Azure](https://portal.azure.com/), o [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md) ou uma das bibliotecas de clientes do Armazenamento do Azure.
* Se a Microsoft iniciar um failover para a região secundária, você terá acesso de leitura e gravação aos dados após o failover ter sido concluído. Para obter mais informações, consulte [Diretrizes de Recuperação de Desastre](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Para obter informações sobre como alternar para a região secundária, consulte [O que fazer se ocorrer uma interrupção do Armazenamento do Azure](../articles/storage/common/storage-disaster-recovery-guidance.md).
* O RA-GRS é para fins de alta disponibilidade. Para obter diretrizes de escalabilidade, examine a [lista de verificação de desempenho](../articles/storage/common/storage-performance-checklist.md).
* Para sugestões sobre como projetar para alta disponibilidade com o RA-GRS, consulte [Projetando Aplicativos Altamente Disponíveis usando o armazenamento RA-GRS](../articles/storage/common/storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>O que é RPO e RTO com GRS?
**Objetivo do ponto de recuperação (RPO):** Em GRS e RA-GRS, o serviço de armazenamento replica de forma assíncrona os dados do local primário para o secundário. No caso de um desastre regional grave na região primária, a Microsoft fará um failover para a região secundária. Se ocorrer um failover, as alterações recentes que ainda não foram replicadas geograficamente poderão ser perdidas. O número de minutos de dados potencialmente perdidos é chamado de RPO, e isso indica o momento em que os dados podem ser recuperados. Normalmente, o Armazenamento do Azure tem um RPO inferior a 15 minutos, embora atualmente não exista nenhum SLA sobre quanto tempo leva a replicação geográfica.

**RTO (Objetivo de Tempo de Recuperação):** o RTO é uma medida de quanto tempo é necessário para fazer o failover e colocar a conta de armazenamento novamente online. O tempo para fazer o failover inclui as seguintes ações:

   * O tempo exigido pela Microsoft para determinar se os dados podem ser recuperados no local primário, ou se um failover é necessário.
   * O tempo para fazer o failover da conta de armazenamento, alterando as entradas DNS primárias para que elas apontem para o local secundário.

   A Microsoft leva a sério a responsabilidade de preservar seus dados. Se houver uma possibilidade de recuperação dos dados na região primária, a Microsoft atrasará o failover e se concentrará na recuperação de seus dados. Uma versão futura do serviço permitirá que você dispare um failover em um nível de conta, para você controlar o RTO por conta própria.

## <a name="paired-regions"></a>Regiões emparelhadas 

Quando você cria uma conta de armazenamento, pode selecionar a região primária para a conta. A região secundária emparelhada é determinada com base na região primária e não pode ser alterada. Para obter informações atualizadas sobre regiões suportadas pelo Azure, consulte [Continuidade de negócios e recuperação de desastre (BCDR): Regiões emparelhadas do Azure](../articles/best-practices-availability-paired-regions.md).
