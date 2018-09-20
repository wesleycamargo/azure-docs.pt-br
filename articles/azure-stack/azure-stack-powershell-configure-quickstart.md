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
ms.date: 09/17/2018
ms.author: mabrigg
ms.openlocfilehash: db253c921354ea132dc6b043dcb6f0b96cdbfd88
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368164"
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Entrar em funcionamento com o PowerShell no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Neste início rápido ajuda você a instalar e configurar um ambiente do Azure Stack com o PowerShell. O script que fornecemos neste artigo tem como escopo o **operador do Azure Stack** apenas.

Este artigo é uma versão condensada do que as etapas descritas a [instalar o PowerShell]( azure-stack-powershell-install.md), [baixar ferramentas]( azure-stack-powershell-download.md), e [configurar o ambiente do PowerShell do operador do Azure Stack]( azure-stack-powershell-configure-admin.md) artigos. Usando os scripts neste artigo, você pode configurar o PowerShell para ambientes Azure Stack que são implantados com o Azure Active Directory ou os serviços de Federação do Active Directory (AD FS).  

## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Configurar o PowerShell para implantações com base no Active Directory do Azure  

Entrar em seu Kit de desenvolvimento do Azure Stack, ou um cliente externo baseado em Windows se você estiver conectado por meio de VPN. Abra uma sessão elevada do PowerShell ISE e, em seguida, execute os seguintes scripts.

Certifique-se de atualizar o **TenantName**, **ArmEndpoint**, e **GraphAudience** variáveis conforme necessário para a configuração do seu ambiente:

```PowerShell  
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following commands:
Get-Module -ListAvailable -Name Azure* | Uninstall-Module
Get-Module Azs.* -ListAvailable | Uninstall-Module -force

# Install PowerShell for Azure Stack.
Install-Module -Name AzureRm.BootStrapper -Force
```

Carregar o módulo de perfil e o administrador de API para sua versão do Azure Stack.

  - O Azure Stack 1808 ou posterior.

  ```PowerShell  
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.5.0
  ```

  - O Azure Stack 1807 ou anterior.

  ```PowerShell  
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    Install-Module -Name AzureStack -RequiredVersion 1.4.0
  ```

  - O Azure Stack 1804 ou anterior.

  ```PowerShell  
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    Install-Module -Name AzureStack -RequiredVersion 1.2.11
  ```

Baixe as ferramentas do Azure Stack e conecte-se.

```PowerShell  
# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip

expand-archive master.zip -DestinationPath . -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId -AADTenantName $TenantName -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Configurar o PowerShell para implantações do AD FS com base em

Se você estiver operando do Azure Stack quando conectado à internet, você pode usar o script a seguir. No entanto, se você estiver operando do Azure Stack sem conectividade com a internet, use o [desconectada de maneira de instalar o PowerShell](azure-stack-powershell-install.md) e os cmdlets para configurar o PowerShell permanecerá igual ao mostrado neste script. Entrar em seu Kit de desenvolvimento do Azure Stack, ou um cliente externo baseado em Windows se você estiver conectado por meio de VPN. Abra uma sessão elevada do PowerShell ISE e, em seguida, execute o seguinte script. Certifique-se de atualizar o **ArmEndpoint** e **GraphAudience** variáveis conforme necessário para a configuração do seu ambiente:

```PowerShell  

# Set the module repository and the execution policy.
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module -Name AzureRm.BootStrapper -Force
```

Carregar o módulo de perfil e o administrador de API para sua versão do Azure Stack.

  - O Azure Stack 1808 ou posterior.

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ````

  - O Azure Stack 1807 ou anterior.

    > [!Note]  
    Para atualizar a partir de 1.2.11 versão, consulte a [guia de migração](https://aka.ms/azspowershellmigration).

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.4.0
    ````

  - O Azure Stack 1804 ou anterior.

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.3.0
    ````

Baixe as ferramentas do Azure Stack e conecte-se.

```PowerShell  
# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip

expand-archive master.zip -DestinationPath . -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId -ADFS -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>Testar a conectividade

Agora que você configurou o PowerShell, você pode testar a configuração com a criação de um grupo de recursos:

```PowerShell  
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

> [!note]  
> Para especificar um grupo de recursos, você precisará ter um grupo de recursos em sua assinatura. Para obter mais informações sobre assinaturas, consulte [visão geral do plano, oferta, cotas e assinatura](azure-stack-plan-offer-quota-overview.md)

Depois que o grupo de recursos é criado, o **estado de provisionamento** estiver definida como **Succeeded**.

## <a name="next-steps"></a>Próximas etapas

 - [Instalar e configurar a CLI](azure-stack-connect-cli.md)
 - [Desenvolver modelos](user/azure-stack-develop-templates.md)
