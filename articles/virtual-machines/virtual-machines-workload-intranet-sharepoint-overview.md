<properties
	pageTitle="Implantar um farm do SharePoint Server 2013 | Microsoft Azure"
	description="Implante um farm do SharePoint Server 2013 de alta disponibilidade usando grupos de disponibilidade do AlwaysOn do SQL Server no Azure em cinco fases."
	documentationCenter=""
	services="virtual-machines"
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
	ms.date="07/22/2015"
	ms.author="josephd"/>

# Implantando o SharePoint com Grupos de Disponibilidade AlwaysOn do SQL Server no Azure

Este tópico contém links com instruções passo a passo para implantar um farm do SharePoint 2013 somente intranet com Grupos de Disponibilidade AlwaysOn do SQL Server com o modelo de implantação clássico. O farm contém estes computadores:

- Dois servidores Web do SharePoint
- Dois servidores de aplicativos do SharePoint
- Dois servidores de banco de dados
- Servidores de nós principais de um cluster
- Dois controladores de domínio

Essa é a configuração, com nomes de espaço reservado para cada servidor:

![](./media/virtual-machines-workload-intranet-sharepoint-overview/workload-spsqlao_05.png)

Duas máquinas para cada função asseguram a alta disponibilidade. Todas as máquinas virtuais estão em uma só região. Cada grupo de máquinas virtuais para uma função específica está em seu próprio conjunto de disponibilidade.

Implante essa configuração nas seguintes fases:

- [Fase 1: configurar o Azure](virtual-machines-workload-intranet-sharepoint-phase1.md). Crie uma conta de armazenamento, serviços de nuvem e uma rede virtual entre locais.
- [Fase 2: configurar os controladores de domínio](virtual-machines-workload-intranet-sharepoint-phase2.md). Crie e configure os controladores de domínio dos Serviços de Domínio do Active Directory (AD DS).
- [Fase 3: configurar a infraestrutura do SQL Server](virtual-machines-workload-intranet-sharepoint-phase3.md). Crie e configure as máquinas virtuais do SQL Server, prepare-as para uso com o SharePoint e crie o cluster.
- [Fase 4: configurar os servidores do SharePoint](virtual-machines-workload-intranet-sharepoint-phase4.md). Crie e configure as quatro máquinas virtuais do SharePoint.
- [Fase 5: criar o grupo de disponibilidade e adicionar os bancos de dados do SharePoint](virtual-machines-workload-intranet-sharepoint-phase5.md). Prepare os bancos de dados e crie um grupos de disponibilidade AlwaysOn do SQL Server.

Essa implantação do SharePoint com o SQL Server AlwaysOn foi projetada para acompanhar o [Infográfico do SharePoint com SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788) e incorporar as recomendações mais recentes.

Essa configuração é um guia prescritivo de cada fase cujo objetivo é desenvolver uma arquitetura predefinida para criar um farm do SharePoint de intranet funcional e altamente disponível nos serviços de infraestrutura do Azure. Para obter mais orientações de arquitetura sobre a implementação do SharePoint 2013 no Azure, consulte [Arquiteturas do Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx).

Lembre-se:

- Se você tiver experiência com o SharePoint, fique à vontade para adaptar as instruções das Fases 3 a 5 e criar o farm da maneira que melhor atenda às suas necessidades.
- Se você já tiver uma implementação de nuvem híbrida do Azure, fique à vontade para adaptar ou ignorar as instruções das Fases 1 e 2 para hospedar o novo farm do SharePoint na sub-rede apropriada.
- Todos os servidores estão localizados em uma só sub-rede da rede virtual do Azure. Se você quiser fornecer segurança adicional equivalente ao isolamento de sub-rede, é possível usar [grupos de segurança de rede](virtual-networks-nsg.md).

Para criar um ambiente de desenvolvimento/teste ou uma verificação de conceito dessa configuração, consulte [Configurar um farm de intranet do SharePoint em uma nuvem híbrida para teste](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

Para saber mais sobre o SharePoint com Grupos de Disponibilidade AlwaysOn do SQL Server, consulte [Configurar Grupos de Disponibilidade AlwaysOn do SQL Server 2012 para o SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

> [AZURE.NOTE]A Microsoft lançou o SharePoint Server 2016 IT Preview. Para facilitar a instalação e teste desta visualização, você pode usar uma imagem da galeria de máquinas virtuais do Azure com o SharePoint Server 2016 IT Preview e seus pré-requisitos pré-instalados. Para obter mais informações, veja [Testar o SharePoint Server 2016 IT Preview no Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Próxima etapa

Para iniciar a configuração dessa carga de trabalho, vá para [Fase 1: configurar o Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).


## Recursos adicionais

[Infográfico do SharePoint com SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Arquiteturas do Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Farms do SharePoint hospedados nos serviços de infraestrutura do Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Diretrizes de implementação dos Serviços de Infraestrutura do Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carga de trabalho dos Serviços de Infraestrutura do Azure: aplicativo de linha de negócios de alta disponibilidade](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=Sept15_HO2-->