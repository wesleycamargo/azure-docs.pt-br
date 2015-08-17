<properties 
	pageTitle="SQL Server nas Máquinas Virtuais do Azure" 
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
	ms.date="07/17/2015"
	ms.author="jroth"/>

# SQL Server nas Máquinas Virtuais do Azure

## Visão geral
Você pode hospedar o [SQL Server em Máquinas Virtuais do Azure][sqlvmlanding] em diversas configurações, que variam desde um único servidor de banco de dados até uma configuração de vários computadores usando Grupos de Disponibilidade do AlwaysOn e uma Rede Virtual do Azure.

> [AZURE.NOTE]A execução do SQL Server em uma VM do Azure é uma opção para armazenar dados relacionais no Azure. Você também pode usar o serviço de Banco de Dados SQL do Azure. Para obter mais informações, consulte [Noções básicas sobre o Banco de Dados SQL e o SQL Server em VMs do Azure][sqldbcompared].
 
## Implantar uma instância do SQL Server em uma única VM
Após [criar uma máquina virtual do Azure usando o Portal do Azure][createvmportal] ou a automação, você pode instalar qualquer instância do SQL Server para a qual tenha uma licença. No entanto, você deve executar etapas adicionais para [configurar a conectividade][setupconnectivity] entre o computador do SQL Server e outros computadores cliente.
 
Você também pode instalar uma das muitas diferentes imagens de máquina virtual do SQL Server da galeria. Essas imagens incluem o licenciamento do SQL Server nos preços para a VM. Para obter mais informações e uma conectividade passo a passo, consulte [Provisionamento de uma máquina Virtual do SQL Server no Azure][provisionsqlvm].

Depois que sua máquina virtual do SQL Server estiver em execução, talvez você queira migrar bancos de dados existentes para a máquina. Para obter mais informações sobre a migração de banco de dados, consulte [Migração de um banco de dados para o SQL Server em uma VM do Azure](virtual-machines-migrate-onpremises-database.md).

## Implantar uma configuração altamente disponível com várias VMs
Você pode obter alta disponibilidade para o SQL Server usando Grupos de Disponibilidade do AlwaysOn do SQL Server. Isso envolve várias VMs do Azure em uma rede virtual. O Portal de Visualização do Azure tem um modelo que define essa configuração para você. Para obter mais informações, consulte [Oferta do AlwaysOn do SQL Server na Galeria do Portal do Microsoft Azure][sqlalwaysonportal]. Ou então, você pode [configurar manualmente um Grupo de Disponibilidade do AlwaysOn][sqlalwaysonmanual]. Para outras considerações sobre alta disponibilidade, consulte [alta disponibilidade e recuperação de desastres para SQL Server em Máquinas Virtuais do Azure][sqlhadr].

## Executar business intelligence, data warehouse e cargas de trabalho OLTP no Azure   
Você pode executar cargas de trabalho comuns do SQL Server em Máquinas Virtuais do Azure. O SQL Server tem várias imagens de máquinas virtuais otimizadas disponíveis na galeria. Elas incluem imagens para [Business Intelligence][sqlbi], [Data Warehouse][sqldw] e [OLTP][sqloltp].

## Migrar seus dados
Depois que sua máquina virtual do SQL Server estiver em execução, talvez você queira migrar bancos de dados existentes para a máquina. Há várias técnicas, mas o assistente de implantação no SQL Server Management Studio funciona bem para a maioria dos cenários. Para uma discussão sobre os cenários e um tutorial do assistente, consulte [Migração de um banco de dados para o SQL Server em uma VM do Azure](virtual-machines-migrate-onpremises-database.md).

## Backup e restauração
Para bancos de dados locais, o Azure pode agir como um data center secundário para armazenar arquivos de backup do SQL Server. O [Backup do SQL Server para URL][backupurl] armazena arquivos de backup do Azure no armazenamento de blob do Azure. O [Backup Gerenciado do SQL Server][managedbackup] permite agendar o backup e a retenção no Azure. Esses serviços podem ser usados com instâncias locais do SQL Server ou com o SQL Server em execução em VMs do Azure. As VMs do Azure também podem tirar proveito do [Backup Automatizado][autobackup] e de [Patches Automatizados][autopatching] para o SQL Server. Para obter mais informações, consulte [Tarefas de gerenciamento para o SQL Server em Máquinas Virtuais do Azure][managementtasks].

## Recursos:
[SQL Server em VMs do Azure][sqlmsdnlanding]

[Provisionando uma máquina virtual do SQL Server no Azure][provisionsqlvm]

[Introdução ao SQL Server em Máquinas Virtuais do Azure][sqlvmgetstarted]

[Migração de um banco de dados para o SQL Server em uma VM do Azure](virtual-machines-migrate-onpremises-database.md)

[Práticas recomendadas para o SQL Server em Máquinas Virtuais do Azure][sqlperf]

[Considerações de segurança para o SQL Server em Máquinas Virtuais do Azure][sqlsecurity]

  [sqlvmlanding]: http://azure.microsoft.com/services/virtual-machines/sql-server/
  [sqldbcompared]: http://azure.microsoft.com/documentation/articles/data-management-azure-sql-database-and-sql-server-iaas
  [createvmportal]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/
  [setupconnectivity]: https://msdn.microsoft.com/library/azure/dn133152.aspx
  [provisionsqlvm]: http://azure.microsoft.com/documentation/articles/virtual-machines-provision-sql-server/
  [sqlalwaysonportal]: http://go.microsoft.com/fwlink/?LinkId=526941
  [sqlalwaysonmanual]: https://msdn.microsoft.com/library/azure/dn249504.aspx
  [sqlhadr]: https://msdn.microsoft.com/library/azure/jj870962.aspx
  [sqlbi]: https://msdn.microsoft.com/library/azure/jj992719.aspx
  [sqldw]: https://msdn.microsoft.com/library/azure/dn387396.aspx
  [sqloltp]: https://msdn.microsoft.com/library/azure/eb0188e2-5569-48ff-b92c-1f6c0bf79620#about
  [migratesql]: https://msdn.microsoft.com/library/azure/dn133142.aspx
  [backupurl]: https://msdn.microsoft.com/library/dn435916(v=sql.120).aspx
  [managedbackup]: https://msdn.microsoft.com/library/dn449496.aspx
  [autobackup]: https://msdn.microsoft.com/library/azure/dn906091.aspx
  [autopatching]: https://msdn.microsoft.com/library/azure/dn961166.aspx
  [managementtasks]: https://msdn.microsoft.com/library/azure/dn906886.aspx
  [sqlmsdnlanding]: https://msdn.microsoft.com/library/azure/jj823132.aspx
  [sqlvmgetstarted]: https://msdn.microsoft.com/library/azure/dn133151.aspx
  [sqlperf]: https://msdn.microsoft.com/library/azure/dn133149.aspx
  [sqlsecurity]: https://msdn.microsoft.com/library/azure/dn133147.aspx
  [technicalarticles]: https://msdn.microsoft.com/library/azure/dn248435.aspx

<!---HONumber=August15_HO6-->