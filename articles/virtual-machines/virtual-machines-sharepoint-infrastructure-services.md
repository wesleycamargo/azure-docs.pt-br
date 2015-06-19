<properties 
	pageTitle="Farms do SharePoint hospedados nos serviços de infraestrutura do Azure" 
	description="Acesse os principais tópicos que descrevem como configurar um farm de desenvolvimento/teste ou produção do SharePoint 2013 nos serviços de infraestrutura do Azure." 
	documentationCenter="" 
	services="virtual-machines"
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/06/2015" 
	ms.author="josephd"/>

# Farms do SharePoint hospedados nos serviços de infraestrutura do Azure

Configure seu primeiro ou próximo farm do SharePoint de desenvolvimento/teste ou de produção nos serviços de infraestrutura do Microsoft Azure, em que você pode tirar proveito da facilidade de configuração e da capacidade de expandir rapidamente o farm para incluir a nova capacidade ou a otimização da funcionalidade de chave.

## Farm de desenvolvimento/teste básico do SharePoint 

Você pode usar o modelo de [Farm do SharePoint Server](virtual-machines-sharepoint-farm-azure-preview.md) no Portal de Visualização do Azure para criar um farm de desenvolvimento/teste básico para um site do SharePoint para a Internet.

O ambiente criado automaticamente consiste em três servidores para um controlador de domínio, um servidor SQL e o servidor do SharePoint em uma Rede Virtual do Azure somente na nuvem.

## Farm de desenvolvimento/teste do SharePoint altamente disponível

Você também pode usar o modelo de [Farm do SharePoint Server](virtual-machines-sharepoint-farm-azure-preview.md) no Portal de Visualização do Azure para criar um farm de desenvolvimento/teste do SharePoint com alta disponibilidade para um site do SharePoint para a Internet.

O ambiente criado automaticamente consiste em nove servidores em uma Rede Virtual do Azure somente na nuvem: dois para controladores de domínio, três para um cluster do SQL Server, dois servidores do SharePoint da camada de aplicativos e dois servidores do SharePoint da camada da Web.

## Farm de desenvolvimento/teste de nuvem híbrida

Com o [farm de intranet do SharePoint em um ambiente de desenvolvimento/teste de nuvem híbrida](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md), você cria uma configuração de nuvem híbrida simulada que hospeda um farm do SharePoint simples de duas camadas, o qual você pode usar para testar um farm do SharePoint de intranet hospedado no Azure de seu local na Internet.

## Farm de produção do SharePoint de intranet altamente disponível

Com [Implantando o SharePoint 2013 com grupos de disponibilidade do AlwaysOn do SQL Server no Azure](https://msdn.microsoft.com/library/dn275959.aspx), você cria um farm do SharePoint Server 2013 de intranet pronto para produção e altamente disponível no Azure.

## Recursos adicionais

[SharePoint Server no Serviços de Infraestrutura do Azure](https://msdn.microsoft.com/library/dn275955.aspx)

[Planejando o SharePoint 2013 nos Serviços de Infraestrutura do Azure](https://msdn.microsoft.com/library/dn275958.aspx)

[Arquiteturas do Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

<!--HONumber=52-->
 