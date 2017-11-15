---
title: Perguntas frequentes do Backup de VM do Azure | Microsoft Docs
description: "Respostas para perguntas comuns sobre: como funciona o backup de VM do Azure, limitações e o que acontece quando ocorrem alterações à política"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "backup de vm do Azure, política de backup e restauração de vm do Azure"
ms.assetid: c4cd7ff6-8206-45a3-adf5-787f64dbd7e1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/18/2017
ms.author: trinadhk;pullabhk;
ms.openlocfilehash: 85d6ec20fb0447165c672ba267569994e3a96e45
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2017
---
# <a name="questions-about-the-azure-vm-backup-service"></a>Perguntas sobre o serviço de Backup do Azure
Este artigo possui respostas para perguntas comuns para ajudar você a compreender rapidamente os componentes do Backup de VM do Azure. Em algumas das respostas, há links para artigos com informações abrangentes. Você também pode postar perguntas sobre o serviço de Backup do Azure no [fórum de discussão](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Configurar o backup
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Os cofres de Serviços de Recuperação dão suporte a VMs clássicas ou VMs com base no Gerenciador de Recursos? <br/>
Os cofres dos Serviços de Recuperação dão suporte a ambos os modelos.  Você pode fazer backup de uma VM clássica (criada no portal Clássico) ou uma VM do Resource Manager (criada no portal do Azure) em um cofre dos Serviços de Recuperação.

### <a name="what-configurations-are-not-supported-by-azure-vm-backup"></a>Quais configurações não têm suporte pelo backup de VM do Azure?
Confira os [Sistemas operacionais com suporte](backup-azure-arm-vms-prepare.md#supported-operating-system-for-backup) e [Limitações de backup de VM](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm)

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>Por que não consigo ver minha VM no assistente de backup de configuração?
No Assistente de backup de configuração, o Backup do Azure lista apenas as VMs que:
  * Ainda não está protegido – Você pode verificar o status do backup de uma VM acessando a folha da VM e verificando o status de Backup no Menu de Configurações. Saiba mais sobre como [Verificar o status do backup de uma VM](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-management-blade)
  * Pertence à mesma região que a VM

## <a name="backup"></a>Backup
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>O trabalho de backup sob demanda seguirá o mesmo agendamento de retenção que os backups agendados?
Não. Você deve especificar o período de retenção para um trabalho de backup sob demanda. Por padrão, ele é retido por 30 dias quando disparado do portal. 

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Eu recentemente habilitei a Criptografia de Disco do Azure em algumas VMs. Meus backups continuarão a funcionar?
Você precisa conceder permissões para o serviço de Backup do Azure acessar o Key Vault. Você pode fornecer essas permissões no PowerShell usando as etapas mencionadas na seção *Habilitar Backup* da documentação do [PowerShell](backup-azure-vms-automation.md).

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>Eu migrei os discos de uma VM para os discos gerenciados. Meus backups continuarão a funcionar?
Sim, os backups funcionam perfeitamente e não precisa configurar novamente o backup. 

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Minha VM está desligada. Será uma sob demanda ou um trabalho de backup agendado?
Sim. Mesmo quando um computador é desligado, os backups funcionam e o ponto de recuperação é marcado como Consistente com falha. Para obter mais detalhes, consulte a seção de consistência de dados [neste artigo](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines)

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Posso cancelar um trabalho de backup em andamento?
Sim. Você poderá cancelar o trabalho de backup se ele estiver na fase "Criando instantâneo". **Você não pode cancelar um trabalho se a transferência de dados de instantâneo está em andamento**. 

## <a name="restore"></a>Restaurar
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>Como decidir entre a restauração de discos em comparação com a restauração completa da VM?
Pense na restauração completa da VM do Azure como uma opção de criação rápida. A opção de restauração de VM altera os nomes dos discos, os contêineres usados por esses discos, os endereços IP públicos e os nomes de adaptadores de rede. A alteração é necessária para manter a exclusividade de recursos criados durante a criação da VM. Mas ele não adicionará a VM ao conjunto de disponibilidade. 

Use os discos de restauração para:
  * Personalizar a VM que é criada do ponto na configuração de tempo como alterar o tamanho
  * Adicionar configurações que não estão presentes no momento do backup 
  * Controlar a convenção de nomenclatura para recursos que estão sendo criados
  * Adicionar a VM ao conjunto de disponibilidade
  * Para qualquer outra configuração que possa ser alcançada usando apenas a definição do PowerShell/um modelo declarativo
  
### <a name="can-i-use-backups-of-unmanaged-disk-vm-to-restore-after-i-upgrade-my-disks-to-managed-disks"></a>Posso usar backups de VM de disco não gerenciado para restaurar após o upgrade dos meus discos para discos gerenciados?
Sim, você pode usar os backups feitos antes de migrar os discos de não gerenciados para gerenciados. Por padrão, o trabalho de VM de restauração criará uma VM com discos não gerenciados. Você pode usar a funcionalidade de restauração de discos para restaurar os discos e usá-los para criar uma máquina virtual em discos gerenciados. 

## <a name="manage-vm-backups"></a>Gerenciar backups de VM
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>O que acontece quando altero uma política de backup nas VMs?
Quando uma nova política for aplicada em VMs, a agenda e a retenção da nova política serão seguidas. Se a retenção for estendida, os pontos de recuperação existentes serão marcados para mantê-los de acordo com a nova política. Se a retenção for reduzida, eles serão marcados para remoção no próximo trabalho de limpeza e subsequentemente excluídos. 
