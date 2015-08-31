<properties 
	pageTitle="Visão geral do SQL Server em máquinas virtuais do Azure" 
	description="Este artigo fornece uma visão geral do SQL Server hospedado em Máquinas Virtuais IaaS do Azure. Isso inclui links para conteúdo mais aprofundado." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="08/18/2015"
	ms.author="jroth"/>

# Visão geral do SQL Server em máquinas virtuais do Azure

## Visão geral
Você pode hospedar o [SQL Server em Máquinas Virtuais do Azure](http://azure.microsoft.com/services/virtual-machines/sql-server/) em diversas configurações, que variam desde um único servidor de banco de dados até uma configuração de vários computadores usando Grupos de Disponibilidade do AlwaysOn e uma Rede Virtual do Azure. Este tópico tenta apontar alguns dos melhores recursos para começar a executar o SQL Server em uma máquina virtual do Azure.

>[AZURE.NOTE]A execução do SQL Server em uma VM do Azure é uma opção para armazenar dados relacionais no Azure. Você também pode usar o serviço de Banco de Dados SQL do Azure. Para obter mais informações, consulte [Noções básicas sobre o Banco de Dados SQL e o SQL Server em VMs do Azure](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).
 
## Implantar uma instância do SQL Server em uma única VM

A maneira mais fácil de implantar uma máquina virtual do SQL Server no Azure é [provisionar uma imagem de galeria de máquina do SQL Server no Portal de Gerenciamento do Azure](virtual-machines-provision-sql-server.md). Essas imagens incluem o licenciamento do SQL Server nos preços para a VM.

No entanto, você também pode [criar uma máquina virtual do Azure](virtual-machines-windows-tutorial.md) sem o SQL Server pré-instalado. Você pode instalar qualquer instância do SQL Server para a qual tenha uma licença.

Durante esses estágios iniciais de provisionamento e configuração, as tarefas comuns incluem:

- [Analisar práticas recomendadas de desempenho do SQL Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/dn133149.aspx)
- [Analisar práticas recomendadas de segurança do SQL Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/dn133147.aspx)
- [Configurar conectividade](virtual-machines-sql-server-connectivity.md)

## Implantar uma configuração altamente disponível com várias VMs

Você pode obter alta disponibilidade para o SQL Server usando Grupos de Disponibilidade do AlwaysOn do SQL Server. Isso envolve várias VMs do Azure em uma rede virtual. O Portal de Visualização do Azure tem um modelo que define essa configuração para você. Para obter mais informações, consulte [Oferta do AlwaysOn do SQL Server na Galeria do Portal do Microsoft Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

Para configurar manualmente o grupo de disponibilidade e escuta associado, consulte os seguintes artigos:

- [Configurar os Grupos de Disponibilidade AlwaysOn no Azure (GUI)](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [Configurar um ouvinte de ILB para grupos de disponibilidade do AlwaysOn no Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [Estender Grupos de Disponibilidade AlwaysOn locais para o Azure](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md)

Para outras considerações sobre alta disponibilidade, consulte [alta disponibilidade e recuperação de desastres para SQL Server em Máquinas Virtuais do Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## Executar business intelligence, data warehouse e cargas de trabalho OLTP no Azure   
Você pode executar cargas de trabalho comuns do SQL Server em Máquinas Virtuais do Azure. O SQL Server tem várias imagens de máquinas virtuais otimizadas disponíveis na galeria. Elas incluem:

- [Business Intelligence](https://msdn.microsoft.com/library/azure/jj992719.aspx)
- [Data Warehouse](https://msdn.microsoft.com/library/azure/dn387396.aspx)
- [OLTP](https://msdn.microsoft.com/library/azure/dn387396.aspx)

## Migrar seus dados

Depois que sua máquina virtual do SQL Server estiver em execução, talvez você queira migrar bancos de dados existentes para a máquina. Há várias técnicas, mas o assistente de implantação no SQL Server Management Studio funciona bem para a maioria dos cenários. Para uma discussão sobre os cenários e um tutorial do assistente, consulte [Migrando um banco de dados para o SQL Server em uma VM do Azure](virtual-machines-migrate-onpremises-database.md).

## Backup e restauração
Para bancos de dados locais, o Azure pode agir como um data center secundário para armazenar arquivos de backup do SQL Server. Para obter uma visão geral das opções de backup e restauração, consulte [Backup e restauração do SQL Server em máquinas virtuais do Azure](virtual-machines-sql-server-backup-and-restore.md).

O [Backup do SQL Server para URL](https://msdn.microsoft.com/library/dn435916.aspx) armazena arquivos de backup do Azure no armazenamento de blob do Azure. O [Backup Gerenciado do SQL Server](https://msdn.microsoft.com/library/dn449496.aspx) permite agendar o backup e a retenção no Azure. Esses serviços podem ser usados com instâncias locais do SQL Server ou com o SQL Server em execução em VMs do Azure. As VMs do Azure também podem tirar proveito do [Backup Automatizado](virtual-machines-sql-server-automated-backup.md) e de [Patches Automatizados](virtual-machines-sql-server-automated-patching.md) para o SQL Server.

<!---HONumber=August15_HO8-->