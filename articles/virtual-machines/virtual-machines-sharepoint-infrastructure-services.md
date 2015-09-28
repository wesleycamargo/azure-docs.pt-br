<properties
	pageTitle="Farms do SharePoint Server 2013 no Azure | Microsoft Azure"
	description="Encontre os artigos que descrevem como configurar um ambiente de desenvolvimento/teste ou um farm do SharePoint Server 2013 no Microsoft Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="09/08/2015"
	ms.author="josephd"/>

# Farms do SharePoint hospedados nos serviços de infraestrutura do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo aborda a criação de recursos com o modelo de implantação do Gerenciador de Recursos ou com o modelo de implantação clássico.

Configure seu primeiro ou próximo farm do SharePoint de desenvolvimento/teste ou de produção nos serviços de infraestrutura do Microsoft Azure, em que você pode tirar proveito da facilidade de configuração e da capacidade de expandir rapidamente o farm para incluir a nova capacidade ou a otimização da funcionalidade de chave.

> [AZURE.NOTE]A Microsoft lançou o SharePoint Server 2016 IT Preview. Para facilitar a instalação e teste desta visualização, você pode usar uma imagem da galeria de máquinas virtuais do Azure com o SharePoint Server 2016 IT Preview e seus pré-requisitos pré-instalados. Para saber mais, consulte [Testar o SharePoint Server 2016 IT Preview no Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Farm de desenvolvimento/teste básico do SharePoint

Para máquinas virtuais criadas com o modelo de implantação clássico, use o recurso [Farm do SharePoint Server](virtual-machines-sharepoint-farm-azure-preview.md) no portal de visualização do Azure para criar um farm de desenvolvimento/teste básico para um site do SharePoint para a Internet.

O ambiente criado automaticamente consiste em três servidores para um controlador de domínio, um servidor SQL e o servidor do SharePoint em uma rede virtual do Azure somente na nuvem.

Para criar uma configuração semelhante com o modelo de implantação do Gerenciador de Recursos, use um modelo. Consulte [Implantar um farm de três servidores do SharePoint](virtual-machines-workload-template-sharepoint.md#deploy-a-three-server-sharepoint-farm).

## Farm de desenvolvimento/teste do SharePoint de alta disponibilidade

Para máquinas virtuais clássicas, use o recurso [Farm do SharePoint Server](virtual-machines-sharepoint-farm-azure-preview.md) do portal de visualização do Azure para criar um farm do SharePoint de desenvolvimento/teste de alta disponibilidade para um site do SharePoint para a Internet.

O ambiente criado automaticamente consiste em nove servidores em uma rede virtual do Azure somente na nuvem: dois para controladores de domínio, três para um cluster do SQL Server, dois servidores do SharePoint da camada de aplicativos e dois servidores do SharePoint da camada da Web.

Para criar uma configuração semelhante com máquinas virtuais do Gerenciador de Recursos, use um modelo. Consulte [Implantar um farm de nove servidores do SharePoint](virtual-machines-workload-template-sharepoint.md#deploy-a-nine-server-sharepoint-farm).

## Farm de desenvolvimento/teste de nuvem híbrida

Com o [farm de intranet do SharePoint em um ambiente de desenvolvimento/teste de nuvem híbrida](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md), você cria uma configuração de nuvem híbrida simulada que hospeda um farm do SharePoint simples de duas camadas, o qual você pode usar para testar um farm do SharePoint de intranet hospedado no Azure de seu local na Internet.

Esta configuração usa as máquinas virtuais clássicas.

## Farm de produção do SharePoint de intranet de alta disponibilidade

Com a implantação do [SharePoint 2013 com Grupos de Disponibilidade AlwaysOn do SQL Server no Azure](virtual-machines-workload-intranet-sharepoint-overview.md), você cria um farm do SharePoint Server 2013 de intranet pronto para produção e de alta disponibilidade no Azure.

Esta configuração usa as máquinas virtuais clássicas.

## Recursos adicionais

Consulte estes recursos para obter configurações e informações adicionais do SharePoint no Azure:

- [Arquiteturas do Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

- [Sites da Internet no Microsoft Azure que usam o SharePoint Server 2013](https://technet.microsoft.com/library/dn635307.aspx)

- [Recuperação de desastre do SharePoint Server 2013 no Microsoft Azure](https://technet.microsoft.com/library/dn635313.aspx)

- [Uso do Active Directory do Microsoft Azure para autenticação do SharePoint 2013](https://technet.microsoft.com/library/dn635311.aspx)

- [Implantar DirSync (Sincronização de Diretório) do Office 365 no Microsoft Azure](https://technet.microsoft.com/library/dn635310.aspx)

<!---HONumber=Sept15_HO3-->