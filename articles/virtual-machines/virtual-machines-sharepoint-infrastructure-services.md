<properties
	pageTitle="Farms do SharePoint hospedados nos Serviços de Infraestrutura do Azure"
	description="Encontre os principais tópicos que descrevem como configurar um farm de desenvolvimento/teste ou produção do SharePoint 2013 nos serviços de infraestrutura do Microsoft Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="07/21/2015"
	ms.author="josephd"/>

# Farms do SharePoint hospedados nos serviços de infraestrutura do Azure

Configure seu primeiro ou próximo farm do SharePoint de desenvolvimento/teste ou de produção nos serviços de infraestrutura do Microsoft Azure, em que você pode tirar proveito da facilidade de configuração e da capacidade de expandir rapidamente o farm para incluir a nova capacidade ou a otimização da funcionalidade de chave.

## Farm de desenvolvimento/teste básico do SharePoint

Para máquinas virtuais criadas no Gerenciamento de Serviços, use o recurso [Farm do SharePoint Server](virtual-machines-sharepoint-farm-azure-preview.md) no Portal de Visualização do Azure para criar um farm de desenvolvimento/teste básico para um site do SharePoint para a Internet.

O ambiente criado automaticamente consiste em três servidores para um controlador de domínio, um servidor SQL e o servidor do SharePoint em uma rede virtual do Azure somente na nuvem.

Para criar uma configuração semelhante com máquinas virtuais criadas no Gerenciador de Recursos, use um modelo. Consulte [Implantar um farm de três servidores do SharePoint](virtual-machines-workload-template-sharepoint.md#deploy-a-three-server-sharepoint-farm).

## Farm de desenvolvimento/teste do SharePoint de alta disponibilidade

Para máquinas virtuais criadas no Gerenciamento de Serviços, use o recurso [Farm do SharePoint Server](virtual-machines-sharepoint-farm-azure-preview.md) do Portal de Visualização do Azure para criar um farm do SharePoint de desenvolvimento/teste de alta disponibilidade para um site do SharePoint para a Internet.

O ambiente criado automaticamente consiste em nove servidores em uma rede virtual do Azure somente na nuvem: dois para controladores de domínio, três para um cluster do SQL Server, dois servidores do SharePoint da camada de aplicativos e dois servidores do SharePoint da camada da Web.

Para criar uma configuração semelhante com máquinas virtuais criadas no Gerenciador de Recursos, use um modelo. Consulte [Implantar um farm de nove servidores do SharePoint](virtual-machines-workload-template-sharepoint.md#deploy-a-nine-server-sharepoint-farm).

## Farm de desenvolvimento/teste de nuvem híbrida

Com o [farm de intranet do SharePoint em um ambiente de desenvolvimento/teste de nuvem híbrida](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md), você cria uma configuração de nuvem híbrida simulada que hospeda um farm do SharePoint simples de duas camadas, o qual você pode usar para testar um farm do SharePoint de intranet hospedado no Azure de seu local na Internet.

Essa configuração usa máquinas virtuais criadas no Gerenciamento de Serviços.

## Farm de produção do SharePoint de intranet de alta disponibilidade

Com a implantação do [SharePoint 2013 com grupos de disponibilidade do AlwaysOn do SQL Server no Azure](virtual-machines-workload-intranet-sharepoint-overview.md), você cria um farm do SharePoint Server 2013 de intranet pronto para produção e alta disponibilidade no Azure.

Essa configuração usa máquinas virtuais criadas no Gerenciamento de Serviços.

## Recursos adicionais

Consulte estes recursos para obter configurações e informações adicionais do SharePoint no Azure:

- [Arquiteturas do Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

- [Sites da Internet no Microsoft Azure que usam o SharePoint Server 2013](https://technet.microsoft.com/library/dn635307.aspx)

- [Recuperação de desastre do SharePoint Server 2013 no Microsoft Azure](https://technet.microsoft.com/library/dn635313.aspx)

- [Uso do Active Directory do Microsoft Azure para autenticação do SharePoint 2013](https://technet.microsoft.com/library/dn635311.aspx)

- [Implantar DirSync (Sincronização de Diretório) do Office 365 no Microsoft Azure](https://technet.microsoft.com/library/dn635310.aspx)

<!---HONumber=August15_HO6-->