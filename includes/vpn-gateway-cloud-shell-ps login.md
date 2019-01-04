---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fee97b29f24d8bb4f50a2929c3ceb33af85a5e21
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852346"
---
Abra o console do PowerShell com privilégios elevados.



Se você estiver executando o Microsoft Azure PowerShell localmente, conecte-se à sua conta do Azure. O cmdlet *Connect-AzureRmAccount* solicita as credenciais. Depois de entrar, ele baixa as configurações da conta para que estejam disponíveis para o Microsoft Azure PowerShell. Se você não estiver executando o PowerShell localmente e está usando o navegador o Azure Cloud Shell 'Try it', você pode pular para a etapa 2 desta seção. Você se conecta à sua conta do Azure automaticamente.

```azurepowershell
Connect-AzureRmAccount
```

Se você tiver mais de uma assinatura, obtenha uma lista das assinaturas do Azure.

```azurepowershell-interactive
Get-AzureRmSubscription
```

Especifique a assinatura que você deseja usar.

```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```