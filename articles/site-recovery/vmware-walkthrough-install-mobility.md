---
title: "Instalar o Serviço de mobilidade para a replicação de VMware para o Azure | Microsoft Docs"
description: "Este artigo descreve como instalar o agente do Serviço de mobilidade para replicação do VMware para o Azure com o serviço Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 3189fbcd-6b5b-4ffb-b5a9-e2080c37f9d9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: bc520bd2ea54208889861a7a3b275e3008a05d53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="step-10-install-the-mobility-service"></a>Etapa 10: instalar o Serviço de mobilidade manualmente


Este artigo descreve como definir as configurações de origem e destino ao replicar máquinas virtuais VMware locais para Azure utilizando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

O Serviço de mobilidade captura gravações de dados em um computador e as encaminha para o servidor em processo. Ele deve ser instalado em cada computador que você deseja replicar para o Azure.

Instale o Serviço de mobilidade manualmente usando uma instalação por push por meio do servidor em processo do Site Recovery quando a replicação for habilitada, ou use uma ferramenta do System Center Configuration Manager. Se você usar a instalação por push, o serviço será instalado na VM quando a replicação for habilitada.

Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="install-manually"></a>Instalar manualmente

1. Verifique os [pré-requisitos](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites) para instalação manual.
2. Siga [estas instruções](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) para a instalação manual usando o portal.
3. Se preferir instalar por meio da linha de comando, siga [estas instruções](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt).

## <a name="install-from-the-process-server"></a>Instalar por meio do servidor em processo

Se você desejar enviar a instalação do Serviço de mobilidade por push por meio do servidor em processo quando habilitar a replicação em uma VM, precisará de uma conta que pode ser usada pelo servidor em processo para acessar a VM. A conta é usada apenas para a instalação por push.

1. Você deve ter [criado uma conta](vmware-walkthrough-prepare-vmware.md) que pode ser usado para a instalação por push. Em seguida, especifique a conta que você deseja usar ao definir as configurações de origem durante a implantação do Site Recovery.
2. Em seguida, siga [estas instruções](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) se desejar enviar o Serviço de mobilidade por push para VMs que executam o Windows ou o Linux.

## <a name="other-methods"></a>Outros métodos

Saiba mais sobre como [instalar o Serviço de mobilidade usando o Configuration Manager](site-recovery-install-mobility-service-using-sccm.md) ou usando [DSC de Automação do Azure](site-recovery-automate-mobility-service-install.md).

## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 11: Habilitar a replicação](vmware-walkthrough-enable-replication.md)
