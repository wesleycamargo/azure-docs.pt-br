---
title: "Configurar um gateway VNet para o ExpressRoute usando o PowerShell: clássico: Azure | Microsoft Docs"
description: "Configure um gateway de VNet para uma VNet do modelo de implantação clássica usando o PowerShell para uma configuração da Rota Expressa."
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 85ee0bc1-55be-4760-bfb4-34d9f2c96f30
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: charwen
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: aa9b7e3ac07d4da035afd549fbeebaec17b56426
ms.contentlocale: pt-br
ms.lasthandoff: 04/27/2017


---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Configurar um gateway de rede virtual para ExpressRoute usando PowerShell (clássico)
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clássico - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo – Portal do Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Este artigo explica as etapas para adicionar, redimensionar e remover um gateway de VNet (rede virtual) para uma VNet já existente. As etapas desta configuração se destinam especificamente a VNets criadas com o **modelo de implantação clássica** e que serão usadas em uma configuração da Rota Expressa. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Sobre modelos de implantação do Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Antes de começar
Verifique se você instalou os cmdlets do Azure PowerShell necessários para esta configuração (1.0.2 ou posterior). Se ainda não os instalou, será necessário fazer isso antes de iniciar as etapas de configuração. Para obter mais informações sobre como instalar o Azure PowerShell, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Próximas etapas
Depois de criar o gateway de VNet, é possível vincular sua VNet a um circuito da Rota Expressa. Consulte [Vincular uma Rede Virtual a um circuito da Rota Expressa](expressroute-howto-linkvnet-classic.md).


