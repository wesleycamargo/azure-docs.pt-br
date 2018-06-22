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
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 48a765b4e613e45699099330e4ff5c3ce7512686
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300730"
---
# <a name="configure-the-azure-stack-powershell-environment"></a>Configurar o ambiente do PowerShell de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode configurar a pilha do Azure para usar o PowerShell para gerenciar recursos, como criação de ofertas, planos, cotas e alertas. Este tópico ajuda você a configurar o ambiente de operador.

## <a name="prerequisites"></a>Pré-requisitos

Execute os seguintes pré-requisitos do [kit de desenvolvimento](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou de um cliente com Windows externo se você estiver [conectado por meio de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn): 

 - Instalar [módulos do PowerShell do Azure pilha-compatível com o Azure](azure-stack-powershell-install.md).  
 - Baixe o [ferramentas necessárias para trabalhar com o Azure pilha](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Configurar o ambiente de operador e entrar pilha do Azure

Configure o ambiente de operador de pilha do Azure com o PowerShell. Com base no tipo de implantação, o Azure AD ou AD FS, execute um dos seguintes scripts: Substitua o tenantName do AD do Azure, ponto de extremidade de GraphAudience e valores ArmEndpoint com sua própria configuração de ambiente.

### <a name="azure-active-directory-azure-ad-based-deployments"></a>Implantações do Active Directory (AD do Azure) com base do Azure

````PowerShell  
# Set the Azure Stack Tools location
$AzSTools = "C:\AzureStack-Tools-master"

# Set the Azure Active Directory Tenant Name that is used to deploy Azure Stack
$AadTenantName = "<myDirectoryTenantName>.onmicrosoft.com"

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
# To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is set to https://graph.windows.net/.
# To get this value for Azure Stack integrated systems, contact your service provider.
$GraphAudience = "<Graph Audience endpoint for your environment>"

# Navigate to the Azure Stack Tools folder and import the **Connect** PowerShell module
cd $AzSTools
Set-ExecutionPolicy RemoteSigned
Import-Module .\Connect\AzureStack.Connect.psm1

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience $GraphAudience

# Get the Active Directory tenantId that is used to deploy Azure Stack
$TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $AadTenantName `
    -EnvironmentName "AzureStackAdmin"

# After signing in to your environment, Azure Stack cmdlets
# can be easily targeted at your Azure Stack instance.
Add-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
````


### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Implantações baseadas em serviços de Federação do Active Directory (AD FS)

````PowerShell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

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
 - [Desenvolver modelos para o Azure Stack](user/azure-stack-develop-templates.md)
 - [Implantar modelos com o PowerShell](user/azure-stack-deploy-template-powershell.md)
