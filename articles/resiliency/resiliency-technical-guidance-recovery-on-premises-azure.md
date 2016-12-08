---
title: "Orientações técnicas: recuperação do local para o Azure | Microsoft Docs"
description: "Artigo sobre a compreensão e criação de sistemas de recuperação da infraestrutura local para o Azure"
services: 
documentationcenter: na
author: adamglick
manager: saladki
editor: 
ms.assetid: 114c29c9-453f-4a54-8d0f-d77c5ad47d60
ms.service: resiliency
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: aglick
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 2890efbfec90ef0e8e205a801c6e9cf6166985f0


---
# <a name="azure-resiliency-technical-guidance-recovery-from-on-premises-to-azure"></a>Orientações técnicas de resiliência do Azure: recuperação do local para o Azure
O Azure fornece um conjunto abrangente de serviços para habilitar a extensão de um datacenter local para o Azure para fins de recuperação de desastre e alta disponibilidade:

* **Rede**: com uma rede virtual privada, você estende sua rede local com segurança à nuvem.
* **Computação**: os clientes usando o Hyper-V local podem "trocar de posição" VMs (máquinas virtuais) existentes no Azure.
* **Armazenamento**: o StorSimple estende o sistema de arquivos para o Armazenamento do Azure. O serviço de Backup do Azure fornece backup de arquivos e bancos de dados SQL no Armazenamento do Azure.
* **Replicação de banco de dados**: com Grupos de Disponibilidade do SQL Server 2014 (ou posterior), você pode implementar alta disponibilidade e recuperação de desastre para seus dados locais.

## <a name="networking"></a>Rede
Você pode usar a Rede Virtual do Azure para criar uma seção logicamente isolada no Azure e conectá-la com segurança ao datacenter local ou a um único computador cliente usando uma conexão IPsec. Com a Rede Virtual, você pode tirar proveito da infraestrutura escalonável sob demanda no Azure enquanto fornece conectividade aos dados e aplicativos locais, incluindo sistemas em execução no Windows Server, mainframes e UNIX. Para obter mais informações, confira a [documentação de rede do Azure](../virtual-network/virtual-networks-overview.md) .

## <a name="compute"></a>Computação
Se estiver usando o Hyper-V local, você poderá "trocar de posição" as máquinas virtuais existentes para o Azure e provedores de serviço que executam o Windows Server 2012 (ou posterior), sem fazer alterações na VM nem converter formatos de VM. Para obter mais informações, consulte [Sobre discos e VHDs para máquinas virtuais do Azure](../virtual-machines/virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-site-recovery"></a>Azure Site Recovery
Caso você queira o DRaaS (recuperação de desastre como um serviço), o Azure oferece o [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/). O Azure Site Recovery oferece proteção abrangente para servidores VMware, Hyper-V e servidores físicos. Com o Azure Site Recovery, você pode usar outro servidor local ou o Azure como o site de recuperação. Para obter mais informações sobre o Azure Site Recovery, confira a [documentação do Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery/).

## <a name="storage"></a>Armazenamento
Há várias opções para usar o Azure como um site de backup para dados locais.

### <a name="storsimple"></a>StorSimple
O StorSimple integra com segurança e transparência o armazenamento em nuvem aos aplicativos locais. Ele também oferece uma única solução que fornece armazenamento em nuvem e local hierárquico de alto desempenho, arquivamento dinâmico, proteção de dados baseada em nuvem e recuperação de desastre. Para obter mais informações, consulte a [página de produto do StorSimple](https://azure.microsoft.com/services/storsimple/).

### <a name="azure-backup"></a>Serviço de Backup do Azure
O Backup do Azure habilita backups de nuvem usando ferramentas familiares de backup no Windows Server 2012 (ou posterior), Windows Server 2012 Essentials (ou posterior) e System Center 2012 Data Protection Manager (ou posterior). Essas ferramentas fornecem um fluxo de trabalho para gerenciamento de backup que é independente do local de armazenamento dos backups, seja um disco local ou o Armazenamento do Azure. Após realizar o backup dos dados para a nuvem, os usuários autorizados podem fazer facilmente a recuperação dos backups para qualquer servidor.

Com os backups incrementais, somente as alterações feitas nos arquivos são transferidas para a nuvem. Isso ajuda a usar o espaço de armazenamento de modo eficiente, reduzir o consumo da largura de banda e dá suporte à recuperação pontual de várias versões dos dados. Você também pode optar por usar recursos adicionais, como políticas de retenção de dados, a compactação de dados e limitação da transferência de dados. Usar o Azure como o local de backup tem a vantagem óbvia de que os backups são automaticamente "externos". Isso elimina os requisitos adicionais de proteger a mídia de backup no local.

Para obter mais informações, confira [O que é o Backup do Azure?](../backup/backup-introduction-to-azure-backup.md) e [Configurar o Backup do Azure para dados DPM](https://technet.microsoft.com/library/jj728752.aspx).

## <a name="database"></a>Banco de dados
Você pode ter uma solução de recuperação de desastres para bancos de dados do SQL Server em um ambiente de TI híbrida, usando Grupos de disponibilidade AlwaysOn, espelhamento de banco de dados, envio de log e backup e restauração com o armazenamento de Blobs do Azure. Todas essas soluções usam o SQL Server em execução em máquinas virtuais do Azure.

Grupos de disponibilidade do AlwaysOn pode ser usados em um ambiente de TI híbrida, em que as réplicas de banco de dados existem localmente e na nuvem. Isso é mostrado no diagrama a seguir.

![Grupos de disponibilidade AlwaysOn do SQL Server em uma arquitetura de nuvem híbrida](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-3.png)

O espelhamento de banco de dados também pode abranger servidores locais e a nuvem em uma configuração baseada em certificado. O diagrama a seguir ilustra esse conceito.

![Espelhamento de banco de dados do SQL Server em uma arquitetura de nuvem híbrida](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-4.png)

O envio de log pode ser usado para sincronizar um banco de dados local com um banco de dados do SQL Server em uma máquina virtual do Azure.

![Envio de logs do SQL Server em uma arquitetura de nuvem híbrida](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-5.png)

Por fim, você pode fazer o backup de um banco de dados local diretamente para o Armazenamento de Blobs do Azure.

![Fazer backup do SQL Server para o armazenamento de Blobs do Azure em uma arquitetura de nuvem híbrida](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-6.png)

Para obter mais informações, confira [Alta disponibilidade e recuperação de desastre para o SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [Backup e restauração para o SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="checklists-for-on-premises-recovery-in-microsoft-azure"></a>Listas de verificação para recuperação local no Microsoft Azure
### <a name="networking"></a>Rede
1. Examine a seção Rede deste documento.
2. Usar a Rede Virtual para se conectar com segurança do local à nuvem.

### <a name="compute"></a>Computação
1. Examine a seção Computação deste documento.
2. Realocar VMs entre o Hyper-V e o Azure.

### <a name="storage"></a>Armazenamento
1. Examine a seção Armazenamento deste documento.
2. Tirar proveito dos serviços do StorSimple para usar o armazenamento em nuvem.
3. Usar o serviço de Backup do Azure.

### <a name="database"></a>Banco de dados
1. Examine a seção Banco de dados deste documento.
2. Considerar o uso do SQL Server em VMs do Azure como o backup.
3. Configurar Grupos de Disponibilidade AlwaysOn.
4. Configurar o espelhamento de banco de dados com base em certificado.
5. Usar o envio de logs.
6. Fazer backup de bancos de dados locais para o armazenamento de Blobs do Azure.

## <a name="next-steps"></a>Próximas etapas
Este artigo faz parte de uma série que tem como foco [Orientações técnicas de resiliência do Azure](resiliency-technical-guidance.md). O próximo artigo desta série é sobre [Recuperação de corrupção de dados ou exclusão acidental](resiliency-technical-guidance-recovery-data-corruption.md).




<!--HONumber=Nov16_HO3-->


