---
title: Usar o módulo de política de pilha do Azure | Microsoft Docs
description: Aprenda a restringir uma assinatura do Azure para que atue como uma assinatura de pilha do Azure
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
ms.openlocfilehash: 538cf0eb0f9f2351f7a71a1dd24aab05938963c5
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259076"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Gerenciar a política do Azure usando o módulo de política de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

O módulo de política de pilha do Azure permite que você configure uma assinatura do Azure com a mesma versão e a disponibilidade de serviço como a pilha do Azure.  O módulo usa o **AzureRMPolicyAssignment novo** para criar uma política do Azure, o que limita os tipos de recursos e serviços disponíveis em uma assinatura.  Depois de configurar a política, você pode usar sua assinatura do Azure para desenvolver aplicativos destinados a pilha do Azure.

## <a name="install-the-module"></a>Instalar o módulo

1. Instale a versão necessária do módulo AzureRM PowerShell, conforme descrito na etapa 1 de [instalar o PowerShell para Azure pilha](azure-stack-powershell-install.md).
2. [Baixar as ferramentas do Azure pilha do GitHub](azure-stack-powershell-download.md)
3. [Configurar o PowerShell para usar com o Azure Stack](azure-stack-powershell-configure-user.md)

4. Importe o módulo AzureStack.Policy.psm1:

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Aplicar política a assinatura do Azure

Você pode usar o comando a seguir para aplicar uma política de pilha do Azure padrão em sua assinatura do Azure. Antes de executar esse comando, substitua *nome da assinatura do Azure* com sua assinatura do Azure.

```PowerShell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>Aplicar política a um grupo de recursos

Você talvez queira aplicar políticas que são mais granulares. Por exemplo, você pode ter outros recursos em execução na mesma assinatura. Você pode definir o escopo a aplicação da diretiva para um grupo de recursos específico, o que lhe permite testar seus aplicativos para a pilha do Azure usando os recursos do Azure. Antes de executar o comando a seguir, substitua *nome da assinatura do Azure* com o nome da sua assinatura do Azure.

```PowerShell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>Diretiva em ação

Depois de implantar a política do Azure, você receber um erro ao tentar implantar um recurso que proibidos pela política.

![Resultado de falha de implantação de recursos devido à restrição de política](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Próximas etapas

* [Implantar modelos com o PowerShell](azure-stack-deploy-template-powershell.md)
* [Implantar modelos com a CLI do Azure](azure-stack-deploy-template-command-line.md)
* [Implantar modelos com o Visual Studio](azure-stack-deploy-template-visual-studio.md)
