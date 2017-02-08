---
title: "Verificar uma conexão de gateway | Microsoft Docs"
description: "Este artigo mostra como verificar uma conexão de gateway no modelo de implantação do Resource Manager"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 3fe204c09eebf7d254a1bf2bb130e2d3498b6b45
ms.openlocfilehash: d1b6d12b1976b317e9ed496857439c86e99253f9


---
# <a name="verify-a-gateway-connection"></a>Verificar uma conexão de gateway
Você pode verificar a conexão de gateway de várias maneiras diferentes. Este artigo mostra como verificar o status de uma conexão de gateway do Resource Manager usando o portal do Azure e usando o PowerShell.

## <a name="verify-using-powershell"></a>Verificar usando o PowerShell
Você precisará instalar a versão mais recente dos cmdlets do PowerShell do Gerenciador de Recursos do Azure. Confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para saber mais sobre como instalar os cmdlets do PowerShell. Para saber mais sobre como usar cmdlets do Resource Manager, confira [Usando o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1-log-in-to-your-azure-account"></a>Etapa 1: fazer logon em sua conta do Azure
1. Abra o console do PowerShell com privilégios elevados e conecte-se à sua conta.
   
        Login-AzureRmAccount
2. Verificar as assinaturas da conta.
   
        Get-AzureRmSubscription 
3. Especifique a assinatura que você quer usar.
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="step-2-verify-your-connection"></a>Etapa 2: Verifique sua conexão
[!INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-portal"></a>Verificar usando o Portal do Azure
[!INCLUDE [verify connection portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>Próximas etapas
* Você pode adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter as etapas.




<!--HONumber=Dec16_HO1-->


