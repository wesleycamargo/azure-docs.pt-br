---
title: Backup do Azure Governamental | Microsoft Docs
description: "Este artigo oferece uma visão geral das funcionalidades do Backup do Azure disponíveis no Azure Governamental."
services: azure-government
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: a7622135-8790-4be4-a02a-7b9ac8a4996f
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 1/5/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: fa00142a9e89c5ad2630f688ea9771a1a542c052
ms.openlocfilehash: e5f89f845302ecb890caa50dd8f86503b29f1154


---
# <a name="azure-government-backup"></a>Blog do Azure Governamental

Este artigo oferece uma visão geral do serviço Backup do Azure e lista os recursos de Backup disponíveis no Azure Governamental. O Backup do Azure é o serviço baseado no Azure que você pode usar para fazer backup (ou proteger) dos dados na nuvem da Microsoft. Proteger seus dados no Azure não significa apenas fazer o backup na nuvem, mas a restauração dos dados na nuvem ou em uma instalação local. O Backup do Azure oferece estes benefícios principais:

- Gerenciamento de armazenamento automático
- Dimensionamento ilimitado
- Várias opções de armazenamento
- Transferência de dados ilimitada
- Criptografia de dados
- Backup consistente com o aplicativo
- Retenção de longo prazo

Se você ainda não conhece o Backup do Azure e se quiser obter uma visão geral das funcionalidades disponíveis, leia o artigo [O que é o Backup do Azure](../backup/backup-introduction-to-azure-backup.md).

[!INCLUDE [learn-about-backup-deployment models](../../includes/backup-deployment-models.md)]

## <a name="azure-backup-components-available-in-azure-government-backup"></a>Componentes do Backup do Azure disponíveis no Backup do Azure Governamental

Você pode usar o Backup do Azure para proteger: arquivos, pastas, volumes, máquinas virtuais, aplicativos e cargas de trabalho. Dependendo do que deseja proteger e da localização dos seus dados, você usará um componente diferente do Azure Backup. As seções a seguir têm links para artigos na documentação pública do Backup do Azure para cada componente. As seções são divididas pelo portal clássico ou Portal do Azure. Use o Portal do Azure se estiver planejando usar as implantações do Resource Manager.

### <a name="using-windows-server-and-windows-computers-in-azure-portal"></a>Usando computadores com Windows e Windows Server no Portal do Azure

- [Fazer backup de computadores cliente com o Windows Server e com o Windows](../backup/backup-configure-vault.md)
- [Restaurar computadores cliente com o Windows Server e com o Windows](../backup/backup-azure-restore-windows-server.md)
- [Gerenciar backups de computadores cliente com o Windows Server e com o Windows](../backup/backup-azure-manage-windows-server.md)
- [Uso do PowerShell para fazer backup do Windows Server](../backup/backup-client-automation.md)

### <a name="using-windows-server-and-windows-computers-in-classic-portal"></a>Uso de computadores com o Windows e o Windows Server no portal clássico

- [Fazer backup de computadores cliente com o Windows Server e com o Windows](../backup/backup-configure-vault-classic.md)
- [Restaurar computadores cliente com o Windows Server e com o Windows](../backup/backup-azure-restore-windows-server-classic.md)
- [Gerenciar backups de computadores cliente com o Windows Server e com o Windows](../backup/backup-azure-manage-windows-server-classic.md)
- [Uso do PowerShell para fazer backup do Windows Server](../backup/backup-client-automation-classic.md)

### <a name="using-virtual-machines-in-azure-portal"></a>Uso de máquinas virtuais no Portal do Azure

- [Preparar sua máquina virtual](../backup/backup-azure-arm-vms-prepare.md)
- [Backup de máquinas virtuais](../backup/backup-azure-vms-first-look-arm.md)
- [Restaurar máquinas virtuais](../backup/backup-azure-arm-restore-vms.md)
- [Gerenciar máquinas virtuais](../backup/backup-azure-manage-vms.md)
- [Usando o PowerShell para fazer backup de máquinas virtuais](../backup/backup-azure-vms-automation.md)

### <a name="using-virtual-machines-in-classic-portal"></a>Uso de máquinas virtuais no portal clássico

- [Preparar sua máquina virtual](../backup/backup-azure-vms-prepare.md)
- [Backup de máquinas virtuais](../backup/backup-azure-vms-first-look.md)
- [Restaurar máquinas virtuais](../backup/backup-azure-restore-vms.md)
- [Gerenciar máquinas virtuais](../backup/backup-azure-manage-vms-classic.md)
- [Usando o PowerShell para fazer backup de máquinas virtuais](../backup/backup-azure-vms-classic-automation.md)

### <a name="using-system-center-data-protection-manager-in-azure-portal"></a>Uso do System Center Data Protection Manager no Portal do Azure

- [Fazer backup do System Center Data Protection Manager](../backup/backup-azure-dpm-introduction.md)

### <a name="using-system-center-data-protection-manager-in-classic-portal"></a>Uso do System Center Data Protection Manager no portal clássico

- [Fazer backup do System Center Data Protection Manager](../backup/backup-azure-dpm-introduction-classic.md)

### <a name="using-azure-backup-server-in-azure-portal"></a>Uso do Servidor de Backup do Azure no Portal do Azure

O Servidor de Backup do Azure é um componente do Backup do Azure que funciona da mesma forma que o System Center DPM (Data Protection Manager) com uma exceção — o Servidor de Backup do Azure não pode salvar dados na fita. O Servidor de Backup do Azure pode proteger cargas de trabalho de aplicativo como VMs do Hyper-V, o Microsoft SQL Server, o SharePoint Server, o Microsoft Exchange e os clientes para a nuvem de um único console. O Servidor de Backup do Azure não exige uma licença do System Center.

- [Servidor de Backup do Azure](../backup/backup-azure-microsoft-azure-backup.md)

### <a name="using-azure-backup-server-in-classic-portal"></a>Uso do Servidor de Backup do Azure no portal clássico

- [Servidor de Backup do Azure](../backup/backup-azure-microsoft-azure-backup-classic.md)


## <a name="next-steps"></a>Próximas etapas

Se você não tiver certeza sobre onde começar, comece com o artigo [Fazer backup de um servidor ou de um cliente do Windows no Azure usando o modelo de implantação clássico](../backup/backup-configure-vault-classic.md). Este tutorial orienta você pelas etapas de configuração de um projeto de backup em um Windows Server ou em um computador.

Se você já sabe que poderia usar o Backup do Azure, mas deseja saber quais são os custos, veja a [página de preços do Backup](http://azure.microsoft.com/pricing/details/backup/). Há uma lista de perguntas frequentes que podem fornecer informações úteis. Observe também que há duas regiões do Azure Governamental no menu suspenso **Região**.



<!--HONumber=Jan17_HO1-->


