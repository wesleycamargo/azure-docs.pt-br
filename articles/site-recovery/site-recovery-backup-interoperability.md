---
title: Recuperação de Site do Azure - Backup interoperabilidade | Microsoft Docs
description: Fornece uma visão geral de como do Azure Site Recovery e Backup do Azure podem ser usados juntos.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/26/2019
ms.author: sideeksh
ms.openlocfilehash: 6658ab8c967c70ac1deaeba3d1dfeac602515591
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731874"
---
# <a name="about-site-recovery-and-backup-interoperability"></a>Sobre interoperabilidade de Backup e recuperação de Site

Este artigo fornece diretrizes para usar com êxito a recuperação de desastres de VM do Azure e o Azure IaaS VM Backup.

## <a name="azure-backup"></a>Backup do Azure

O Backup do Azure ajuda a proteger os dados para servidores locais, máquinas virtuais, cargas de trabalho virtualizadas, SQL servers, servidores do SharePoint e muito mais. O Azure Site Recovery orquestra e gerencia a recuperação de desastre para VMs do Azure, VMs locais e servidores físicos.

## <a name="azure-site-recovery"></a> do Azure Site Recovery 

É possível configurar o Backup do Azure e o Azure Site Recovery em uma VM ou um grupo de VMs. Ambos os produtos são interoperáveis. Alguns cenários onde a interoperabilidade entre o Backup e recuperação de Site do Azure se torna importante serão o seguinte:

### <a name="file-backuprestore"></a>Backup/restauração de arquivo

Se o Backup e replicação são ambos habilitados e um backup é feito, não há nenhum problema com a restauração de qualquer arquivo (s) na VM do lado do código-fonte ou o grupo de VMs. Replicação como de costume continuará sem alterações na integridade da replicação.

### <a name="disk-backuprestore"></a>Backup/restauração de disco

Se você restaurar o disco de backup, em seguida, a proteção da máquina virtual deve ser habilitado novamente.

### <a name="vm-backuprestore"></a>Backup/restauração de VM

Não há suporte para backup e restauração de uma VM ou um grupo de VMs. Para que isso funcione, a proteção precisa ser habilitado novamente.

**Cenário** | **Suporte do Azure Site Recovery?** | **Solução alternativa, se houver**  
--- | --- | ---
Backup de arquivo/pasta | Sim | Não Aplicável
Backup em disco | Não atualmente | Desabilitar e habilitar a proteção
Backup de VM | Não | Desabilitar e habilitar a proteção
