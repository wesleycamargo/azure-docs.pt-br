---
title: Usar o módulo de política do Azure Stack | Microsoft Docs
description: Aprenda a restringir uma assinatura do Azure para que atue como uma assinatura do Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: mabrigg
ms.openlocfilehash: 105991296629e04addab33a0611736b379b11688
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281857"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Gerenciar a política do Azure usando o módulo de política do Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O módulo de política do Azure Stack permite que você configure uma assinatura do Azure com o mesmo controle de versão e a disponibilidade do serviço como o Azure Stack.  O módulo usa o **New-AzureRMPolicyAssignment** para criar uma política do Azure, o que limita os tipos de recursos e serviços disponíveis em uma assinatura.  Depois de configurar a política, você pode usar sua assinatura do Azure para desenvolver aplicativos direcionados para o Azure Stack.

## <a name="install-the-module"></a>Instalar o módulo

1. Instale a versão necessária do módulo AzureRM PowerShell, conforme descrito na etapa 1 da [instalar o PowerShell para Azure Stack](azure-stack-powershell-install.md).
2. [Baixe as ferramentas do Azure Stack no GitHub](azure-stack-powershell-download.md)
3. [Configurar o PowerShell para usar com o Azure Stack](azure-stack-powershell-configure-user.md)

4. Importe o módulo AzureStack.Policy.psm1:

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Aplicar a política à assinatura do Azure

Você pode usar o comando a seguir para aplicar uma política padrão do Azure Stack em relação a sua assinatura do Azure. Antes de executar esse comando, substitua *nome da assinatura do Azure* com sua assinatura do Azure.

```PowerShell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>Aplicar a política a um grupo de recursos

Você talvez queira aplicar políticas que são mais granulares. Por exemplo, você pode ter outros recursos em execução na mesma assinatura. Você pode definir o escopo a aplicação da diretiva de grupo de recursos específico, que permite que você teste seus aplicativos para o Azure Stack usando recursos do Azure. Antes de executar o comando a seguir, substitua *nome da assinatura do Azure* com o nome da sua assinatura do Azure.

```PowerShell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>Política em ação

Depois de implantar a política do Azure, você receber um erro ao tentar implantar um recurso que é proibida pela diretiva.

![Resultado da falha de implantação de recursos devido à restrição de política](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Próximas etapas

* [Implantar modelos com o PowerShell](azure-stack-deploy-template-powershell.md)
* [Implantar modelos com a CLI do Azure](azure-stack-deploy-template-command-line.md)
* [Implantar modelos com o Visual Studio](azure-stack-deploy-template-visual-studio.md)
