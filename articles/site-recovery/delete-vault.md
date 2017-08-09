---
title: Excluir um cofre do Site Recovery
description: "Saiba como excluir um cofre do Azure Site Recovery, com base no cenário do Site Recovery."
service: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: rajani-janaki-ram
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: b95b9defa0a037f7d7d3ef36b99bc7c53c751050
ms.contentlocale: pt-br
ms.lasthandoff: 07/21/2017

---
# <a name="delete-a-site-recovery-vault"></a>Excluir um cofre do Site Recovery
Dependências podem impedir a exclusão de um cofre do Azure Site Recovery. As ações necessárias variam de acordo com o cenário do Site Recovery: VMware para o Azure, Hyper-V (com e sem o System Center Virtual Machine Manager) para o Azure e Backup do Azure. Para excluir um cofre usado no Backup do Azure, consulte [Excluir um cofre de Backup no Azure](../backup/backup-azure-delete-vault.md).

>[!Important]
>Se estiver testando o produto e não estiver preocupado com a perda de dados, use o método de exclusão forçada para remover rapidamente o cofre e todas as suas dependências.

> O comando do PowerShell exclui todo o conteúdo do cofre e não é reversível.

## <a name="use-powershell-to-force-delete-the-vault"></a>Usar o PowerShell para forçar a exclusão do cofre 

Para excluir o cofre o Azure Site Recovery, mesmo se houver itens protegidos, use estes comandos:

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault


## <a name="delete-a-site-recovery-vault"></a>Excluir um cofre do Site Recovery 
Para excluir o cofre, siga as etapas recomendadas para seu cenário.

### <a name="vmware-vms-to-azure"></a>VMs VMware para o Azure

1. Exclua todas as VMs protegidas seguindo as etapas em [Desabilitar a proteção de um VMware](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).

2. Exclua todas as políticas de replicação seguindo as etapas em [Excluir uma política de replicação](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).

3. Exclua as referências ao vCenter seguindo as etapas em [Excluir um vCenter](site-recovery-vmware-to-azure-manage-vCenter.md##delete-a-vcenter-in-azure-site-recovery).

4. Exclua o servidor de configuração seguindo as etapas em [Desativar um servidor de configuração](site-recovery-vmware-to-azure-manage-configuration-server.md##decommissioning-a-configuration-server).

5. Exclua o cofre.


### <a name="hyper-v-vms-with-virtual-machine-manager-to-azure"></a>VMs do Hyper-V (com o Virtual Machine Manager) para o Azure
1. Exclua todas as VMs protegidas seguindo as etapas em [Desabilitar a proteção de uma VM do VMware ou de um servidor físico](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).

2. Exclua todas as políticas de replicação seguindo as etapas em [Excluir uma política de replicação](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).

3.  Exclua as referências aos servidores do Virtual Machine Manager seguindo as etapas em [Cancelar o registro de um servidor do VMM conectado](site-recovery-manage-registration-and-protection.md##unregister-a-connected-vmm-server).

4.  Exclua o cofre.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>VMs do Hyper-V (sem o Virtual Machine Manager) para o Azure
1. Exclua todas as VMs protegidas seguindo as etapas em [Desabilitar a proteção de uma VM do VMware ou de um servidor físico](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).

2. Exclua todas as políticas de replicação seguindo as etapas em [Excluir uma política de replicação](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).

3. Exclua as referências aos servidores do Hyper-V seguindo as etapas em [Cancelar o registro de um host Hyper-V](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site).

4. Exclua o site do Hyper-V.

5. Exclua o cofre.

