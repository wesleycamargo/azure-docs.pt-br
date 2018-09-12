---
title: Instalar e configurar o PowerShell para o guia de início rápido do Azure Stack | Microsoft Docs
description: Saiba mais sobre como instalar e configurar o PowerShell para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: mabrigg
ms.openlocfilehash: 5d988e8a8a32924b8424a07cf20c75f0e8f8cf4d
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391067"
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Entrar em funcionamento com o PowerShell no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Neste início rápido ajuda você a instalar e configurar um ambiente do Azure Stack com o PowerShell. O script que fornecemos neste artigo tem como escopo o **operador do Azure Stack** apenas.

Este artigo é uma versão condensada do que as etapas descritas a [instalar o PowerShell]( azure-stack-powershell-install.md), [baixar ferramentas]( azure-stack-powershell-download.md), e [configurar o ambiente do PowerShell do operador do Azure Stack]( azure-stack-powershell-configure-admin.md) artigos. Usando os scripts neste artigo, você pode configurar o PowerShell para ambientes Azure Stack que são implantados com o Azure Active Directory ou os serviços de Federação do Active Directory (AD FS).  


## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Configurar o PowerShell para implantações com base no Active Directory do Azure

<a name="sign-in-to-your-azure-stack-development-kit-or-a-windows-based-external-client-if-you-are-connected-through-vpn-open-an-elevated-powershell-ise-session-and-then-run-the-following-script"></a>Entrar em seu Kit de desenvolvimento do Azure Stack, ou um cliente externo baseado em Windows se você estiver conectado por meio de VPN. Abra uma sessão elevada do PowerShell ISE e, em seguida, execute o seguinte script. 
-  
- Certifique-se de atualizar o **TenantName**, **ArmEndpoint**, e **GraphAudience** variáveis conforme necessário para a configuração do seu ambiente:

```powershell
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $TenantName `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Add-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Configurar o PowerShell para implantações do AD FS com base em

Se você estiver operando do Azure Stack quando conectado à internet, você pode usar o script a seguir. No entanto, se você estiver operando do Azure Stack sem conectividade com a internet, use o [desconectada de maneira de instalar o PowerShell](azure-stack-powershell-install.md) e os cmdlets para configurar o PowerShell permanecerá igual ao mostrado neste script. Entrar em seu Kit de desenvolvimento do Azure Stack, ou um cliente externo baseado em Windows se você estiver conectado por meio de VPN. Abra uma sessão elevada do PowerShell ISE e, em seguida, execute o seguinte script. Certifique-se de atualizar o **ArmEndpoint** e **GraphAudience** variáveis conforme necessário para a configuração do seu ambiente:

```powershell

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Add-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>Testar a conectividade

Agora que você configurou o PowerShell, você pode testar a configuração com a criação de um grupo de recursos:

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

> [!note]  
> Para especificar um grupo de recursos, você precisará ter um grupo de recursos em sua assinatura. Para obter mais informações sobre assinaturas, consulte [visão geral do plano, oferta, cotas e assinatura](azure-stack-plan-offer-quota-overview.md)

Depois que o grupo de recursos é criado, o **estado de provisionamento** estiver definida como **Succeeded**.

## <a name="next-steps"></a>Próximas etapas

* [Instalar e configurar a CLI](azure-stack-connect-cli.md)

* [Desenvolver modelos](user/azure-stack-develop-templates.md)
