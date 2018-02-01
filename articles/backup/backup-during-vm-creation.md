---
title: "Habilitar o backup de VM do Azure durante a criação | Microsoft Docs"
description: "Veja as etapas para habilitar o backup de máquina virtual do Azure durante o processo de criação."
services: backup, virtual-machines
documentationcenter: 
author: markgalioto
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: 4041fc555fe4b61d10f84236dcae5156c6282fd3
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2018
---
# <a name="enable-backup-during-azure-virtual-machine-creation"></a>Habilitar o backup durante a criação da máquina virtual do Azure 

O serviço de Backup do Azure fornece uma interface para criar e configurar backups para a nuvem. Proteja seus dados fazendo backups, chamados de pontos de recuperação, em intervalos regulares. O Backup do Azure cria pontos de recuperação que são armazenados em cofres de recuperação com redundância geográfica. Este artigo fornece detalhes sobre como habilitar o backup durante a criação de uma VM (máquina virtual) no portal do Azure.  

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Se você não ainda tiver entrado em sua conta, entre no [portal do Azure](http://portal.azure.com).
 
## <a name="create-virtual-machine-with-backup-configured"></a>Criar uma máquina virtual com o backup configurado 

1. No canto superior esquerdo do Portal do Azure, clique em **Novo**. 

2. Selecione **Computação** e, depois, selecione uma imagem de máquina virtual.   

3. Insira as informações da máquina virtual. O nome do usuário e a senha fornecidos são usados para fazer logon na máquina virtual. Ao concluir, clique em **OK**. 

4. Selecione um tamanho para a VM.  

5. Em **Configurações > Backup**, clique em **Habilitado** para exibir as definições de configuração do backup. Você pode aceitar os valores padrão e clicar em **OK** na página de configurações para prosseguir até a página Resumo e criar a VM. Se você quiser alterar os valores, execute as etapas a seguir.  

6. Crie ou selecione um cofre dos Serviços de Recuperação, o qual armazenará os backups da máquina virtual. Se você estiver criando um cofre dos serviços de recuperação, escolha um grupo de recursos para o cofre.  

    ![Configuração do backup na página de criação da vm](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > O grupo de recursos do cofre dos Serviços de Recuperação pode ser diferente do grupo de recursos da máquina virtual.  
    > 
    > 

7. Por padrão, uma política de backup está selecionada para proteger rapidamente a VM. Uma política de backup especifica a frequência de instantâneos de backup e por quanto tempo manter as cópias de backup. Você pode aceitar a política padrão, ou pode criar ou selecionar uma política de backup diferente. Para editar a política de backup, selecione **Política de Backup** e altere os valores da política.  

8. Quando estiver satisfeito com os valores de configuração de backup, na página Configuração, clique em **OK**.  

9. Na página de resumo, após a aprovação na validação, clique em **Criar** para criar uma máquina virtual que usa as configurações de backup definidas. 

## <a name="initiate-a-backup-after-creating-the-vm"></a>Iniciar um backup após a criação da VM 

Embora a política de Backup tenha sido criada, é recomendável criar um backup inicial. Para exibir os detalhes do backup da máquina virtual após a conclusão do modelo de criação de VM, nas configurações de **Operações** no menu esquerdo, clique em **Backup**. Use isso para disparar um backup sob demanda, restaurar uma VM inteira ou todos os discos, restaurar arquivos do backup da VM ou alterar a política de backup associada à máquina virtual.  

## <a name="frequently-asked-questions"></a>Perguntas frequentes 

### <a name="which-vm-images-enable-backup-at-the-time-of-vm-creation"></a>Quais imagens de VM permitem o backup no momento da criação da VM? 

A lista a seguir de imagens de núcleo publicadas pela Microsoft tem suporte para a habilitação do backup durante a criação da VM. Para outras VMs, você pode habilitar o backup após a criação da VM. Saiba mais em [Habilitar o backup após a criação da VM](quick-backup-vm-portal.md) 

- **Windows** – Windows Server 2016 Data Center, Windows Server 2016 Data Center Core, Windows Server 2012 DataCenter, o Windows Server 2012 R2 DataCenter, Windows Server 2008 R2 SP1 
- **Ubuntu** – Ubuntu Server 1710, Ubuntu Server 1704, UUbuntu Server 1604(LTS), Ubuntu Server 1404(LTS) 
- **Redhat** – RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4 
- **SUSE** – SUSE Linux Enterprise Server 11 SP4, 12 SP2, 12 SP3 
- **Debian** – Debian 8, Debian 9 
- **CentOS** – CentOS 6.9, CentOS 7.3 
- **Oracle Linux** – Oracle Linux 6.7, 6.8, 6.9, 7.2, 7.3 
 
### <a name="is-backup-cost-included-in-the-vm-cost"></a>O custo do backup está incluído no custo da VM? 

Não, os custos de backup são separados, ou diferentes, dos custos de máquinas virtuais. Para saber mais sobre os preços de backup, veja o [site de Preços de Backup](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Quais permissões são necessárias para habilitar o backup em uma VM? 

Se você for um colaborador de máquina virtual, poderá habilitar o backup na VM. Se você estiver usando uma função personalizada, precisará das seguintes permissões para habilitar o backup na VM com êxito. 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Se o seu cofre dos Serviços de Recuperação e a máquina virtual tiverem grupos de recursos diferentes, verifique se você tem permissões de gravação no grupo de recursos do cofre dos serviços de recuperação.  

## <a name="next-steps"></a>Próximas etapas 

Agora que você protegeu sua VM, consulte os seguintes artigos para aprender sobre tarefas de gerenciamento de VM e a restauração de VMs. 

- [Gerenciar e monitorar suas máquinas virtuais](backup-azure-manage-vms.md) 
- [Restaurar máquinas virtuais](backup-azure-arm-restore-vms.md) 
