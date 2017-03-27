---
title: "Cenários de recuperação de desastre para VMs do Azure | Microsoft Docs"
description: "Saiba o que fazer caso uma interrupção de serviço do Azure afete as máquinas virtuais do Azure."
services: virtual-machines
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: kmouss;aglick
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b1fb88277f6da86c023cd63af0d7c2c5681f8be0
ms.lasthandoff: 03/21/2017


---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>O que fazer caso uma interrupção de serviço do Azure afete as VMs do Azure
Na Microsoft, trabalhamos muito para garantir que nossos serviços estejam sempre disponíveis quando você precisar deles. Às vezes, forças além do nosso controle nos afetam de formas que causam interrupções de serviço não planejadas.

A Microsoft fornece um SLA (Contrato de Nível de Serviço) para seus serviços como um compromisso com o tempo de atividade e a conectividade. O SLA para serviços individuais do Azure pode ser encontrado em [Contratos de Nível de Serviço do Azure](https://azure.microsoft.com/support/legal/sla/).

O Azure já tem muitos recursos internos de plataforma que oferecem suporte a aplicativos altamente disponíveis. Para saber mais sobre esses serviços, leia [Recuperação de desastres e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Este artigo aborda um cenário real de recuperação de desastre, quando uma região inteira sofre uma interrupção devido a um grande desastre natural ou a uma interrupção do serviço generalizada. Essas ocorrências são raras, mas você deve se preparar para a possibilidade de uma interrupção em toda uma região. Se toda a região sofrer uma interrupção de serviço, as cópias localmente redundantes dos dados poderão estar temporariamente indisponíveis. Se você tiver habilitado a replicação geográfica, haverá três cópias adicionais dos blobs de Armazenamento do Azure e tabelas armazenadas em uma região diferente. No caso de uma interrupção regional completa ou de um desastre no qual a região primária não seja recuperável, o Azure remapeia todas as entradas de DNS para a região geográfica replicada.

> [!NOTE]
> Lembre-se de que você não tem nenhum controle sobre esse processo e de que ele ocorrerá apenas em caso de interrupção do serviço em toda uma região. Por isso, você também deve contar com outras estratégias de backup específicas ao aplicativo para chegar ao nível mais alto de disponibilidade. Para obter mais informações, consulte a seção sobre [Estratégias de Dados para Recuperação de Desastre](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications#data-strategies-for-disaster-recovery).
>
>

Para ajudar você a lidar com essas ocorrências raras, fornecemos as seguintes diretrizes para a máquina virtual do Azure no caso de uma interrupção de serviço de toda a região em que seu aplicativo da máquina virtual do Azure é implantado.

## <a name="option-1-wait-for-recovery"></a>Opção 1: aguardar a recuperação
Nesse caso, nenhuma ação sua é necessária. Saiba que estamos trabalhando cuidadosamente para restaurar a disponibilidade do serviço. Você pode ver o status atual do serviço no nosso [Painel de integridade do serviço Azure](https://azure.microsoft.com/status/).

> [!NOTE]
> Essa será a melhor opção se você não tiver instalado o Azure Site Recovery, o backup de máquina virtual, o armazenamento com redundância geográfica de acesso de leitura ou o armazenamento com redundância geográfica antes da interrupção. Se você configurou o armazenamento com redundância geográfica de acesso de leitura ou o armazenamento com redundância geográfica para a conta de armazenamento na qual os VHDs (discos rígidos virtuais) de sua VM estão armazenados, você poderá recuperar o VHD da imagem base e tentar provisionar uma nova VM por meio dele. Essa não é uma opção preferencial, pois não há nenhuma garantia de sincronização de dados, a menos que o backup da VM do Azure ou o Azure Site Recovery seja usado. Consequentemente, não há garantia de funcionamento dessa opção.
>
>

Para clientes que desejam acesso imediato às máquinas virtuais, as duas opções abaixo estão disponíveis.  

> [!NOTE]
> Lembre-se de que as duas opções abaixo têm a possibilidade de perda parcial de dados.     
>
>

## <a name="option-2-restore-a-vm-from-a-backup"></a>Opção 2: Restaurar uma VM de um backup
Para os clientes que configuraram um backup de VM, você pode restaurar a VM de seu ponto de backup e recuperação.

Para restaurar uma nova VM a partir do Backup do Azure, confira [Restauração de máquinas virtuais no Azure](../backup/backup-azure-restore-vms.md).

Para ajudar a planejar sua infraestrutura de backup de máquinas virtuais do Azure, confira [Planejar sua infraestrutura de backup de VM no Azure](../backup/backup-azure-vms-introduction.md).

## <a name="option-3-initiate-a-failover-by-using-azure-site-recovery"></a>Opção 3: Iniciar um failover usando o Azure Site Recovery
Se tiver configurado o Azure Site Recovery para trabalhar com suas máquinas virtuais do Azure afetadas, você poderá restaurar as VMs de suas réplicas. Essas réplicas podem residir no Azure ou no local. Nesse caso, você pode criar novas VMs a partir de sua réplica existente. Para restaurar suas VMs de uma réplica do Azure Site Recovery, confira [Migrar máquinas virtuais IaaS do Azure entre regiões do Azure com o Azure Site Recovery](../site-recovery/site-recovery-migrate-azure-to-azure.md).

> [!NOTE]
> Embora o sistema operacional de máquina virtual do Azure e os discos de dados sejam replicados em um VHD secundário, se eles estiverem em uma conta de armazenamento com redundância geográfica ou de armazenamento com redundância geográfica de acesso de leitura, cada VHD será replicado independentemente. Esse nível de replicação não garante a consistência entre os VHDs replicados. Se o seu aplicativo e/ou bancos de dados que usam esses discos de dados tiverem dependências entre si, não haverá garantia de que todos os VHDs serão replicados como um único instantâneo. Também não há garantia de que a réplica de VHD do armazenamento com redundância geográfica ou do armazenamento com redundância geográfica de acesso de leitura resultará em um instantâneo consistente de aplicativo para inicializar a VM.
>
>

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como implementar uma estratégia de alta disponibilidade e recuperação de desastres, consulte [Recuperação de desastres e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Para desenvolver uma compreensão técnica detalhada dos recursos de uma plataforma de nuvem, consulte [Orientação técnica sobre a resiliência do Azure](../resiliency/resiliency-technical-guidance.md).

Para saber como fazer backup de VMs, confira [Fazer backup de máquinas virtuais do Azure](../backup/backup-azure-vms.md).

Saiba como usar o Azure Site Recovery para orquestrar e automatizar a proteção de suas máquinas físicas (e virtuais) com Windows e Linux que serão executadas em VMs Hyper-V e VMWare, confira [Azure Site Recovery](https://azure.microsoft.com/documentation/learning-paths/site-recovery/).

Se as instruções não estiverem claras ou se você desejar que a Microsoft faça as operações em seu nome, entre em contato com o [Atendimento ao Cliente](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

