<properties 
	pageTitle="Aplicativo de linha de negócios no Azure | Microsoft Azure" 
	description="Conheça o valor de um aplicativo de linha de negócios no Azure, configure um ambiente de teste e implante uma configuração de alta disponibilidade." 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2016" 
	ms.author="josephd"/>

# Carga de trabalho dos Serviços de Infraestrutura do Azure: aplicativo de linha de negócios de alta disponibilidade

Configure seu primeiro ou próximo aplicativo de linha de negócios baseado na Web e somente para intranet no Microsoft Azure e aproveite a facilidade de configuração e a capacidade de expandir rapidamente o aplicativo para incluir novas capacidades.
 
Com os recursos de Máquinas Virtuais e Rede Virtual dos serviços de infraestrutura do Azure, você pode rapidamente implantar e executar um aplicativo de linha de negócios que seja acessível pelos usuários de sua organização. Por exemplo, você pode configurar o seguinte.

![](./media/virtual-machines-windows-lob/workload-lobapp-phase4.png)
 
Como a Rede Virtual do Azure é uma extensão da sua rede local com toda a nomenclatura correta e o roteamento de tráfego em vigor, os usuários acessarão os servidores do aplicativo de linha de negócios como se estivesse localizado em um datacenter local.

Essa configuração permite que você expanda com facilidade a capacidade do aplicativo adicionando novas máquinas virtuais do Azure, nas quais os custos de hardware e manutenção contínua são menores do que a execução de um farm equivalente em seu datacenter.

Sua próxima etapa é configurar um aplicativo de linha de negócios de desenvolvimento/teste hospedado no Azure.

## Criar um aplicativo de linha de negócios de desenvolvimento/teste hospedado no Azure

Uma rede virtual entre locais é conectada a uma rede local com uma conexão VPN ou de Rota Expressa entre sites. Se você quiser criar um ambiente de desenvolvimento/teste que simule a configuração final e fazer experiências com o acesso ao aplicativo e executar a administração remota em uma conexão VPN, consulte [Configurar um aplicativo LOB baseado na Web em uma nuvem híbrida para teste](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md).

![](./media/virtual-machines-windows-lob/CreateLOBAppHybridCloud_3.png)
 
É possível criar este ambiente de teste/desenvolvimento gratuitamente com sua [assinatura do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) ou com uma assinatura do Azure.

Sua próxima etapa é criar um aplicativo de linha de negócios de alta disponibilidade no Azure.

## Implantar um aplicativo de linha de negócios hospedado no Azure

A linha de base, uma configuração representante para um aplicativo de linha de negócios de alta disponibilidade no Azure, é assim.

![](./media/virtual-machines-windows-lob/workload-lobapp-phase4.png)
 
Ela é formada por:

- Um aplicativo de linha de negócios somente para intranet com dois servidores nas camadas Web e de banco de dados.
- Uma configuração do SQL Server AlwaysOn com duas máquinas virtuais que executam o SQL Server e um computador de nó principal em um cluster.
- Serviços de Domínio do Active Directory na rede virtual com dois controladores de domínio de réplica.

Para obter uma visão geral do aplicativo de linha de negócios, consulte o [Plano gráfico da arquitetura de aplicativos de linha de negócios](http://msdn.microsoft.com/dn630664).

Para implantar essa configuração, use o seguinte processo:

- Fase 1: Configurar o Azure 

	Use o Azure PowerShell para criar contas de armazenamento, conjuntos de disponibilidade e uma rede virtual entre os locais. Para obter as etapas de configuração detalhadas, consulte a [Fase 1](virtual-machines-windows-ps-lob-ph1.md).

- Fase 2: Configurar os controladores de domínio

	Defina dois controladores de domínio de réplica do Active Directory e as configurações de DNS para a rede virtual. Para obter as etapas de configuração detalhadas, consulte [Fase 2](virtual-machines-windows-ps-lob-ph2.md).

- Fase 3: Configurar a infraestrutura do SQL Server.

	Crie as máquinas virtuais que executam o SQL Server e o cluster. Para obter as etapas de configuração detalhadas, consulte a [Fase 3](virtual-machines-windows-ps-lob-ph3.md).

- Fase 4: configurar os servidores Web.

	Crie máquinas virtuais do servidor Web e adicione a seu aplicativo de linha de negócios a ele. Para obter a configuração detalhada, consulte a [Fase 4](virtual-machines-windows-ps-lob-ph4.md).

- Fase 5: Configurar um grupo de disponibilidade AlwaysOn do SQL Server.

	Prepare os bancos de dados do aplicativo, crie um grupo de disponibilidade AlwaysOn do SQL Server e, em seguida, adicione os bancos de dados do aplicativo a ele. Para obter as etapas de configuração detalhadas, consulte a [Fase 5](virtual-machines-windows-ps-lob-ph5.md).

Uma vez configurado, você pode expandir facilmente esse aplicativo de linha de negócios adicionando ao cluster mais servidores Web ou máquinas virtuais que executam o SQL Server.

## Próxima etapa

- Obtenha uma [visão geral](virtual-machines-windows-lob-overview.md) da carga de trabalho de produção antes começar a configuração.

<!---HONumber=AcomDC_0601_2016-->