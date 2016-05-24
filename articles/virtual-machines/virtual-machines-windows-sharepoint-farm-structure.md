<properties
	pageTitle="Farms do SharePoint Server 2013 no Azure | Microsoft Azure"
	description="Encontre os artigos que descrevem como configurar um ambiente de desenvolvimento/teste ou um farm do SharePoint Server 2013 no Microsoft Azure."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="04/25/2016"
	ms.author="josephd"/>

# Farms do SharePoint hospedados nos serviços de infraestrutura do Azure

[AZURE.INCLUDE [learn-about-deployment-models-both-include](../../includes/learn-about-deployment-models-both-include.md)]

Configure seu primeiro ou próximo farm do SharePoint Server 2013 de desenvolvimento/teste ou de produção nos serviços de infraestrutura do Microsoft Azure, em que você pode tirar proveito da facilidade de configuração e da capacidade de expandir rapidamente o farm para incluir a nova capacidade ou a otimização da funcionalidade principal.

## Farm de desenvolvimento/teste básico do SharePoint

Este ambiente criado automaticamente consiste em três servidores em uma rede virtual do Azure somente na nuvem: um controlador de domínio, um servidor SQL e o servidor do SharePoint.

Veja o item [Farm de não HA do SharePoint 2013](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/) no Azure Marketplace do portal do Azure. Isso cria um farm de desenvolvimento/teste básico para um site do SharePoint voltado para a Internet. Veja [Criar farms de servidores do SharePoint](virtual-machines-windows-sharepoint-farm.md) para obter mais detalhes.

Você também pode usar um modelo do Gerenciador de Recursos do Azure. Confira [SharePoint](virtual-machines-linux-app-frameworks.md).

> [AZURE.NOTE] O item **Farm do SharePoint Server** no Azure Marketplace do portal do Azure foi removido.

## Farm de desenvolvimento/teste do SharePoint de alta disponibilidade

Este ambiente criado automaticamente consiste em nove servidores em uma rede virtual do Azure somente na nuvem: dois para controladores de domínio, três para um cluster do SQL Server, dois servidores do SharePoint da camada de aplicativos e dois servidores do SharePoint da camada da Web.

Veja o item [Farm de HA do SharePoint 2013](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/) no Azure Marketplace do portal do Azure. Isso cria um farm de desenvolvimento/teste de alta disponibilidade para um site do SharePoint voltado para a Internet. Veja [Criar farms de servidores do SharePoint](virtual-machines-windows-sharepoint-farm.md) para obter mais detalhes.

Você também pode usar um modelo do Gerenciador de Recursos do Azure. Veja [Implantar um farm de nove servidores do SharePoint](virtual-machines-windows-app-frameworks.md#deploy-a-nine-server-sharepoint-farm).

> [AZURE.NOTE] O item **Farm do SharePoint Server** no Azure Marketplace do portal do Azure foi removido.

## Farm de desenvolvimento/teste de nuvem híbrida

Com o [farm de intranet do SharePoint em um ambiente de desenvolvimento/teste de nuvem híbrida](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md), você cria uma configuração de nuvem híbrida simulada que hospeda um farm do SharePoint simples de duas camadas, o qual você pode usar para testar um farm do SharePoint de intranet hospedado no Azure de seu local na Internet.

## Farm de produção do SharePoint de intranet de alta disponibilidade

Com a implantação do [SharePoint 2013 com Grupos de Disponibilidade AlwaysOn do SQL Server no Azure](virtual-machines-windows-sp-intranet-overview.md), você cria um farm do SharePoint Server 2013 de intranet pronto para produção e de alta disponibilidade no Azure.

## Próxima etapa

- Descubra configurações adicionais do [SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) nos serviços de infraestrutura do Azure.

<!---HONumber=AcomDC_0511_2016-->