---
title: "Verificar uma conexão de Gateway de VPN | Microsoft Docs"
description: "Este artigo mostra como verificar a conexão de Gateway de VPN de uma rede virtual."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b5bad71095e4b7e3b26df15780467526200ffa10
ms.openlocfilehash: 68d94a6402b1497f65c4d03fb987ba800e86c2a3


---
# <a name="verify-a-vpn-gateway-connection"></a>Verificar uma conexão de Gateway de VPN
Você pode verificar a conexão de Gateway de VPN de sua rede virtual usando o portal e o PowerShell. Este artigo contém etapas para os modelos de implantação clássico e do Resource Manager.

## <a name="verify-using-the-azure-portal"></a>Verificar usando o Portal do Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="verify-using-powershell"></a>Verificar usando o PowerShell

Para verificar usando o PowerShell, instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para saber mais sobre como instalar os cmdlets do PowerShell. Para saber mais sobre como usar cmdlets do Resource Manager, confira [Usando o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

### <a name="log-in-to-your-azure-account"></a>Fazer logon na sua conta do Azure
1. Abra o console do PowerShell com privilégios elevados e conecte-se à sua conta.
   
        Login-AzureRmAccount
2. Verificar as assinaturas da conta.
   
        Get-AzureRmSubscription 
3. Especifique a assinatura que você quer usar.
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="verify-your-connection"></a>Verificar a conexão

[!INCLUDE [Powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-portal-classic"></a>Verificar usando o Portal do Azure (clássico)
[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


## <a name="verify-using-powershell-classic"></a>Verificar usando o PowerShell (clássico)
Para verificar usando o PowerShell, instale as versões mais recente dos cmdlets do Azure PowerShell. Baixe e instale as versões do Resource Manager e do Gerenciamento de Serviço (SM). Confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para saber mais sobre como instalar os cmdlets do PowerShell. 

### <a name="log-in-to-your-azure-account"></a>Fazer logon na sua conta do Azure
1. Abra o console do PowerShell com privilégios elevados e conecte-se à sua conta.
   
        Login-AzureRmAccount
2. Verificar as assinaturas da conta.
   
        Get-AzureRmSubscription 
3. Especifique a assinatura que você quer usar.
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
4. Faça logon para usar os cmdlets do Gerenciamento de Serviço para o modelo de implantação clássica.

        Add-AzureAccount

### <a name="verify-your-connection"></a>Verificar a conexão
[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Próximas etapas
* Você pode adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter as etapas.




<!--HONumber=Jan17_HO5-->


