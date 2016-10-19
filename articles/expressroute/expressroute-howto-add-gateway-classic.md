<properties
   pageTitle="Configurar um gateway de VNet para Rota Expressa usando o PowerShell | Microsoft Azure"
   description="Configure um gateway de VNet para uma VNet do modelo de implantação clássica usando o PowerShell para uma configuração da Rota Expressa."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="charwen"/>

# Configurar gateway de rede virtual para a Rota Expressa usando o modelo de implantação clássico e o PowerShell

> [AZURE.SELECTOR]
- [PowerShell – Resource Manager](expressroute-howto-add-gateway-resource-manager.md)
- [PowerShell - clássico](expressroute-howto-add-gateway-classic.md)

Este artigo explica as etapas para adicionar, redimensionar e remover um gateway de VNet (rede virtual) para uma VNet já existente. As etapas desta configuração se destinam especificamente a VNets criadas com o **modelo de implantação clássica** e que serão usadas em uma configuração da Rota Expressa.

**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Antes de começar

Verifique se você instalou os cmdlets do Azure PowerShell necessários para esta configuração (1.0.2 ou posterior). Se ainda não os instalou, será necessário fazer isso antes de iniciar as etapas de configuração. Para obter mais informações sobre como instalar o Azure PowerShell, consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

	
## Próximas etapas

Depois de criar o gateway de VNet, é possível vincular sua VNet a um circuito da Rota Expressa. Consulte [Vincular uma Rede Virtual a um circuito da Rota Expressa](expressroute-howto-linkvnet-classic.md).

<!---HONumber=AcomDC_0928_2016-->