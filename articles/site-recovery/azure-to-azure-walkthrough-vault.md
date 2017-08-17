---
title: "Configurar um cofre para replicação de VM do Azure entre regiões com o Azure Site Recovery | Microsoft Docs"
description: "Resume as etapas necessárias para configurar um cofre para replicação do Azure entre regiões do Azure utilizando o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 40472189-3d80-4963-b175-8bddcbc2f61f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: e03d17992ee0b12049636e40188950bcc4a6f31e
ms.contentlocale: pt-br
ms.lasthandoff: 08/02/2017

---

# <a name="step-4-set-up-a-vault-for-azure-to-azure-replication"></a>Etapa 4: configurar um cofre para replicação de Azure para Azure

Após [planejar as redes](azure-to-azure-walkthrough-network.md), utilize este artigo para configurar um cofre para VMs (máquinas virtuais) do Azure replicando para outra região do Azure, utilizando o serviço do [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

- Ao concluir o artigo, você deverá ter um cofre dos Serviços de Recuperação configurado.
- Publique eventuais comentários no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



>[!NOTE]
>
> Atualmente, a replicação de VM do Azure está em versão prévia.




## <a name="create-a-vault"></a>Criar um cofre

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> É recomendável que você crie o cofre de serviços de recuperação no local onde você deseja suas VMs para replicar. Por exemplo, se o local de destino é centro dos EUA, crie o cofre no **centro dos EUA**.


## <a name="next-steps"></a>Próximas etapas

Acesse a [Etapa 5: habilitar a replicação](azure-to-azure-walkthrough-enable-replication.md)

