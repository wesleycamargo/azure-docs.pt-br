<properties
	pageTitle="Farm do SharePoint Server 2013 no Azure | Microsoft Azure"
	description="Conheça o valor de um farm do SharePoint Server 2013 no Azure, configure um ambiente de teste e implante uma configuração de alta disponibilidade."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="josephd"/>

# Carga de trabalho de serviços de infraestrutura do Azure: farm do SharePoint na Intranet

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

Configure seu primeiro ou próximo farm do SharePoint no Microsoft Azure e aproveite a facilidade de configuração e a capacidade de expandir rapidamente o farm a fim de incluir a nova capacidade ou otimização da funcionalidade principal. Muitos farms do SharePoint crescem partindo de uma configuração padrão, de alta disponibilidade, com três camadas para um farm com possivelmente uma dúzia ou mais de servidores otimizados para desempenho ou funções separadas, como pesquisa ou cache distribuído.

Com os recursos de máquinas virtuais e rede virtual dos Serviços de Infraestrutura do Azure, você pode implantar e executar rapidamente um farm do SharePoint conectado de forma transparente à sua rede local. Por exemplo, você pode configurar o seguinte.

![](./media/virtual-machines-windows-sp-intranet/workload-spsqlao.png)

Como a Rede virtual do Azure é uma extensão da sua rede local com toda a nomenclatura correta e roteamento de tráfego em vigor, os usuários a acessarão como se estivesse localizada em um datacenter local.

Essa configuração permite que você expanda com facilidade o farm do SharePoint, adicionando novas máquinas virtuais do Azure nas quais os custos de hardware e manutenção contínuos são menores do que a execução de um farm equivalente em seu datacenter.

Hospedar um farm do SharePoint na Intranet nos serviços de infraestrutura do Azure é um exemplo de um aplicativo de linha de negócios. Para uma visão geral, consulte o [Plano gráfico de arquitetura de aplicativos de linha de negócios](http://msdn.microsoft.com/dn630664)

A próxima etapa é configurar um farm do SharePoint na Intranet para desenvolvimento/teste hospedado no Azure.

> [AZURE.NOTE] A Microsoft lançou o SharePoint Server 2016 IT Preview. Para facilitar a instalação e teste desta visualização, você pode usar uma imagem da galeria de máquinas virtuais do Azure com o SharePoint Server 2016 IT Preview e seus pré-requisitos pré-instalados. Para obter mais informações, veja [Testar o SharePoint Server 2016 IT Preview no Azure](https://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Criar um farm do SharePoint na Intranet para desenvolvimento/teste hospedado no Azure

Você tem duas opções para a criação de um ambiente de desenvolvimento/teste para um farm do SharePoint hospedado no Azure:

- Rede virtual somente na nuvem
- Rede virtual entre locais

Você pode criar gratuitamente esses ambientes de desenvolvimento/teste com sua [assinatura do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) ou com uma [Assinatura de avaliação do Azure](https://azure.microsoft.com/pricing/free-trial/).

### Rede virtual somente na nuvem

Uma rede virtual somente na nuvem não é conectada a uma rede local. Se você apenas desejar criar rapidamente um farm do SharePoint básico ou de alta disponibilidade, veja [Farm do SharePoint Server](virtual-machines-windows-sharepoint-farm.md). O exemplo a seguir mostra a configuração básica do farm do SharePoint.

![](./media/virtual-machines-windows-sp-intranet/Non-HAFarm.png)

### Rede virtual entre locais

Uma rede virtual entre locais é conectada a uma rede local com uma conexão VPN ou de Rota Expressa entre sites. Se você quiser criar um ambiente de desenvolvimento/teste que simule a configuração final e fazer experiências com o acesso ao servidor do SharePoint e executar a administração remota em uma conexão VPN, consulte [Configurar um farm de Intranet do SharePoint em uma nuvem híbrida para teste](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

![](./media/virtual-machines-windows-sp-intranet/CreateSPFarmHybridCloud.png)

A próxima etapa é criar um farm do SharePoint na Intranet de alta disponibilidade no Azure.

## Implantar um farm do SharePoint na Intranet hospedado no Azure

A linha de base, uma configuração representante para um farm do SharePoint de intranet funcional e de alta disponibilidade no Azure, é a seguinte:

![](./media/virtual-machines-windows-sp-intranet/workload-spsqlao.png)

Ela é formada por:

- Um farm do SharePoint na Intranet com dois servidores na Web, aplicativos e níveis de banco de dados.
- Uma configuração de Grupos de Disponibilidade AlwaysOn do SQL Server com dois servidores SQL e um computador de nó principal em um cluster.
- Duas réplicas de controlador de domínio de um domínio local do Active Directory.

Para ver essa configuração como um infográfico, consulte [SharePoint com SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788).

Para implantar essa configuração, use o seguinte processo:

- Fase 1: configurar o Azure.

	Use o Azure PowerShell para criar uma conta de armazenamento, conjuntos de disponibilidade e uma rede virtual entre locais. Para obter as etapas de configuração detalhadas, consulte a [Fase 1](virtual-machines-windows-ps-sp-intranet-ph1.md).

- Fase 2: configurar os controladores de domínio.

	Defina dois controladores de domínio de réplica do Active Directory e as configurações de DNS para a rede virtual. Para obter as etapas de configuração detalhadas, consulte [Fase 2](virtual-machines-windows-ps-sp-intranet-ph2.md).

- Fase 3: Configurar a infraestrutura do SQL Server.

	Prepare as máquinas virtuais do SQL Server para uso com o SharePoint e crie o cluster do SQL Server. Para obter as etapas de configuração detalhadas, consulte a [Fase 3](virtual-machines-windows-ps-sp-intranet-ph3.md).

- Fase 4: Configurar os servidores do SharePoint.

	Configure as quatro máquinas virtuais do SharePoint para um novo farm do SharePoint. Para obter a configuração detalhada, consulte a [Fase 4](virtual-machines-windows-ps-sp-intranet-ph4.md).

- Fase 5: Criar um Grupo de disponibilidade AlwaysOn

	Prepare os bancos de dados do SharePoint, crie um Grupo de Disponibilidade AlwaysOn e, em seguida, adicione bancos de dados a ele. Para obter as etapas de configuração detalhadas, consulte a [Fase 5](virtual-machines-windows-ps-sp-intranet-ph5.md).

Depois de configurado, você poderá expandir esse farm do SharePoint com as diretrizes encontradas em [Arquiteturas do Microsoft Azure para o SharePoint 2013](http://technet.microsoft.com/library/dn635309.aspx).

## Próxima etapa

- Obtenha uma [visão geral](virtual-machines-windows-sp-intranet-overview.md) da carga de trabalho de produção antes começar a configuração.

<!---HONumber=AcomDC_0323_2016-->