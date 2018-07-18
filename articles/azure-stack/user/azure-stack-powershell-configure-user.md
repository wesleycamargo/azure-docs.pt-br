---
title: Configurar o ambiente do PowerShell do usuário a pilha do Azure | Microsoft Docs
description: Configurar o ambiente do PowerShell do usuário a pilha do Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/15/2018
ms.author: mabrigg
ms.reviewer: Balsu.G
ms.openlocfilehash: bcd1c53221028a852550fa429abcb9f8e9523ed4
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752414"
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Configurar o ambiente do PowerShell do usuário a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Use as instruções neste artigo para configurar o ambiente do PowerShell para um usuário de pilha do Azure.
Depois de configurar o ambiente, você pode usar o PowerShell para gerenciar recursos da pilha do Azure. Por exemplo, você pode usar o PowerShell para se inscrever para ofertas, criar máquinas virtuais e implantar modelos do Gerenciador de recursos do Azure.

>[!NOTE]
>Este artigo é o escopo para ambientes de usuário de pilha do Azure. Se você quiser configurar o PowerShell para o ambiente de operador de nuvem, consulte o [configurar o ambiente do PowerShell do operador Azure pilha](../azure-stack-powershell-configure-admin.md) artigo.

## <a name="prerequisites"></a>Pré-requisitos

Você pode configurar esses pré-requisitos do [kit de desenvolvimento](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou de um cliente com Windows externo se você estiver [conectado por meio de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Instalar [módulos do PowerShell do Azure pilha-compatível com o Azure](azure-stack-powershell-install.md).
* Baixe o [ferramentas necessárias para trabalhar com o Azure pilha](azure-stack-powershell-download.md).

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>Configurar o ambiente do usuário e entrar pilha do Azure

Com base no tipo de implantação do Azure pilha (Azure AD ou AD FS), execute um dos seguintes scripts para configurar o PowerShell para Azure pilha.

Certifique-se de que substituir as seguintes variáveis de script com valores de sua configuração de pilha do Azure:

* AAD tenantName
* ArmEndpoint

### <a name="azure-active-directory-aad-based-deployments"></a>Implantações baseadas em Azure Active Directory (AAD)

  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID
   ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Implantações baseadas em serviços de Federação do Active Directory (AD FS)

  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID
  ```

## <a name="register-resource-providers"></a>Registrar provedores de recursos

Provedores de recursos não estão registrados automaticamente para novas assinaturas de usuário que não tem nenhum recurso implantado por meio do portal. Explicitamente, você pode registrar um provedor de recursos executando o script a seguir:

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>Testar a conectividade

Quando você tem tudo configurado, teste conectividade usando o PowerShell para criar recursos na pilha do Azure. Como um teste, crie um grupo de recursos para um aplicativo e adicione uma máquina virtual. Execute o seguinte comando para criar um grupo de recursos denominado "MyResourceGroup":

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Próximas etapas

* [Desenvolver modelos para o Azure Stack](azure-stack-develop-templates.md)
* [Implantar modelos com o PowerShell](azure-stack-deploy-template-powershell.md)
