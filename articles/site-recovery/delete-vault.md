---
title: "Excluir cofre dos Serviços de Recuperação"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 32fcab0c9e4665d07691dc3792bdee90fb01fe66
ms.contentlocale: pt-br
ms.lasthandoff: 07/06/2017


---
# <a name="delete-recovery-services-vault"></a>Excluir cofre dos Serviços de Recuperação
As dependências impedem que você exclua o cofre dos serviços de recuperação e as ações necessárias variam de acordo com o tipo de cenário do Azure Site Recovery – VMWare para o Azure, Hyper-V (com e sem o VMM) para o Azure e Backup do Azure. Para excluir um cofre usado no Backup do Azure, confira [este](../backup/backup-azure-delete-vault.md) link.

>[!Important]
>Se estiver testando o produto e desejar excluir o cofre rapidamente e não estiver preocupado com a perda de dados, poderá usar o método de exclusão forçada para remover o cofre e todas as suas dependências.

> Observe que o comando do PowerShell excluirá todo o conteúdo do cofre e que essa não é uma etapa reversível

## <a name="force-delete-vault-using-powershell"></a>Forçar exclusão do cofre usando o PowerShell

Siga as etapas abaixo para excluir o cofre do Site Recovery mesmo se houver itens protegidos

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault



Siga as etapas recomendadas (na ordem fornecida) para seu cenário a fim de excluir o cofre

## <a name="delete-vault-used-in-site-recovery-for-protecting-vmware-vms-to-azure"></a>Exclua o Cofre, usado no Site Recovery para a proteção de VMs do VMWare no Azure:
1. Verifique se todas as VMs Protegidas foram excluídas; consulte [como](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).
2.  Verifique se todas as políticas de replicação foram excluídas; consulte [como](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).
3.  Verifique se as referências ao vCenter foram excluídas; consulte [como](site-recovery-vmware-to-azure-manage-vCenter.md##delete-a-vcenter-in-azure-site-recovery).
4. Verifique se o Servidor de configuração foi excluído; consulte [como](site-recovery-vmware-to-azure-manage-configuration-server.md##decommissioning-a-configuration-server).
5. Agora, tente excluir o cofre.


## <a name="delete-vault-used-in-site-recovery-for-protecting-hyper-v-vms-with-vmm-to-azure"></a>Exclua o Cofre, usado no Site Recovery para a proteção de VMs do Hyper-V (com o VMM) no Azure:
1.  Verifique se todas as VMs Protegidas foram excluídas; consulte [como](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).
- Verifique se todas as políticas de replicação foram excluídas; consulte [como](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).
-   Exclua as referências aos Servidores VMM; consulte [como](site-recovery-manage-registration-and-protection.md##unregister-a-connected-vmm-server)
-   Agora, tente excluir o cofre.

## <a name="delete-vault-used-in-site-recovery--for-protecting-hyper-v-vms-without-vmm-to-azure"></a>Exclua o Cofre, usado no Site Recovery para a proteção de VMs do Hyper-V (sem o VMM) no Azure:
1. Verifique se todas as VMs Protegidas foram excluídas; consulte [como](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).
- Verifique se todas as políticas de replicação foram excluídas; consulte [como](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).
-   Exclua as referências aos Hyper-V Servers; consulte [como](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site).
-   Exclua o site do Hyper-V.
-   Agora, tente excluir o cofre.

