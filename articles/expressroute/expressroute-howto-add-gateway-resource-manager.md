<properties
   pageTitle="Adicionar um Gateway de VPN a uma rede virtual para a Rota Expressa usando o Gerenciador de Recursos e o PowerShell | Microsoft Azure"
   description="Este artigo explica como adicionar um Gateway de VPN a uma Rede Virtual já criada do Gerenciador de Recursos para a Rota Expressa"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/26/2016"
   ms.author="cherylmc"/>

# Adicionar um Gateway de VPN a uma Rede Virtual do Gerenciador de Recursos para uma configuração da Rota Expressa 

Este artigo explica as etapas usadas para adicionar uma sub-rede de gateway e criar um gateway de VPN para uma Rede Virtual já existente. As etapas desta configuração se destinam especificamente a Redes Virtuais criadas com o **modelo de implantação do Gerenciador de Recursos** e que serão usadas em uma configuração da Rota Expressa. Se precisar de informações sobre como criar gateways para Redes Virtuais usando o modelo de implantação clássica, veja [Configurar uma rede virtual para a Rota Expressa usando o portal clássico](expressroute-howto-vnet-portal-classic.md).

## Antes de começar

Verifique se você instalou os cmdlets do Azure PowerShell necessários para esta configuração (1.0.2 ou posterior). Se ainda não os instalou, será necessário fazer isso antes de iniciar as etapas de configuração. Para obter mais informações sobre como instalar o Azure PowerShell, veja [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## Verificar se o gateway foi criado

Use o comando abaixo para verificar se o gateway foi criado.

	Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
	
## Próximas etapas

Depois de criar o gateway de VPN, é possível vincular sua Rede Virtual a um circuito da Rota Expressa. Veja [Vincular uma Rede Virtual a um circuito da Rota Expressa](expressroute-howto-linkvnet-arm.md).

<!---HONumber=AcomDC_0309_2016-->