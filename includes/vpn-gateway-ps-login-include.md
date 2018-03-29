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
ms.openlocfilehash: 56d75310c90c34d1fd21821479d414bf23cf6b63
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
Antes de iniciar essa configuração, você deve fazer logon na sua conta do Azure. O cmdlet solicita as credenciais de logon para sua conta do Azure. Depois de entrar, ele baixa as configurações da conta para que elas estejam disponíveis para o Azure PowerShell. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos](../articles/powershell-azure-resource-manager.md).

Para fazer logon, abra o console do PowerShell com privilégios elevados e conecte-se à sua conta. Use o exemplo a seguir para ajudar a se conectar:

```powershell
Login-AzureRmAccount
```

Se você tiver várias assinaturas do Azure, verifique as assinaturas para a conta.

```powershell
Get-AzureRmSubscription
```

Especifique a assinatura que você deseja usar.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```