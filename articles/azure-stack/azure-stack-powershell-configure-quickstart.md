---
title: "Instalar e configurar o PowerShell para início rápido do Azure pilha | Microsoft Docs"
description: Saiba mais sobre como instalar e configurar o PowerShell para Azure pilha.
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: sngun
ms.openlocfilehash: fe7b38d66e0e17924bad1bf3fde4af486e7968b2
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2017
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Colocar em funcionamento com o PowerShell na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Este guia de início rápido ajuda você a instalar e configurar um ambiente de pilha do Azure com o PowerShell. O script que fornecemos neste artigo tem como escopo o **operador Azure pilha** somente.

Este artigo é uma versão condensada das etapas descritas a [instalar o PowerShell]( azure-stack-powershell-install.md), [baixar ferramentas]( azure-stack-powershell-download.md), e [configurar o ambiente do PowerShell do operador Azure pilha]( azure-stack-powershell-configure-admin.md) artigos. Usando os scripts neste tópico, você pode definir o PowerShell para ambientes de pilha do Azure que são implantados com o Active Directory do Azure ou os serviços de Federação do Active Directory (AD FS).  


## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Configurar o PowerShell para implantações com base no Active Directory do Azure

Entrar no seu Kit de desenvolvimento de pilha do Azure, ou um cliente externo baseado em Windows se você estiver conectado por meio de VPN. Abra uma sessão de ISE do PowerShell com privilégios elevados e, em seguida, execute o seguinte script. Certifique-se de atualizar o **TenantName**, **ArmEndpoint**, e **GraphAudience** variáveis conforme necessário para a sua configuração de ambiente:

> [!IMPORTANT]
> A versão do módulo do PowerShell AzureRM 1.2.11 vem com uma lista de alterações significativas. Para atualizar a partir de 1.2.10 versão, consulte o [guia de migração](https://aka.ms/azspowershellmigration).

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
Get-Module -ListAvailable | `
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

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = "<Keyvault DNS suffix for your environment>"


# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $TenantName `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Configurar o PowerShell para implantações do AD FS com base em

Se você estiver operando Azure pilha quando conectado à internet, você pode usar o script a seguir. No entanto, se você estiver operando pilha do Azure sem conectividade com a internet, usar o [desconectada de maneira de instalar o PowerShell](azure-stack-powershell-install.md#install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) e os cmdlets para configurar o PowerShell permanecerá mesmos conforme mostrado no script. Entrar no seu Kit de desenvolvimento de pilha do Azure, ou um cliente externo baseado em Windows se você estiver conectado por meio de VPN. Abra uma sessão de ISE do PowerShell com privilégios elevados e, em seguida, execute o seguinte script. Certifique-se de atualizar o **ArmEndpoint** e **GraphAudience** variáveis conforme necessário para a sua configuração de ambiente:

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

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = "<Keyvault DNS suffix for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>Testar a conectividade

Agora que você configurou o PowerShell, você pode testar a configuração criando um grupo de recursos:

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

Depois que o grupo de recursos é criado, o **estado de provisionamento** está definida como **êxito**.

## <a name="next-steps"></a>Próximas etapas

* [Instalar e configurar a CLI](azure-stack-connect-cli.md)

* [Desenvolver modelos](user/azure-stack-develop-templates.md)







