---
title: Backup do Azure Governamental | Microsoft Docs
description: "Este artigo oferece uma visão geral das funcionalidades do Backup do Azure disponíveis no Azure Governamental."
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
ms.assetid: a7622135-8790-4be4-a02a-7b9ac8a4996f
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/11/2016
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: 0095a95afc14de42c1160a73139a0f059cd758dd
ms.openlocfilehash: 3b3ecaef33aefc89e5a5c1cd351566d21246d0b8


---
# <a name="azure-government-backup"></a>Blog do Azure Governamental

Este artigo oferece uma visão geral do serviço Backup do Azure e lista os recursos de Backup disponíveis no Azure Governamental. O Backup do Azure é o serviço baseado no Azure que você pode usar para fazer backup (ou proteger) dos dados na nuvem da Microsoft. Proteger seus dados no Azure não significa apenas o backup na nuvem, mas a restauração dos dados na nuvem ou em uma instalação local. O Backup do Azure oferece estes benefícios principais:

- Gerenciamento de armazenamento automático
- Dimensionamento ilimitado
- Várias opções de armazenamento
- Transferência de dados ilimitada
- Criptografia de dados
- Backup consistente com o aplicativo
- Retenção de longo prazo

Se você ainda não conhece o Backup do Azure e se quiser obter uma visão geral das funcionalidades disponíveis, leia o artigo [O que é o Backup do Azure](../backup/backup-introduction-to-azure-backup.md).

> [!IMPORTANT]
> Atualmente, o Backup do Azure Governamental oferece suporte a implantações do Service Manager, também conhecido como o modelo de implantação clássico. Ainda não há suporte para implantações do Resource Manager. Veja o artigo a seguir sobre a [diferença entre os modelos de implantação clássico e do Azure Resource Manager](../resource-manager-deployment-model.md).

[!INCLUDE [learn-about-backup-deployment models](../../includes/backup-deployment-models.md)]

## <a name="azure-backup-components-available-in-azure-government-backup"></a>Componentes do Backup do Azure disponíveis no Backup do Azure Governamental

Você pode usar o Backup do Azure para proteger: arquivos, pastas, volumes, máquinas virtuais, aplicativos e cargas de trabalho. Dependendo do que deseja proteger e da localização dos seus dados, você usará um componente diferente do Azure Backup. As seções a seguir têm links para artigos na documentação pública do Backup do Azure para cada componente.

Cada artigo explica como usar o componente do Backup do Azure no portal da versão clássica.

### <a name="windows-server-and-windows-computers"></a>Computadores com o Windows Server e com o Windows

- [Fazer backup de computadores cliente com o Windows Server e com o Windows](../backup/backup-configure-vault-classic.md)
- [Restaurar computadores cliente com o Windows Server e com o Windows](../backup/backup-azure-restore-windows-server.md)
- [Gerenciar backups de computadores cliente com o Windows Server e com o Windows](../backup/backup-azure-manage-windows-server.md)
- [Usar o PowerShell para fazer backup do Windows Server](../backup/backup-client-automation-classic.md)

### <a name="virtual-machines"></a>Máquinas Virtuais

- [Preparar sua máquina virtual](../backup/backup-azure-vms-prepare.md)
- [Backup de máquinas virtuais](../backup/backup-azure-vms-first-look.md)
- [Restaurar máquinas virtuais](../backup/backup-azure-restore-vms.md)
- [Gerenciar máquinas virtuais](../backup/backup-azure-manage-vms-classic.md)
- [Usando o PowerShell para fazer backup de máquinas virtuais](../backup/backup-azure-vms-classic-automation.md)

### <a name="system-center-data-protection-manager"></a>System Center Data Protection Manager

- [Fazer backup do System Center Data Protection Manager](../backup/backup-azure-dpm-introduction-classic.md)

### <a name="azure-backup-server"></a>Servidor de Backup do Azure

- [Servidor de Backup do Azure](../backup/backup-azure-microsoft-azure-backup-classic.md)

O Servidor de Backup do Azure é um componente do Backup do Azure que funciona da mesma forma que o DPM (System Center Data Protection). O Servidor de Backup do Azure pode proteger cargas de trabalho de aplicativo como VMs do Hyper-V, o Microsoft SQL Server, o SharePoint Server, o Microsoft Exchange e os clientes para a nuvem de um único console.

## <a name="next-steps"></a>Próximas etapas

Se você não tiver certeza sobre onde começar, comece com o artigo [Fazer backup de um servidor ou de um cliente do Windows no Azure usando o modelo de implantação clássico](../backup/backup-configure-vault-classic.md). Este tutorial orienta você pelas etapas de configuração de um projeto de backup em um Windows Server ou em um computador.

Se você já sabe que poderia usar o Backup do Azure, mas deseja saber quais são os custos, veja a [página de preços do Backup](http://azure.microsoft.com/pricing/details/backup/). Há uma lista de perguntas frequentes que podem fornecer informações úteis. Observe também que há duas regiões do Azure Governamental no menu suspenso **Região**.



<!--HONumber=Nov16_HO3-->


