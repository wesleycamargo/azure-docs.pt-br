<properties
   pageTitle="Modificar prefixos de endereço IP de gateway de rede local e IP de gateway | Microsoft Azure"
   description="Este artigo mostra o passo a passo da alteração dos prefixos de endereço IP do seu gateway de rede local"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="cherylmc"/>

# Modificar as configurações de gateway de rede local usando o PowerShell

Às vezes, as configurações do seu gateway de rede local AddressPrefix ou GatewayIPAddress mudam. As instruções a seguir o ajudarão a modificar as configurações de gateway de rede local. Você também pode modificar essas configurações no portal do Azure.

## Antes de começar
	
Você precisará instalar a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar os cmdlets do PowerShell.

## Para modificar os prefixos de endereço IP

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## Para localizar o endereço IP do gateway

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## Próximas etapas

Você pode verificar a conexão de gateway. Confira [Verificar uma conexão de gateway](vpn-gateway-verify-connection-resource-manager.md).

<!---HONumber=AcomDC_0810_2016-->