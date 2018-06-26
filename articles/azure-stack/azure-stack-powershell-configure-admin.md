---
title: Configurar o ambiente do PowerShell de pilha do Azure | Microsoft Docs
description: Saiba como configurar o ambiente do PowerShell de pilha do Azure.
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
ms.date: 06/22/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 74a5a9408a78dd0da12fb3f8ed721774030cc438
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36749854"
---
# <a name="configure-the-azure-stack-powershell-environment"></a>Configurar o ambiente do PowerShell de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode configurar a pilha do Azure para usar o PowerShell para gerenciar recursos, como criação de ofertas, planos, cotas e alertas. Este tópico ajuda você a configurar o ambiente de operador.

## <a name="prerequisites"></a>Pré-requisitos

Execute os seguintes pré-requisitos do [kit de desenvolvimento](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou de um cliente com Windows externo se você estiver [conectado por meio de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

 - Instalar [módulos do PowerShell do Azure pilha-compatível com o Azure](azure-stack-powershell-install.md).  
 - Baixe o [ferramentas necessárias para trabalhar com o Azure pilha](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Configurar o ambiente de operador e entrar pilha do Azure

Configure o ambiente de operador de pilha do Azure com o PowerShell. Execute um dos seguintes scripts: Substitua o tenantName do AD do Azure, ponto de extremidade de GraphAudience e valores ArmEndpoint com sua própria configuração de ambiente.

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

Agora que você tem tudo o que a instalação, use o PowerShell para criar recursos na pilha do Azure. Por exemplo, você pode criar um grupo de recursos para um aplicativo e adicionar uma máquina virtual. Use o seguinte comando para criar um grupo de recursos denominado **MyResourceGroup**.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Próximas etapas

 - [Desenvolver modelos para o Azure Stack](user/azure-stack-develop-templates.md)
 - [Implantar modelos com o PowerShell](user/azure-stack-deploy-template-powershell.md)
