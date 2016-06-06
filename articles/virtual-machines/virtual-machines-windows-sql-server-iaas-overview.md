<properties
	pageTitle="Visão geral do SQL Server em Máquinas Virtuais | Microsoft Azure"
	description="Introdução aos bancos de dados do SQL Server na nuvem em máquinas virtuais do Azure. Esse modelo de IaaS (infraestrutura como um serviço) permite que você execute a carga de trabalho do SQL Server no Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/25/2016"
	ms.author="jroth"/>

# Visão geral do SQL Server em máquinas virtuais do Azure

O [SQL Server em execução em máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) permite hospedar os bancos de dados do SQL Server na nuvem. Por exemplo, você pode migrar um banco de dados local para uma VM do Azure pré-configurada com o Windows Server 2012 R2 e SQL Server 2014 Enterprise edition. Mas há muitos outros cenários possíveis, como configurações de vários computadores que dão suporte a arquiteturas híbridas ou de alta disponibilidade com conectividade para redes locais.

Para uma boa visão geral, assista o vídeo a seguir:

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## Ofertas do SQL

A execução do SQL Server em uma VM do Azure é uma opção para armazenar dados relacionais no Azure. A tabela a seguir resume as diferentes ofertas.

|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Oferta do SQL | Descrição |
|---:|---|---|
|![SQL Server nas Máquinas Virtuais do Azure](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-virtual-machine.png)|[SQL Server nas Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/)|Execute o SQL Server em máquinas virtuais do Azure. Gerencie diretamente a máquina virtual e execute seu banco de dados em versões de varejo do SQL Server. |
|![Banco de Dados SQL](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-database.png)|[Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/)|Use o serviço de Banco de Dados SQL para acessar e dimensionar seu banco de dados sem precisar gerenciar a infraestrutura subjacente.|
|![SQL Data Warehouse](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-data-warehouse.png)|[SQL Data Warehouse](https://azure.microsoft.com/pt-BR/services/sql-data-warehouse/)|Use o SQL Data Warehouse para processar grandes quantidades de dados relacionais e não relacionais. Fornece funcionalidades de armazenamento de dados escalonáveis como um serviço.|
|![SQL Server Stretch Database](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-stretch-database.png)|[SQL Server Stretch Database](https://azure.microsoft.com/pt-BR/services/sql-server-stretch-database/)|Amplie dinamicamente os dados transacionais locais do Microsoft SQL Server 2016 para o Azure.|

>[AZURE.NOTE] Para obter uma comparação detalhada entre VMs do SQL e o Banco de Dados SQL, veja [Escolher uma opção do SQL Server de nuvem: Banco de Dados do SQL Azure (PaaS) ou SQL Server em VMs do Azure (IaaS)](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).

## Implantar uma VM do SQL Server

Para criar uma máquina virtual do SQL Server no Azure, você deve primeiro obter uma assinatura da Plataforma Azure. É possível adquirir uma assinatura do Azure em [Opções de Compra](https://azure.microsoft.com/pricing/purchase-options/). Para testá-la gratuitamente, visite [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

Depois de se inscrever em uma assinatura, a maneira mais fácil de implantar uma máquina virtual do SQL Server no Azure é [provisionar uma imagem da galeria de máquinas do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md). Essas imagens incluem o licenciamento do SQL Server nos preços para a VM.

É importante observar que há dois modelos para criar e gerenciar máquinas virtuais do Azure: clássico e Gerenciador de Recursos. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Para saber mais, confira [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](../resource-manager-deployment-model.md). Cada tópico deve indicar claramente seu modelo de destino, a menos que ele se aplique ao clássico e ao Resource Manager, como este artigo.

## Escolha uma imagem de VM do SQL
A tabela a seguir fornece uma matriz de imagens do SQL Server disponíveis na galeria de máquinas virtuais. Clique em qualquer um dos links na tabela com base na versão, edição e sistema operacional. Em seguida, clique no botão **Criar Máquina Virtual** na página do marketplace.

|Versão do SQL Server|Sistema operacional|Edição do SQL Server|
|---|---|---|
|**SQL Server 2016 RC**|Windows Server 2012 R2|[Avaliação](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016rc3evaluationwindowsserver2012r2/)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1enterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1standardwindowsserver2012r2/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1webwindowsserver2012r2/) e [Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1expresswindowsserver2012r2/)|
|**SQL Server 2014**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/) e [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012r2/) e [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012r2/)|
|**SQL Server 2012 SP2**|Windows Server 2012|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/) e [Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2expresswindowsserver2012/)|
|**SQL Server 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3enterprisewindowsserver2008r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3standardwindowsserver2008r2/) e [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3webwindowsserver2008r2/)|
|**SQL Server 2008 R2 SP3**|Windows Server 2012|[Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3expresswindowsserver2012/)|

>[AZURE.NOTE] O CEIP (Programa de Aperfeiçoamento da Experiência do Usuário) está habilitado por padrão. Se necessário, você pode personalizar ou desabilitar o Programa de Aperfeiçoamento da Experiência do Usuário após o provisionamento da máquina virtual. Conecte-se à VM com a área de trabalho remota e execute o utilitário **Relatório de Erro e Uso do SQL Server**.

Precisa de mais ajuda com a criação de uma nova VM do SQL? Consulte o passo a passo detalhado no [tutorial de provisionamento](virtual-machines-windows-portal-sql-server-provision.md).

Além dessas imagens pré-configuradas, também é possível [criar uma máquina virtual do Azure](virtual-machines-windows-hero-tutorial.md) sem o SQL Server pré-instalado. Você pode instalar qualquer instância do SQL Server para a qual tenha uma licença. Migre a licença do Azure para executar o SQL Server em uma Máquina Virtual do Azure usando a [Mobilidade de Licenças por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Neste cenário, você paga apenas pelos [custos](https://azure.microsoft.com/pricing/details/virtual-machines/) de computação e de armazenamento do Azure associados à máquina virtual.

Para determinar as melhores definições de configuração de máquina virtual para a sua imagem do SQL Server, examine as [Práticas recomendadas de desempenho para VMs do SQL Server](virtual-machines-windows-sql-performance.md). Para cargas de trabalho de produção, o **DS3** é o tamanho mínimo recomendado de máquina virtual para o SQL Server Enterprise edition. **DS2** é o tamanho mínimo recomendado de máquinas virtuais para cargas de trabalho de produção no Standard edition.

## Migrar seus dados

Depois que sua máquina virtual do SQL Server estiver em execução, talvez você queira migrar bancos de dados existentes para a máquina. Para obter uma lista das opções de migração e diretrizes, consulte [Migrar um banco de dados para SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md).

## Alta disponibilidade

Se você precisar de alta disponibilidade, considere configurar grupos de disponibilidade do SQL Server. Isso envolve várias VMs do Azure em uma rede virtual. O portal do Azure tem um modelo que define essa configuração para você. Para obter mais informações, consulte [Configurar um grupo de disponibilidade AlwaysOn nas máquinas virtuais do Azure Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

Se você deseja configurar manualmente o Grupo de Disponibilidade e o ouvinte associado, consulte [Configurar os Grupos de Disponibilidade AlwaysOn na VM do Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Para outras considerações sobre alta disponibilidade, consulte [alta disponibilidade e recuperação de desastres para SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## Backup e restauração
Para bancos de dados locais, o Azure pode agir como um data center secundário para armazenar arquivos de backup do SQL Server. Para obter uma visão geral das opções de backup e restauração, veja [Backup e Restauração do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

O [Backup do SQL Server para URL](https://msdn.microsoft.com/library/dn435916.aspx) armazena arquivos de backup do Azure no armazenamento de blob do Azure. O [Backup Gerenciado do SQL Server](https://msdn.microsoft.com/library/dn449496.aspx) permite agendar o backup e a retenção no Azure. Esses serviços podem ser usados com instâncias locais do SQL Server ou com o SQL Server em execução em VMs do Azure. As VMs do Azure também podem tirar proveito do [Backup Automatizado](virtual-machines-windows-classic-sql-automated-backup.md) e de [Patches Automatizados](virtual-machines-windows-classic-sql-automated-patching.md) para o SQL Server.

## Próximas etapas

Primeiro, [crie sua própria VM do SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

Em seguida, examine as [práticas recomendadas de desempenho](virtual-machines-windows-sql-performance.md) e as [técnicas de migração](virtual-machines-windows-migrate-sql.md) conforme considerar mover suas cargas de trabalho do SQL Server para VMs do Azure.

Você tem mais dúvidas sobre o SQL Server em máquinas virtuais do Azure? Primeiramente, consulte as [Perguntas frequentes sobre o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-faq.md). Adicione também suas perguntas ou comentários à parte inferior de qualquer um dos tópicos sobre VM do SQL para interagir com a Microsoft e com a comunidade.

<!---HONumber=AcomDC_0525_2016-->