---
title: Fazer backup de aplicativos e arquivos do Azure Stack | Microsoft Docs
description: Use o Backup do Azure para fazer backup e recuperar aplicativos e arquivos no ambiente do Azure Stack.
services: backup
documentationcenter: ''
author: adiganmsft
manager: shivamg
editor: ''
keyword: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/11/2018
ms.author: adigan,markgal
ms.openlocfilehash: 19067b40e8e87c160515d13bb490e7c1604788b6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196705"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Fazer backup de arquivos e aplicativos no Azure Stack
É possível usar o Backup do Azure para proteger (ou fazer backup) de arquivos e aplicativos no Azure Stack. Para fazer backup de arquivos e aplicativos, instale o Servidor de Backup do Microsoft Azure como uma máquina virtual em execução no Azure Stack. É possível proteger qualquer aplicativo executado nos servidores do Azure Stack na mesma rede virtual. Após instalar o Servidor de Backup do Azure, adicione os discos do Azure para aumentar o armazenamento local disponível para dados de backup de curto prazo. O Servidor de Backup do Azure usa o armazenamento do Azure para retenção de longo prazo.

> [!NOTE]
> Embora o Servidor de Backup do Azure e o DPM (System Center Data Protection Manager) sejam semelhantes, o DPM não é compatível com o Azure Stack.
>


## <a name="azure-backup-server-protection-matrix"></a>Matriz de proteção do Servidor de Backup do Azure
O Servidor de Backup do Azure protege as seguintes cargas de trabalho de máquina virtual do Azure Stack.

| Fonte de dados protegida | Proteção e recuperação |
| --------------------- | ----------------------- |
| Canal Semestral do Windows Server – Datacenter/Enterprise/Standard | Volumes, arquivos, pastas |
| Windows Server 2016 – Datacenter/Enterprise/Standard | Volumes, arquivos, pastas |
| Windows Server 2012 R2 – Datacenter/Enterprise/Standard | Volumes, arquivos, pastas |
| Windows Server 2012 – Datacenter/Enterprise/Standard | Volumes, arquivos, pastas |
| Windows Server 2008 R2 – Datacenter/Enterprise/Standard | Volumes, arquivos, pastas |
| SQL Server 2016 | Banco de dados |
| SQL Server 2014 | Banco de dados |
| SQL Server 2012 SP1 | Banco de dados |
| SharePoint 2013 | Farm, banco de dados, front-end, servidor Web |
| SharePoint 2010 | Farm, banco de dados, front-end, servidor Web |


## <a name="install-azure-backup-server"></a>Instalar o Servidor de Backup do Azure
Para instalar o Servidor de Backup do Azure em uma máquina virtual do Azure Stack, confira o artigo [Preparar-se para fazer backup de cargas de trabalho usando o Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md). Antes de instalar e configurar o Servidor de Backup do Azure, lembre-se de:

### <a name="determining-size-of-virtual-machine"></a>Determinar o tamanho da máquina virtual
Para executar o Servidor de Backup do Azure em uma máquina virtual do Azure Stack, use o tamanho A2 ou maior. Para obter assistência na escolha do tamanho da máquina virtual, baixe a [calculadora de tamanho da VM do Azure Stack](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Redes virtuais nas máquinas virtuais do Azure Stack
Todas as máquinas virtuais usadas em uma carga de trabalho do Azure Stack devem pertencer à mesma Rede Virtual e Assinatura do Azure.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Armazenar dados de backup em disco local e no Azure
O Servidor de Backup do Azure armazena dados de backup em discos do Azure anexados à máquina virtual, para recuperação operacional. Depois que os discos e o espaço de armazenamento são anexados à máquina virtual, o Servidor de Backup do Azure gerenciará o armazenamento para você. A quantidade de armazenamento de dados de backup depende do número e do tamanho dos discos anexados a cada [máquina de virtual do Azure Stack](../azure-stack/user/azure-stack-storage-overview.md). Cada tamanho de VM do Azure Stack tem um número máximo de discos que podem ser anexados à máquina virtual. Por exemplo, A2 é para quatro discos. A3 é para oito discos. A4 é para 16 discos. Novamente, o tamanho e o número de discos determina o pool de armazenamento de backup total.

> [!IMPORTANT]
> **Não** retenha os dados de recuperação operacional (backup) nos discos anexados ao Servidor de Backup do Azure por mais de cinco dias.
>

Armazenar os dados de backup no Azure reduz a infraestrutura de backup no Azure Stack. Se os dados tiverem mais de cinco dias, deverão ser armazenados no Azure.

Para armazenar dados de backup no Azure, crie ou use um cofre dos Serviços de Recuperação. Ao se preparar para fazer backup da carga de trabalho do Servidor de Backup do Azure, [configure o cofre dos Serviços de Recuperação](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Uma vez configurado, cada vez que um trabalho de backup for executado, um ponto de recuperação será criado no cofre. Cada cofre dos Serviços de Recuperação contém até 9999 pontos de recuperação. De acordo com o número de pontos de recuperação criados, e por quanto tempo eles estão retidos, será possível reter dados de backup por muitos anos. Por exemplo, você pode criar pontos de recuperação mensalmente e mantê-los por cinco anos.
 
### <a name="using-sql-server"></a>Usar o SQL Server
Se você quiser usar um SQL Server remoto para o banco de dados do Servidor de Backup do Azure, selecione apenas uma VM do Azure Stack que esteja executando o SQL Server.

### <a name="azure-backup-server-vm-performance"></a>Desempenho da VM do Servidor de Backup do Azure
Se compartilhados com outras máquinas virtuais, o tamanho da conta de armazenamento e os limites IOPS podem afetar o desempenho da máquina virtual do Servidor de Backup do Azure. Por esse motivo, use uma conta de armazenamento separada para a máquina virtual do Servidor de Backup do Azure. O agente de Backup do Azure em execução no Servidor de Backup do Azure precisa de armazenamento temporário para:
    - seu próprio uso (um local de cache),
    - dados restaurados da nuvem (área de preparação local)
  
### <a name="configuring-azure-backup-temporary-disk-storage"></a>Configurar o armazenamento em disco temporário do Backup do Azure
Cada máquina virtual do Azure Stack vem com armazenamento em disco temporário, que está disponível para o usuário como volume `D:\`. A área de preparação local necessária para o Backup do Azure pode ser configurada para residir no `D:\`, e o local do cache pode ser colocado em `C:\`. Dessa forma, nenhum armazenamento precisará ser formado longe os discos de dados anexados à máquina virtual do Servidor de Backup do Azure.

### <a name="scaling-deployment"></a>Dimensionar a implantação
Se você quiser dimensionar a implantação, terá as seguintes opções:
  - Escalar verticalmente: aumentar o tamanho da máquina virtual do Servidor de Backup do Azure da série A para a série D. Além disso, aumentar o armazenamento local [de acordo com as instruções da máquina virtual do Azure Stack](../azure-stack/user/azure-stack-manage-vm-disks.md).
  - Descarregar dados: enviar dados mais antigos para o Servidor de Backup do Azure e reter somente os mais recentes no armazenamento anexado ao Servidor de Backup do Azure.
  - Escalar horizontalmente: adicionar mais Servidores de Backup do Azure para proteger as cargas de trabalho.

## <a name="see-also"></a>Consulte também
Para saber mais sobre como usar o Servidor de Backup do Azure para proteger outras cargas de trabalho, confira um dos seguintes artigos:
- [Fazer backup do farm do SharePoint](backup-azure-backup-sharepoint-mabs.md)
- [Fazer backup do SQL Server](backup-azure-sql-mabs.md)
