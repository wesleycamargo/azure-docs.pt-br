---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0b535cac35012b2663dee4e276371c9eacfab03a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2018
---
Antes de iniciar essa configuração, você deve fazer logon na sua conta do Azure. O cmdlet solicita as credenciais de logon para sua conta do Azure. Depois de entrar, ele baixa as configurações da conta para que elas estejam disponíveis para o Azure PowerShell. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos](../articles/powershell-azure-resource-manager.md).

Para fazer logon, abra o console do PowerShell com privilégios elevados e conecte-se à sua conta. Use o exemplo a seguir para ajudar a se conectar:

```powershell
Connect-AzureRmAccount
```

Se você tiver várias assinaturas do Azure, verifique as assinaturas para a conta.

```powershell
Get-AzureRmSubscription
```

Especifique a assinatura que você deseja usar.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
