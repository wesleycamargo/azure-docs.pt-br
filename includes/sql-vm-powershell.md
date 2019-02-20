---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: 3dc799ecc75589279c8d1c73062a8f2157761330
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56212920"
---
## <a name="start-your-powershell-session"></a>Iniciar sua sessão do PowerShell
 

Execute o cmdlet [**Connect-Az Account**](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) e você verá uma tela de entrada para inserir suas credenciais. Use as mesmas credenciais que você usa para entrar no Portal do Azure.

```powershell
Connect-AzAccount
```

Se você tiver várias assinaturas, use o cmdlet [**Set-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) para selecionar qual assinatura sua sessão do PowerShell deve usar. Para ver qual assinatura a sessão atual do PowerShell está usando, execute [**Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Para ver todas as suas assinaturas, execute [**Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

