---
title: "Examine a arquitetura para a replicação de máquinas virtuais do Azure entre regiões do Azure | Microsoft Docs"
description: "Este artigo fornece uma visão geral dos componentes e da arquitetura usados ao replicar VMs do Azure entre regiões do Azure com o serviço Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/12/2017
ms.author: raynew
ms.openlocfilehash: f471add4f4dee26482e2820fb06d010d6c0c0e88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="step-1-review-the-architecture-for-azure-vm-replication-between-azure-regions"></a>Etapa 1: Analisar a arquitetura para a replicação de VMs do Azure entre regiões do Azure


Depois de revisar as [etapas de visão geral](azure-to-azure-walkthrough-overview.md) para essa implantação, leia este artigo para entender os componentes e processos usados quando a replicação e a recuperação de máquinas virtuais (VMs) do Azure de uma região do Azure para outra, usando o [Azure Site Recovery](site-recovery-overview.md).

- Quando você concluir o artigo, deverá ter uma compreensão clara de como funciona a replicação de VMs do Azure para outra região.
- Publique eventuais comentários no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

>[!NOTE]
>Atualmente, a replicação de VM do Azure com o serviço Site Recovery está em versão prévia.



## <a name="architectural-components"></a>Componentes de arquitetura

O diagrama a seguir fornece uma exibição de alto nível de um ambiente de VM do Azure em uma região específica (neste exemplo, a localização Leste dos EUA). Em um ambiente de VM do Azure:
- Os aplicativos podem ser executados em VMs com discos distribuídos em contas de armazenamento.
- As VMs podem ser incluídas em uma ou mais sub-redes de uma rede virtual.

![ambiente do cliente](./media/azure-to-azure-walkthrough-architecture/source-environment.png)

## <a name="replication-process"></a>Processo de replicação

### <a name="step-1"></a>Etapa 1

Quando você habilita a replicação de VM do Azure no portal do Azure, os recursos mostrados na tabela e no diagrama a seguir são criados automaticamente na região de destino. Por padrão, os recursos são criados com base nas configurações da região de origem. Você pode personalizar as configurações de destino, conforme necessário. [Saiba mais](site-recovery-replicate-azure-to-azure.md).

![Habilitar o processo de replicação, etapa 1](./media/azure-to-azure-walkthrough-architecture/enable-replication-step-1.png)

**Recurso** | **Detalhes**
--- | ---
**Grupo de recursos de destino** | O grupo de recursos ao qual as VMs replicadas pertencem após o failover.
**Rede virtual de destino** | A rede virtual na qual as VMs replicadas estão localizadas após o failover. Um mapeamento de rede é criado entre redes virtuais de origem e de destino e vice-versa.
**Contas de armazenamento de cache** | Antes que as alterações nas VMs de origem sejam replicadas para a conta de armazenamento de destino, elas são acompanhadas e enviadas para a conta de armazenamento de cache no local de destino. Isso garante um impacto mínimo sobre os aplicativos de produção em execução na VM.
**Contas de armazenamento de destino**  | Contas de armazenamento no local de destino para as quais os dados são replicados.
**Conjuntos de disponibilidade de destino**  | Conjuntos de disponibilidade nos quais as VMs replicadas estão localizadas após o failover.

### <a name="step-2"></a>Etapa 2

Conforme a replicação é habilitada, a extensão Serviço de mobilidade do Site Recovery é automaticamente instalada na VM. Ocorre o seguinte:

1. A VM é registrada no Site Recovery.

2. A replicação contínua é configurada na VM. As gravações de dados nos discos de VM são continuamente transferidas para a conta de armazenamento de cache no local de origem.

   ![Habilitar o processo de replicação, etapa 2](./media/azure-to-azure-walkthrough-architecture/enable-replication-step-2.png)

  
  Observe que o Site Recovery nunca precisa de conectividade de entrada com a VM. Somente a conectividade de saída aos endereços IP/URLs do serviço Site Recovery, os endereços IP/URLs de autenticação do Office 365 e os endereços IP da conta de armazenamento de cache são necessários. 

## <a name="continuous-replication-process"></a>Processo de replicação contínua

Depois que a replicação contínua estiver funcionando, as gravações de disco serão transferidas imediatamente para a conta de armazenamento de cache. O Site Recovery processa os dados e envia-os para a conta de armazenamento de destino. Depois que os dados são processados, pontos de recuperação são gerados na conta de armazenamento de destino em intervalos de alguns minutos.

## <a name="failover-process"></a>Processo de failover

Quando você inicia um failover, as VMs são criadas no grupo de recursos de destino, na rede virtual de destino, na sub-rede de destino e no conjunto de disponibilidade de destino. Durante um failover, você pode usar qualquer ponto de recuperação.

![Processo de failover](./media/azure-to-azure-walkthrough-architecture/failover.png)

## <a name="next-steps"></a>Próximas etapas

Ir para a [Etapa 2: Verificar os pré-requisitos e as limitações](azure-to-azure-walkthrough-prerequisites.md)
