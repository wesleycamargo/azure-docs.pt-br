---
title: 'Redefinir um circuito falho da Rota Expressa do Azure: PowerShell | Microsoft Docs'
description: Este artigo ajuda a redefinir um circuito de ExpressRoute em um estado de falha.
documentationcenter: na
services: expressroute
author: anzaman
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: anzaman;cherylmc
ms.openlocfilehash: 423bc1d6409e5b7fe02339a05d0775f4ff42de49
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2018
---
# <a name="reset-a-failed-expressroute-circuit"></a>Redefinir um circuito falho do	Microsoft Azure ExpressRoute

Quando uma operação em um circuito do Microsoft Azure ExpressRoute não for concluída, o circuito pode entrar em um estado 'Com falha'. Este artigo ajuda a redefinir um circuito da rota expressa com falha.

## <a name="reset-a-circuit"></a>Redefinir um circuito

1. Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Para obter mais informações, consulte [Instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps).

2. Abra o console do PowerShell com privilégios elevados e conecte-se à sua conta. Use o exemplo a seguir para ajudar a se conectar:

  ```powershell
  Connect-AzureRmAccount
  ```
3. Se você tiver várias assinaturas do Azure, verifique as assinaturas para a conta.

  ```powershell
  Get-AzureRmSubscription
  ```
4. Especifique a assinatura que você deseja usar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. Execute os seguintes comandos para redefinir um circuito em um estado com falha:

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

Agora o circuito deve ser íntegro. Abra um tíquete de suporte com [o suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se o circuito ainda estiver em um estado de falha.

## <a name="next-steps"></a>Próximas etapas

Abra um tíquete de suporte com o [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se você ainda estiver enfrentando problemas.
