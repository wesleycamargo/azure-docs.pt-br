<properties
	pageTitle="Ambientes de teste de nuvem híbrida do Azure"
	description="Acesse os principais tópicos que descrevem como criar ambientes de teste que você pode usar para desenvolvimento/teste ou uma prova de conceito para sua nuvem híbrida do Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="josephd"/>

# Ambientes de teste de nuvem híbrida do Azure

Para desenvolvimento/teste ou uma prova de conceito, os ambientes de teste de nuvem híbrida usam sua conexão de Internet local e um de seus endereços IP públicos e o orientam durante a configuração de uma VNet (Rede Virtual) do Azure entre locais funcional. Ao concluir, você pode desenvolver e testar aplicativos, fazer experiências com cargas de trabalho de TI simplificadas e medir o desempenho de uma conexão VPN (rede privada virtual) de site a site em relação a seu local na Internet.

> [AZURE.NOTE]Estes artigos atualmente criam máquinas virtuais, redes virtuais e outros recursos no Gerenciamento de Serviços.

## Configuração de base de nuvem híbrida

A [configuração de base de nuvem híbrida](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md) consiste em:

- Uma rede local simplificada com quatro máquinas virtuais (um controlador de domínio, um servidor de aplicativos, um computador cliente e um dispositivo VPN que executa o Windows Server e o Roteamento e Acesso Remoto).
- Uma rede virtual do Azure com um controlador de domínio de réplica
- Uma conexão VPN de site a site.

## Farm de intranet do SharePoint em uma nuvem híbrida

O [farm de intranet do SharePoint em um ambiente de teste de nuvem híbrida](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md) adiciona um servidor SQL Server 2014 e um servidor SharePoint Server 2013 à configuração de base de nuvem híbrida. Isso cria um farm do SharePoint de duas camadas que você pode acessar do computador cliente na rede local simplificada.

## Aplicativo LOB (linha de negócios) baseado na Web em uma nuvem híbrida

O [aplicativo LOB baseado na Web em um ambiente de teste de nuvem híbrida](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md) adiciona um servidor SQL Server 2014 e um servidor IIS (Serviços de Informações da Internet) à configuração de base de nuvem híbrida. Isso cria a infraestrutura na qual você pode implantar e testar um aplicativo LOB em camadas baseado na Web.

## Servidor DirSync (Sincronização de Diretório) do Office 365

O [servidor DirSync do Office 365 em um ambiente de teste de nuvem híbrida](../virtual-network/virtual-networks-setup-dirsync-hybrid-cloud-testing.md) adiciona um servidor DirSync à configuração de base de nuvem híbrida e demonstra o DirSync do Office 365 com sincronização de senha para uma assinatura de avaliação do Office 365.

## Ambiente de teste de nuvem híbrida simulado

Para organizações e indivíduos para os quais uma conexão direta com a Internet e um endereço IP público não estão prontamente disponíveis, o [ambiente de teste de nuvem híbrida simulado](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) cria a rede local simplificada em uma Rede Virtual do Azure separada e, então, conecta as duas redes virtuais a uma conexão VPN de VNet a VNet.


## Recursos adicionais

[Farms do SharePoint hospedados nos serviços de infraestrutura do Azure](virtual-machines-sharepoint-infrastructure-services.md)

[PDF do plano gráfico da arquitetura de aplicativos de linha de negócios 3-D](http://download.microsoft.com/download/2/C/8/2C8EB75F-AC45-4A79-8A63-C1800C098792/MS_Arch_LOB_App_3D_pdf.pdf)

[Implantar DirSync (Sincronização de Diretório) do Office 365 no Microsoft Azure](https://technet.microsoft.com/library/dn635310.aspx)

[Diretrizes de implementação dos Serviços de Infraestrutura do Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=July15_HO5-->