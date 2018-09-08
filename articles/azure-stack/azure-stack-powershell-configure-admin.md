---
title: Conectar-se ao Azure Stack com o PowerShell como um operador | Microsoft Docs
description: Saiba como se conectar ao Azure Stack com o PowerShell como um operador
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 09/07/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: e6e1ffdf4384080649a769b2fdf6877ea744ec51
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162165"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Conectar-se ao Azure Stack com o PowerShell como um operador

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode configurar o Azure Stack para usar o PowerShell para gerenciar recursos como a criação de planos, ofertas, cotas e alertas. Este tópico ajuda você a configurar o ambiente de operador.

## <a name="prerequisites"></a>Pré-requisitos

Execute os seguintes pré-requisitos a partir de [kit de desenvolvimento](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou de um cliente de externo com base no Windows se você estiver [conectados por meio de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

 - Instale [módulos do Azure Stack-compatível com o Azure PowerShell](azure-stack-powershell-install.md).  
 - Baixe o [as ferramentas necessárias para trabalhar com o Azure Stack](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Configure o ambiente de operador e entrar no Azure Stack

Configure o ambiente de operador do Azure Stack com o PowerShell. Execute um dos seguintes scripts: Substitua o nome do locatário do Azure AD, ponto de extremidade GraphAudience e valores de ArmEndpoint com sua própria configuração de ambiente.

````PowerShell  
    # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
    # To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
````

## <a name="test-the-connectivity"></a>Testar a conectividade

Agora que você tem tudo o que a instalação, use o PowerShell para criar recursos no Azure Stack. Por exemplo, você pode criar um grupo de recursos para um aplicativo e adicionar uma máquina virtual. Use o seguinte comando para criar um grupo de recursos denominado **MyResourceGroup**.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Próximas etapas

 - [Desenvolver modelos para o Azure Stack](user/azure-stack-develop-templates.md)
 - [Implantar modelos com o PowerShell](user/azure-stack-deploy-template-powershell.md)
