---
title: Perguntas frequentes sobre o Backup de VM do Azure
description: 'Respostas para perguntas comuns sobre: como funciona o backup de VM do Azure, limitações e o que acontece quando ocorrem alterações à política'
services: backup
author: trinadhk
manager: shreeshd
keywords: backup de vm do Azure, política de backup e restauração de vm do Azure
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: trinadhk
ms.openlocfilehash: ff97d164ee8b2059e1b46377067041d6c381052b
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50753960"
---
# <a name="questions-about-the-azure-vm-backup-service"></a>Perguntas sobre o serviço de Backup do Azure
Este artigo possui respostas para perguntas comuns para ajudar você a compreender rapidamente os componentes do Backup de VM do Azure. Em algumas das respostas, há links para artigos com informações abrangentes. Você também pode postar perguntas sobre o serviço de Backup do Azure no [fórum de discussão](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Configurar o backup
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Os cofres de Serviços de Recuperação dão suporte a VMs clássicas ou VMs com base no Gerenciador de Recursos? <br/>
Os cofres dos Serviços de Recuperação dão suporte a ambos os modelos.  É possível fazer backup de uma VM clássica ou uma VM do Gerenciador de Recursos em um cofre dos Serviços de Recuperação.

### <a name="what-configurations-are-not-supported-by-azure-vm-backup"></a>Quais configurações não têm suporte pelo backup de VM do Azure?
Confira os [Sistemas operacionais com suporte](backup-azure-arm-vms-prepare.md#supported-operating-systems-for-backup) e [Limitações de backup de VM](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm)

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>Por que não consigo ver minha VM no assistente de backup de configuração?
No Assistente de backup de configuração, o Backup do Azure lista apenas as VMs que:
  * Ainda não está protegido – Você pode verificar o status do backup de uma VM acessando a folha da VM e verificando o status de Backup no Menu de Configurações. Saiba mais sobre como [Verificar o status do backup de uma VM](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-operations-menu)
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

### <a name="i-enabled-resource-group-lock-on-my-backed-up-managed-disk-vms-will-my-backups-continue-to-work"></a>Eu habilitei o bloqueio de Grupo de Recursos nas minhas VMs de disco gerenciado de backup. Meus backups continuarão a funcionar?
Se o usuário bloqueia o Grupo de Recursos, o serviço de Backup não é capaz de excluir os pontos de restauração mais antigos. Devido a isso, os novos backups começam a falhar, porque há um limite de um máximo de 18 pontos de restauração impostos pelo back-end. Se os seus backups estiverem falhando com um erro interno após o bloqueio do Grupo de Recursos, siga estas [etapas para remover a coleção do ponto de restauração](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal).

### <a name="does-backup-policy-take-daylight-saving-timedst-into-account"></a>A política de Backup leva em conta o horário de verão (DST)?
Não. Lembre-se que a data e hora no computador local são exibidas em sua hora local e com o horário de verão atual. Portanto o tempo configurado para backups agendados pode ser diferente da sua hora local devido ao horário de verão.

### <a name="maximum-of-how-many-data-disks-can-i-attach-to-a-vm-to-be-backed-up-by-azure-backup"></a>Qual é o máximo de discos de dados que eu posso anexar a uma VM para fazer backup pelo Backup do Azure?
O Backup do Azure agora dá suporte de backup de máquinas virtuais com até 16 discos. Para obter suporte a 16 discos, [atualize para a pilha de Backup de VM do Azure V2](backup-upgrade-to-vm-backup-stack-v2.md). Todas as VMs que habilitar a proteção a partir de 24 de setembro de 2018 terão suporte.

### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>O backup do Azure dá suporte para disco gerenciado SSD Standard?
O Backup do Azure dá suporte para [Discos Gerenciados SSD Standard](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/), um novo tipo de armazenamento durável para máquinas virtuais do Microsoft Azure. É compatível com discos gerenciados na [pilha de Backup da VM do Azure V2](backup-upgrade-to-vm-backup-stack-v2.md).

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

### <a name="what-is-the-procedure-to-restore-a-vm-to-a-restore-point-taken-before-the-conversion-from-unmanaged-to-managed-disks-was-done-for-a-vm"></a>Qual é o procedimento para restaurar uma VM para um ponto de restauração feito antes da conversão de discos não gerenciados para gerenciados ser feita em uma VM?
Neste cenário, por padrão, o trabalho de restauração de VM criará uma VM com discos não gerenciados. Para criar uma VM com discos gerenciados:
1. [Restaurar em discos não gerenciados](tutorial-restore-disk.md#restore-a-vm-disk)
2. [Converta os discos restaurados em discos gerenciados](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk)
3. [Criar uma VM com discos gerenciados](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk) <br>
Para cmdlets do PowerShell, consulte [aqui](backup-azure-vms-automation.md#restore-an-azure-vm).

### <a name="can-i-restore-the-vm-if-my-vm-is-deleted"></a>Posso restaurar a VM se minha VM for excluída?
Sim. O ciclo de vida da VM e seu item de backup correspondente são diferentes. Portanto, mesmo se você excluir a VM, você pode ir até o item de backup correspondente no cofre dos Serviços de Recuperação e disparar uma restauração usando um dos pontos de recuperação.

## <a name="manage-vm-backups"></a>Gerenciar backups de VM
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>O que acontece quando altero uma política de backup nas VMs?
Quando uma nova política for aplicada em VMs, a agenda e a retenção da nova política serão seguidas. Se a retenção for estendida, os pontos de recuperação existentes serão marcados para mantê-los de acordo com a nova política. Se a retenção for reduzida, eles serão marcados para remoção no próximo trabalho de limpeza e subsequentemente excluídos.

### <a name="how-can-i-move-a-vm-enrolled-in-azure-backup-between-resource-groups"></a>Como mover uma VM registrada no backup do Azure entre grupos de recursos?
Siga as etapas a seguir para migrar com êxito a VM com backup para o grupo de recursos de destino
1. Pare temporariamente o backup e mantenha os dados de backup
2. Mova a VM para o grupo de recursos de destino
3. Proteja-a novamente com o mesmo/novo cofre

Os usuários podem restaurar a partir dos pontos de restauração disponíveis criados antes da operação de movimentação.
