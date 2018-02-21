---
title: "Arquitetura de replicação do Azure para o Azure no Azure Site Recovery | Microsoft Docs"
description: "Este artigo fornece uma visão geral dos componentes e da arquitetura usados ao replicar VMs do Azure entre regiões do Azure com o serviço Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/07/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 409dd26cc1dfcb1c562d175a43e842b213501d03
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="azure-to-azure-replication-architecture"></a>Arquitetura de replicação do Azure para o Azure


Este artigo descreve a arquitetura usada quando você faz a replicação, o failover e a recuperação de VMs (máquinas virtuais) do Azure entre regiões do Azure, usando o serviço [Azure Site Recovery](site-recovery-overview.md).

>[!NOTE]
>Atualmente, a replicação de VM do Azure com o serviço Site Recovery está em versão prévia.



## <a name="architectural-components"></a>Componentes de arquitetura

O gráfico a seguir fornece uma exibição de alto nível de um ambiente de VM do Azure em uma região específica (neste exemplo, a localização Leste dos EUA). Em um ambiente de VM do Azure:
- Os aplicativos podem ser executados em VMs com discos distribuídos em contas de armazenamento.
- As VMs podem ser incluídas em uma ou mais sub-redes de uma rede virtual.


**Replicação do Azure para o Azure**

![ambiente do cliente](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>Processo de replicação

### <a name="step-1"></a>Etapa 1

Ao habilitar a replicação de VM do Azure, os seguintes recursos são criados automaticamente na região de destino, com base nas configurações da região de origem. Você pode personalizar as configurações de recursos de destino conforme necessário.

![Habilitar o processo de replicação, etapa 1](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**Recurso** | **Detalhes**
--- | ---
**Grupo de recursos de destino** | O grupo de recursos ao qual as VMs replicadas pertencem após o failover.
**Rede virtual de destino** | A rede virtual na qual as VMs replicadas estão localizadas após o failover. Um mapeamento de rede é criado entre redes virtuais de origem e de destino e vice-versa.
**Contas de armazenamento de cache** | Antes que as alterações nas VMs de origem sejam replicadas para uma conta de armazenamento de destino, elas são rastreadas e enviadas para a conta de armazenamento de cache no local de origem. Essa etapa garante um impacto mínimo sobre os aplicativos de produção em execução na VM.
**Contas de armazenamento de destino**  | Contas de armazenamento no local de destino para as quais os dados são replicados.
**Conjuntos de disponibilidade de destino**  | Conjuntos de disponibilidade nos quais as VMs replicadas estão localizadas após o failover.

### <a name="step-2"></a>Etapa 2

Conforme a replicação é habilitada, a extensão Serviço de mobilidade do Site Recovery é automaticamente instalada na VM:

1. A VM é registrada no Site Recovery.

2. A replicação contínua é configurada na VM. As gravações de dados nos discos de VM são continuamente transferidas para a conta de armazenamento de cache na localização de origem.

   ![Habilitar o processo de replicação, etapa 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)


 O Site Recovery nunca precisa de conectividade de entrada à VM. Somente conectividade de saída é necessária para o seguinte.

 - Endereços IP/URLs do serviço Site Recovery
 - Endereços IP/URLs da autenticação do Office 365
 - Endereços IP da conta de armazenamento de cache

Se você habilitar a consistência de várias VMs, as máquinas virtuais no grupo de replicação se comunicarão entre si pela porta 20004. Veja se não há nenhum dispositivo de firewall bloqueando a comunicação interna entre as VMs através da porta 20004.

> [!IMPORTANT]
Se você quiser que VMs do Linux façam parte de um grupo de replicação, abra manualmente o tráfego de saída na porta 20004, de acordo com as diretrizes da versão específica do Linux.

### <a name="step-3"></a>Etapa 3

Depois que a replicação contínua estiver em andamento, as gravações de disco serão transferidas imediatamente para a conta de armazenamento de cache. O Site Recovery processa os dados e envia-os para a conta de armazenamento de destino. Depois que os dados são processados, pontos de recuperação são gerados na conta de armazenamento de destino em intervalos de alguns minutos.

## <a name="failover-process"></a>Processo de failover

Quando você inicia um failover, as VMs são criadas no grupo de recursos de destino, na rede virtual de destino, na sub-rede de destino e no conjunto de disponibilidade de destino. Durante um failover, você pode usar qualquer ponto de recuperação.

![Processo de failover](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Próximas etapas

[Replicar rapidamente](azure-to-azure-quickstart.md) uma VM do Azure para uma região secundária.
