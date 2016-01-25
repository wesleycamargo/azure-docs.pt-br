<properties
	pageTitle="Ambientes de teste de nuvem híbrida no Azure | Microsoft Azure"
	description="Encontre os artigos que descrevem como criar ambientes profissionais de TI de verificação de conceito e de desenvolvimento e teste para a sua nuvem híbrida baseada no Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="01/12/2016"
	ms.author="josephd"/>

# Ambientes de teste de nuvem híbrida do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


Para desenvolvimento/teste ou uma prova de conceito, os ambientes de teste de nuvem híbrida usam sua conexão de Internet local e um de seus endereços IP públicos e o orientam durante a configuração de uma VNet (Rede Virtual) do Azure entre locais funcional. Ao concluir, você pode desenvolver e testar aplicativos, fazer experiências com cargas de trabalho de TI simplificadas e medir o desempenho de uma conexão VPN (rede privada virtual) de site a site em relação a seu local na Internet.

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


## Próxima etapa

- Saiba mais sobre [diretrizes de implementação](virtual-machines-infrastructure-services-implementation-guidelines.md) para criar uma implantação personalizada de teste/desenvolvimento ou produção nos serviços de infraestrutura do Azure.

<!---HONumber=AcomDC_0114_2016-->