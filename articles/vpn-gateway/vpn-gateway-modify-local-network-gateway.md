---
title: "Modificar os prefixos de endereço IP do gateway de rede local e o endereço IP do Gateway de VPN | Azure | PowerShell | Microsoft Docs"
description: "Este artigo mostra o passo a passo da alteração dos prefixos de endereço IP do seu gateway de rede local"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: a26cbe2172dc27c152246d70b7f6b504ec4a08a5
ms.lasthandoff: 04/12/2017


---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Modificar as configurações de gateway de rede local usando o PowerShell
Às vezes, as configurações do seu gateway de rede local AddressPrefix ou GatewayIPAddress mudam. As instruções a seguir o ajudarão a modificar as configurações de gateway de rede local. Você também pode modificar essas configurações no portal do Azure.

## <a name="before-you-begin"></a>Antes de começar
Você precisará instalar a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para saber mais sobre como instalar os cmdlets do PowerShell.

## <a name="to-modify-ip-address-prefixes"></a>Para modificar os prefixos de endereço IP
[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address"></a>Para localizar o endereço IP do gateway
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Próximas etapas
Você pode verificar a conexão de gateway. Confira [Verificar uma conexão de gateway](vpn-gateway-verify-connection-resource-manager.md).


