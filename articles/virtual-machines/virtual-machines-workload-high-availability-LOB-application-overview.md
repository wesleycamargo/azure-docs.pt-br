<properties 
	pageTitle="Implantar um aplicativo de linha de negócios de alta disponibilidade no Azure" 
	description="Você pode implantar um aplicativo de linha de negócios baseado na Web e de alta disponibilidade com Grupos de Disponibilidade AlwaysOn do SQL Server no Azure em cinco fases." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015" 
	ms.author="josephd"/>

# Implantar um aplicativo de linha de negócios de alta disponibilidade no Azure

Este artigo contém links para as instruções passo a passo para implantar um aplicativo de linha de negócios de alta disponibilidade, apenas para intranet e baseado na Web com Grupos de Disponibilidade AlwaysOn do SQL Server nos Serviços de Infraestrutura do Azure. O aplicativo está hospedado nesses computadores:

- Dois servidores Web
- Dois servidores de banco de dados
- Servidores de nós principais de um cluster
- Dois controladores de domínio

Essa é a configuração, com nomes de espaço reservado para cada servidor.

![](./media/virtual-machines-workload-high-availability-LOB-application-overview/workload-lobapp-phase4.png)
 
Pelo menos duas máquinas para cada função asseguram a alta disponibilidade. Todas as máquinas virtuais estão em um único local do Azure (também conhecido como região). Cada grupo de máquinas virtuais para uma função específica está em seu próprio conjunto de disponibilidade.

Implante essa configuração nas seguintes fases:

- [Fase 1: configurar o Azure](virtual-machines-workload-high-availability-LOB-application-phase1.md). Crie uma conta de armazenamento, conjuntos de disponibilidade e uma rede virtual entre os locais.
- [Fase 2: configurar os controladores de domínio](virtual-machines-workload-high-availability-LOB-application-phase2.md). Crie e configure os controladores de domínio dos Serviços de Domínio do Active Directory (AD DS).
- [Fase 3: configurar a infraestrutura do SQL Server](virtual-machines-workload-high-availability-LOB-application-phase3.md). Crie e configure as máquinas virtuais que executam o SQL Server, crie o cluster e habilite os Grupos de Disponibilidade AlwaysOn do SQL Server.
- [Fase 4: configurar servidores Web](virtual-machines-workload-high-availability-LOB-application-phase4.md). Crie e configure as duas máquinas virtuais do servidor Web.
- [Fase 5: adicionar os bancos de dados de aplicativo a um Grupo de Disponibilidade AlwaysOn do SQL Server](virtual-machines-workload-high-availability-LOB-application-phase5.md). Prepare os bancos de dados do aplicativo linha de negócios e adicione-os a um Grupo de Disponibilidade AlwaysOn do SQL Server.

Essa implantação foi projetada para acompanhar o [Plano gráfico da arquitetura de aplicativos de linha de negócios](http://msdn.microsoft.com/dn630664) e incorporar as recomendações mais recentes.

Esta é uma arquitetura prescritiva predefinida. Lembre-se:

- Se você for um implementador experiente de aplicativo de linha de negócios baseado na Web, fique à vontade para adaptar as instruções das fases 3 a 5 e criar a infraestrutura de aplicativos que melhor atenda às suas necessidades. 
- Se você já tiver uma implementação de nuvem híbrida do Azure, fique à vontade adaptar ou ignorar as instruções das fases 1 e 2 para hospedar as máquinas virtuais para o novo aplicativo na sub-rede apropriada.
- Todos os servidores estão localizados em uma só sub-rede da rede virtual do Azure. Se você quiser fornecer segurança adicional equivalente ao isolamento de sub-rede, é possível usar [Grupos de Segurança de Rede](../virtual-networks/virtual-networks-nsg.md).

Para criar um ambiente de desenvolvimento/teste ou uma verificação de conceito desta configuração, consulte [Configurar um aplicativo LOB baseado na Web em uma nuvem híbrida para teste](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md).

Para obter informações adicionais sobre a criação de cargas de trabalho de TI para o Azure, consulte [Diretrizes de implementação de serviços de infraestrutura do Azure](virtual-machines-infrastructure-services-implementation-guidelines.md).

## Próxima etapa

Para iniciar a configuração dessa carga de trabalho, vá para [Fase 1: configurar o Azure](virtual-machines-workload-high-availability-LOB-application-phase1.md).

## Recursos adicionais

[Plano gráfico da arquitetura de aplicativos de linha de negócios](http://msdn.microsoft.com/dn630664)

[Configurar um aplicativo LOB baseado na Web em uma nuvem híbrida para teste](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Diretrizes de implementação dos Serviços de Infraestrutura do Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carga de trabalho de serviços de infraestrutura do Azure: farm do SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=August15_HO8-->