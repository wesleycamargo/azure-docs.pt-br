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
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201475"
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