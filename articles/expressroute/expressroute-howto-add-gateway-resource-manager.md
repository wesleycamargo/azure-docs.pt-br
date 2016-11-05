---
title: Adicionar um Gateway de VNet a uma rede virtual para a Rota Expressa usando o Resource Manager e o PowerShell | Microsoft Docs
description: Este artigo explica como adicionar um Gateway de VNet a uma VNet já criada do Resource Manager para a Rota Expressa
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: charwen

---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-resource-manager-and-powershell"></a>Configurar gateway de rede virtual para a Rota Expressa usando o Resource Manager e o PowerShell
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-howto-add-gateway-resource-manager.md)
> * [PowerShell - clássico](expressroute-howto-add-gateway-classic.md)
> 
> 

Este artigo explica as etapas para adicionar, redimensionar e remover um gateway de VNet (rede virtual) para uma VNet já existente. As etapas desta configuração se destinam especificamente a Redes Virtuais criadas com o **modelo de implantação do Resource Manager** e que serão usadas em uma configuração da Rota Expressa. 

**Sobre modelos de implantação do Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Antes de começar
Verifique se você instalou os cmdlets do Azure PowerShell necessários para esta configuração (1.0.2 ou posterior). Se ainda não os instalou, será necessário fazer isso antes de iniciar as etapas de configuração. Para obter mais informações sobre como instalar o Azure PowerShell, consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Próximas etapas
Depois de criar o gateway de VNet, é possível vincular sua VNet a um circuito da Rota Expressa. Consulte [Vincular uma Rede Virtual a um circuito da Rota Expressa](expressroute-howto-linkvnet-arm.md).

<!--HONumber=Oct16_HO2-->


