---
title: Configurar o ambiente do PowerShell do operador de pilha do Azure | Microsoft Docs
description: Saiba como configurar o ambiente do PowerShell do operador de pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 37D9CAC9-538B-4504-B51B-7336158D8A6B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: mabrigg
ms.openlocfilehash: a8ab52de6c57e84bb2c90ce6bcf53ef1b92e30af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="configure-the-azure-stack-operators-powershell-environment"></a>Configurar o ambiente do PowerShell do operador de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode configurar a pilha do Azure para usar o PowerShell para gerenciar recursos, como criação de ofertas, planos, cotas e alertas. Este tópico ajuda você a configurar o ambiente de operador. Se você quiser configurar o PowerShell para o ambiente do usuário, consulte [configurar o ambiente do PowerShell do usuário do Azure pilha](user/azure-stack-powershell-configure-user.md) artigo.

## <a name="prerequisites"></a>Pré-requisitos

Execute os seguintes pré-requisitos do [kit de desenvolvimento](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou de um cliente com Windows externo se você estiver [conectado por meio de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn): 

* Instalar [módulos do PowerShell do Azure pilha-compatível com o Azure](azure-stack-powershell-install.md).  
* Baixe o [ferramentas necessárias para trabalhar com o Azure pilha](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Configurar o ambiente de operador e entrar pilha do Azure

Configure o ambiente de operador de pilha do Azure com o PowerShell. Com base no tipo de implantação, o Azure AD ou AD FS, execute um dos seguintes scripts: Substitua o tenantName do AD do Azure, ponto de extremidade de GraphAudience e valores ArmEndpoint com sua própria configuração de ambiente.

### <a name="azure-active-directory-azure-ad-based-deployments"></a>Implantações do Active Directory (AD do Azure) com base do Azure

````powershell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# Get the value of your Directory Tenant ID
$TenantID = Get-AzsDirectoryTenantId -AADTenantName "<mydirectorytenant>.onmicrosoft.com" -EnvironmentName AzureStackAdmin

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````


### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Implantações baseadas em serviços de Federação do Active Directory (AD FS)

````powershell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# Get the value of your Directory Tenant ID
$TenantID = Get-AzsDirectoryTenantId -ADFS -EnvironmentName AzureStackAdmin

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````

## <a name="test-the-connectivity"></a>Testar a conectividade

Agora que você tem tudo o que a instalação, vamos usar o PowerShell para criar recursos na pilha do Azure. Por exemplo, você pode criar um grupo de recursos para um aplicativo e adicionar uma máquina virtual. Use o comando a seguir para criar um grupo de recursos denominado "MyResourceGroup":

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Próximas etapas
* [Desenvolver modelos para o Azure Stack](user/azure-stack-develop-templates.md)
* [Implantar modelos com o PowerShell](user/azure-stack-deploy-template-powershell.md)
