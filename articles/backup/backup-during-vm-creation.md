---
title: Habilitar o backup de VM do Azure durante a criação
description: Como habilitar o backup de máquina virtual do Azure durante o processo de criação.
services: backup, virtual-machines
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: fd2beaa39f03d4f2342c94bf1cd8b8aea7440e62
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780436"
---
# <a name="enable-backup-when-you-create-an-azure-virtual-machine"></a>Habilitar o backup quando você cria uma máquina virtual do Azure

Use o serviço de Backup do Azure para fazer backup de máquinas virtuais (VMs). As VMs são backup de acordo com um agendamento especificado em uma política de backup e pontos de recuperação são criados a partir de backups. Pontos de recuperação são armazenados em cofres dos serviços de recuperação.

Este artigo fornece detalhes sobre como habilitar o backup enquanto você estiver criando uma máquina virtual (VM) no portal do Azure.  

## <a name="sign-in-to-azure"></a>Entrar no Azure

Se você não tiver entrado sua conta, entrar para o [portal do Azure](https://portal.azure.com).
 
## <a name="create-a-vm-with-backup-configured"></a>Criar uma VM com Backup configurado 

1. No canto superior esquerdo do portal do Azure, selecione **New**.

1. Selecione **computação**e, em seguida, selecione uma imagem da VM.

1. Insira as informações para a VM. O nome de usuário e senha que você fornecer serão ser usados para entrar na VM. Quando tiver terminado, selecione **Okey**. 

1. Selecione um tamanho para a VM.  

1. Sob **as configurações** > **Backup**, selecione **habilitado** para abrir as configurações de configuração de backup.

   - Para aceitar os valores padrão, selecione **Okey** sobre o **configurações** página. Em seguida, vou até a **resumo** página para criar a VM. Ignore as etapas 6 a 8.
   - Para alterar os valores de configuração de backup, siga as próximas etapas.  

1. Crie ou selecione um cofre de serviços de recuperação para armazenar os backups da VM. Se você estiver criando um cofre dos serviços de recuperação, você pode escolher um grupo de recursos para o cofre.  

    ![Definições de configuração de backup na página de criação de VM](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > O grupo de recursos para o Cofre de serviços de recuperação pode ser diferente do grupo de recursos para a VM.  

1. Por padrão, uma política de backup é selecionada para que você possa proteger a VM. Uma política de backup Especifica a frequência de instantâneos de backup e por quanto tempo manter as cópias de backup. 

   - Você pode aceitar a política padrão, ou pode criar ou selecionar uma política de backup diferente. 
   - Para editar a política de backup, selecione **política de Backup** e altere os valores.  

1. Quando você terminar de definir os valores de configuração de backup, selecione **Okey** sobre o **configurações** página.  

1. Sobre o **resumo** página, depois de passar na validação, selecione **criar** para criar uma VM que usa as configurações de backup configuradas. 

## <a name="start-a-backup-after-creating-the-vm"></a>Iniciar um backup após a criação da VM 

Mesmo que você configurou uma política de Backup para sua VM, é uma boa prática para criar um backup inicial. 

Depois que o modelo de criação da VM for concluído, vá para **operações** no menu à esquerda e selecione **Backup** para exibir os detalhes de backup para a máquina virtual. Você pode usar esta página para:

- Dispare um backup sob demanda.
- Restaure uma VM inteira ou todos os seus discos.
- Restaure arquivos de um backup de VM.
- Altere a política de backup associada à VM.  

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Usar um modelo do Resource Manager para implantar uma VM protegida

As etapas anteriores explicam como usar o portal do Azure para criar uma máquina virtual e protegê-lo em um cofre de serviços de recuperação. Para implantar uma ou mais máquinas virtuais e protegê-los em um cofre de serviços de recuperação rapidamente, consulte o modelo [implantar uma VM do Windows e habilitar o backup](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="frequently-asked-questions"></a>Perguntas frequentes 

### <a name="which-vm-images-support-backup-configuration-during-vm-creation"></a>Quais imagens VM dá suporte a configuração de backup durante a criação da VM?

As seguintes imagens de núcleo publicadas pela Microsoft há suporte para habilitar o backup durante a criação da VM. Para outras VMs, você pode habilitar o backup após a criação da VM. Para obter mais informações, consulte [habilitar o backup após a criação da VM](quick-backup-vm-portal.md).

- **Windows** -Windows Server 2016 Datacenter, Windows Server 2016 Datacenter Core, Windows Server 2012 Datacenter, Windows Server 2012 R2 Datacenter, Windows Server 2008 R2 SP1 
- **Ubuntu** -Server-Ubuntu 17.10, servidor do Ubuntu 17.04, Ubuntu Server 16.04 LTS (), Ubuntu Server 14.04 (LTS) 
- **Red Hat** - RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4 
- **SUSE** – SUSE Linux Enterprise Server 11 SP4, 12 SP2, 12 SP3 
- **Debian** – Debian 8, Debian 9 
- **CentOS** – CentOS 6.9, CentOS 7.3 
- **Oracle Linux** – Oracle Linux 6.7, 6.8, 6.9, 7.2, 7.3 
 
### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>É o custo de backup incluído no custo da VM? 

 Não. Custos de backup são separados dos custos da VM. Para obter mais informações sobre preços de backup, consulte [preços de Backup do Azure](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Quais permissões são necessárias para habilitar o backup em uma VM? 

Se você for um colaborador VM, você pode habilitar o backup na VM. Se você estiver usando uma função personalizada, você precisará das seguintes permissões para habilitar o backup na VM: 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Se seu Cofre de serviços de recuperação e a VM tiverem grupos de recursos diferentes, certifique-se de que ter permissões de gravação no grupo de recursos para o Cofre de serviços de recuperação.  

## <a name="next-steps"></a>Próximas etapas 

Agora que você protegeu sua VM, consulte os seguintes artigos para aprender a gerenciar e restaurar máquinas virtuais:

- [Gerenciar e monitorar suas máquinas virtuais](backup-azure-manage-vms.md) 
- [Restaurar máquinas virtuais](backup-azure-arm-restore-vms.md) 
