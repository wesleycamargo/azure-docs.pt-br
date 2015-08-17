<properties 
	pageTitle="Carga de trabalho de serviços de infraestrutura do Azure: farm do SharePoint na Intranet" 
	description="Saiba mais sobre o valor de um farm do SharePoint na Intranet implantado no Azure, como configurar um ambiente de desenvolvimento/teste e como implantar uma configuração de produção de alta disponibilidade." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows-sharepoint" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/21/2015" 
	ms.author="josephd"/>

# Carga de trabalho de serviços de infraestrutura do Azure: farm do SharePoint na Intranet

Configure seu primeiro ou próximo farm do SharePoint no Microsoft Azure e aproveite a facilidade de configuração e a capacidade de expandir rapidamente o farm a fim de incluir a nova capacidade ou otimização da funcionalidade principal. Muitos farms do SharePoint crescem partindo de uma configuração padrão, de alta disponibilidade, com três camadas para um farm com possivelmente uma dúzia ou mais de servidores otimizados para desempenho ou funções separadas, como pesquisa ou cache distribuído.
 
Com os recursos de Máquinas virtuais e Rede virtual dos serviços de infraestrutura do Azure, você pode implantar e executar rapidamente um farm do SharePoint conectado de forma transparente à sua rede local. Por exemplo, você pode configurar o seguinte.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)
 
Como a Rede virtual do Azure é uma extensão da sua rede local com toda a nomenclatura correta e roteamento de tráfego em vigor, os usuários a acessarão como se estivesse localizada em um datacenter local.

Essa configuração permite que você expanda com facilidade o farm do SharePoint, adicionando novas máquinas virtuais do Azure nas quais os custos de hardware e manutenção contínuos são menores do que a execução de um farm equivalente em seu datacenter.

Hospedar um farm do SharePoint na Intranet nos serviços de infraestrutura do Azure é um exemplo de um aplicativo de linha de negócios. Para uma visão geral, consulte o [Plano gráfico de arquitetura de aplicativos de linha de negócios](http://msdn.microsoft.com/dn630664)

A próxima etapa é configurar um farm do SharePoint na Intranet para desenvolvimento/teste hospedado no Azure.

## Criar um farm do SharePoint na Intranet para desenvolvimento/teste hospedado no Azure

Você tem algumas opções para a criação de um ambiente de desenvolvimento/teste para um farm do SharePoint hospedado no Azure:

- Rede virtual somente na nuvem
- Rede virtual entre locais

Você pode criar gratuitamente esses ambientes de desenvolvimento/teste com sua [assinatura do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/) ou com uma [Assinatura de avaliação do Azure](http://azure.microsoft.com/pricing/free-trial/).

### Rede virtual somente na nuvem

Uma rede virtual somente na nuvem não é conectada a uma rede local. Se você apenas quiser criar rapidamente um farm do SharePoint básico ou de alta disponibilidade, consulte [Farm do SharePoint Server](virtual-machines-sharepoint-farm-azure-preview.md). Esta é a configuração básica do farm do SharePoint.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/SPFarm_Basic.png)
 
### Rede virtual entre locais

Uma rede virtual entre locais é conectada a uma rede local com uma conexão VPN ou de Rota Expressa entre sites. Se você quiser criar um ambiente de desenvolvimento/teste que simule a configuração final e fazer experiências com o acesso ao servidor do SharePoint e executar a administração remota em uma conexão VPN, consulte [Configurar um farm de Intranet do SharePoint em uma nuvem híbrida para teste](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

![](./media/virtual-machines-workload-intranet-sharepoint-farm/CreateSPFarmHybridCloud.png)
 
A próxima etapa é criar um farm do SharePoint na Intranet de alta disponibilidade no Azure.

## Implantar um farm do SharePoint na Intranet hospedado no Azure

A linha de base, configuração representativa para um farm do SharePoint na Intranet funcional e de alta disponibilidade no Azure, tem esta aparência.

![](./media/virtual-machines-workload-intranet-sharepoint-farm/workload-spsqlao.png)
 
Ela é formada por:

- Um farm do SharePoint na Intranet com dois servidores na Web, aplicativos e níveis de banco de dados.
- Configuração de Grupos de disponibilidade do AlwaysOn do SQL Server com dois servidores SQL e um computador de nó principal em um cluster.
- O Active Directory na rede virtual com dois controladores de domínio de réplica.

Para ver essa configuração como um infográfico, consulte [SharePoint com SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788).

### Lista de materiais

Essa configuração de linha de base exige o seguinte conjunto de componentes e serviços do Azure:

- Nove máquinas virtuais
- Quatro discos de dados extras para os controladores de domínio e servidores SQL
- Três serviços de nuvem
- Quatro conjuntos de disponibilidade
- Uma rede virtual entre locais
- Uma conta de armazenamento
- Uma assinatura do Azure

### Fases de implantação

Para implantar essa configuração, use o seguinte processo:

- Fase 1: Configurar o Azure 

	Use o Portal de Gerenciamento do Azure e o PowerShell do Azure para criar uma conta de armazenamento, os serviços de nuvem e uma rede virtual entre locais. Para obter as etapas de configuração detalhadas, consulte a [Fase 1](virtual-machines-workload-intranet-sharepoint-phase1.md).

- Fase 2: Configurar os controladores de domínio

	Defina dois controladores de domínio de réplica do Active Directory e as configurações de DNS para a rede virtual. Para obter as etapas de configuração detalhadas, consulte [Fase 2](virtual-machines-workload-intranet-sharepoint-phase2.md).

- Fase 3: Configurar a infraestrutura do SQL Server.

	Prepare as máquinas virtuais do SQL Server para uso com o SharePoint e crie o cluster do SQL Server. Para obter as etapas de configuração detalhadas, consulte a [Fase 3](virtual-machines-workload-intranet-sharepoint-phase3.md).

- Fase 4: Configurar os servidores do SharePoint.

	Configure as quatro máquinas virtuais do SharePoint para um novo farm do SharePoint. Para obter a configuração detalhada, consulte a [Fase 4](virtual-machines-workload-intranet-sharepoint-phase4.md).

- Fase 5: Criar um Grupo de disponibilidade AlwaysOn

	Prepare os bancos de dados do SharePoint, crie um Grupo de Disponibilidade AlwaysOn e, em seguida, adicione bancos de dados a ele. Para obter as etapas de configuração detalhadas, consulte a [Fase 5](virtual-machines-workload-intranet-sharepoint-phase5.md).

Uma vez configurado, você pode expandir esse farm do SharePoint com a orientação encontrada em [Arquiteturas do Microsoft Azure para SharePoint 2013](http://technet.microsoft.com/library/dn635309.aspx).

## Recursos adicionais

[Implantação do SharePoint com Grupos de Disponibilidade AlwaysOn do SQL Server no Azure](../virtual-machines-workload-deploy-spsqlao-overview.md)

[Configurar um farm de intranet do SharePoint em uma nuvem híbrida para teste](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Arquiteturas do Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Infográfico do SharePoint com SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Farms do SharePoint hospedados nos serviços de infraestrutura do Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Diretrizes de implementação dos Serviços de Infraestrutura do Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=August15_HO6-->