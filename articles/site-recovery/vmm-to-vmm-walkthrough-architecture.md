---
title: "Revisar a arquitetura para replicação do Hyper-V para um site secundário com o Azure Site Recovery | Microsoft Docs"
description: "Este artigo fornece uma visão geral da arquitetura para replicação de VMs Hyper-V locais em um VMM do System Center secundário com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 07099161-4cc7-4f32-8eb9-2a71bbf0750b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: b78cd0d5a5395873afaddc8856004775f447e8ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="step-1-review-the-architecture-for-hyper-v-replication-to-a-secondary-site"></a>Etapa 1: revisar a arquitetura para replicação do Hyper-V em um site secundário

Este artigo descreve os componentes e processos envolvidos na replicação de VMs (máquinas virtuais) do Hyper-V locais em nuvens do System Center Virtual Machine Manager (VMM) para um site de VMM secundário usando o serviço [Azure Site Recovery](site-recovery-overview.md) no Portal do Azure.

Poste comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="architectural-components"></a>Componentes de arquitetura

Veja o que você precisa para replicar VMs Hyper-V em um site de VMM secundário.

**Componente** | **Localidade** | **Detalhes**
--- | --- | ---
**As tabelas** | Assinatura no Azure. | Crie um cofre dos Serviços de Recuperação na assinatura do Azure para orquestrar e gerenciar a replicação entre os locais do VMM.
**Servidor VMM** | Você precisa de locais primário e secundário para o VMM. | Recomendamos um servidor VMM no site primário e um no site secundário 
**Servidor Hyper-V** |  Um ou mais servidores de host do Hyper-V nas nuvens do VMM primárias e secundárias. | Os dados são replicados entre os servidores de host Hyper-V primários e secundários pela LAN ou VPN usando a autenticação Kerberos ou autenticação de certificado.  
**VMs Hyper-V** | No servidor de host do Hyper-V. | O servidor host de origem deve ter pelo menos uma VM que você deseja replicar.

## <a name="replication-process"></a>Processo de replicação

1. Configure a conta do Azure, crie um cofre dos Serviços de Recuperação e especifique o que você deseja replicar.
2. Defina as configurações de replicação de origem e de destino, que inclui a instalação do Provedor do Azure Site Recovery em servidores do VMM e o agente dos Serviços de Recuperação do Microsoft Azure em cada host Hyper-V.
3. Você cria uma política de replicação para a nuvem do VMM de origem. A política é aplicada a todas as VMs localizadas nos hosts na nuvem.
4. Você habilita a replicação de cada VMM e ocorre a replicação inicial de uma VM de acordo com as configurações que você escolher.
5. Após a replicação inicial, a replicação de alterações delta começa. As alterações acompanhadas para um item são mantidas em um arquivo .hrl.


![Local para o próprio local](./media/vmm-to-vmm-walkthrough-architecture/arch-onprem-onprem.png)

## <a name="failover-and-failback-process"></a>Processo de failover e failback

1. Você pode executar um [failover](site-recovery-failover.md) planejado ou não planejado entre sites locais. Se você executar um failover planejado, as VMs de origem serão desligadas para evitar a perda de dados.
2. Você pode fazer o failover de um único computador ou criar [planos de recuperação](site-recovery-create-recovery-plans.md) para orquestrar o failover de vários computadores.
4. Se você executar um failover não planejado para um site secundário, depois as máquinas de failover no local secundário não serão habilitadas para replicação ou proteção. Depois de executar um failover planejado, os computadores no local secundário são protegidos.
5. Em seguida, você confirma o failover para começar a acessar a carga de trabalho na VM de réplica.
6. Quando seu site primário estiver disponível novamente, você poderá iniciar a replicação inversa para replicar do site secundário para o primário. A replicação inversa coloca as máquinas virtuais em um estado protegido, mas o datacenter secundário permanece sendo o local ativo.
7. Para transformar o site primário em local ativo novamente, inicie um failover planejado do site secundário para o primário, seguido por outra replicação inversa.



## <a name="next-steps"></a>Próximas etapas

Acesse [Etapa 2: Examinar os pré-requisitos e as limitações](vmm-to-vmm-walkthrough-prerequisites.md).
