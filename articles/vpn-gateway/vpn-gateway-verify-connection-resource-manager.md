---
title: Verificar uma conexão de gateway | Microsoft Docs
description: Este artigo mostra como verificar uma conexão de gateway no modelo de implantação do Resource Manager
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2016
ms.author: cherylmc

---
# Verificar uma conexão de gateway
Você pode verificar a conexão de gateway de várias maneiras diferentes. Este artigo mostra como verificar o status de uma conexão de gateway do Resource Manager usando o portal do Azure e usando o PowerShell.

## Antes de começar
Se pretende usar o PowerShell, você precisa instalar a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar os cmdlets do PowerShell. Para saber mais sobre como usar cmdlets do Resource Manager, confira [Usando o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

1. Abra o console do PowerShell e conecte-se à sua conta.
   
        Login-AzureRmAccount
2. Verificar as assinaturas da conta.
   
        Get-AzureRmSubscription 
3. Especifique a assinatura que você quer usar.
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## Como verificar suas conexões
[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## Próximas etapas
* Você pode adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter as etapas.

<!---HONumber=AcomDC_0810_2016-->