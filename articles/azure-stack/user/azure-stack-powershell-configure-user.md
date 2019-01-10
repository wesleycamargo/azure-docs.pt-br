---
title: Conectar-se ao Azure Stack com o PowerShell como um usuário | Microsoft Docs
description: Etapas para se conectar à instância do Azure Stack do usuário.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2018
ms.author: sethm
ms.reviewer: Balsu.G
ms.openlocfilehash: cf0d0f5bb87d1a7750775d3e22c8c50dcd8cf24d
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159427"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Conectar-se ao Azure Stack com o PowerShell como um usuário

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Este artigo fornece as etapas para conectar-se à instância do Azure Stack. Conecte-se para gerenciar os recursos do Azure Stack com o PowerShell. Por exemplo, você pode usar o PowerShell para assinar a oferta, criar máquinas virtuais e implantar modelos do Azure Resource Manager. para executar os cmdlets do PowerShell.

Para configurar:
  - Verifique se que você tem os requisitos.
  - Conectar-se com o Azure Active Directory (Azure AD) ou serviços de Federação do Active Directory (AD FS). 
  - Registre provedores de recursos.
  - Teste a conectividade.

## <a name="prerequisites"></a>Pré-requisitos

Você pode configurar esses pré-requisitos do [kit de desenvolvimento](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou de um cliente de externo com base no Windows se você estiver [conectados por meio de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Instale [módulos do Azure Stack-compatível com o Azure PowerShell](azure-stack-powershell-install.md).
* Baixe o [as ferramentas necessárias para trabalhar com o Azure Stack](azure-stack-powershell-download.md).

Certifique-se de que substituir as seguintes variáveis de script com valores da sua configuração do Azure Stack:

- **Nome do locatário do AD do Azure**  
  O nome do locatário do Azure AD usado para gerenciar o Azure Stack, por exemplo, yourdirectory.onmicrosoft.com.
- **Ponto de extremidade do Azure Resource Manager**  
  Para o kit de desenvolvimento do Azure Stack, esse valor é definido como https://management.local.azurestack.external. Para obter esse valor para os sistemas integrados do Azure Stack, entre em contato com seu provedor de serviços.

## <a name="connect-with-azure-ad"></a>Conectar-se com o Azure AD

```PowerShell  
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Conectar-se com o AD FS

  ```PowerShell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
  $tenantId = (invoke-restmethod "$($AuthEndpoint)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment

  $cred = get-credential

  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $tenantId `
    -Credential $cred
  ```

## <a name="register-resource-providers"></a>Registrar provedores de recursos

Provedores de recursos não são registrados automaticamente para novas assinaturas de usuário que não têm todos os recursos implantados por meio do portal. Explicitamente, você pode registrar um provedor de recursos executando o seguinte script:

```PowerShell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>Testar a conectividade

Quando você tem tudo configurado, teste a conectividade por meio do PowerShell para criar recursos no Azure Stack. Como um teste, crie um grupo de recursos para um aplicativo e adicionar uma máquina virtual. Execute o seguinte comando para criar um grupo de recursos chamado "MyResourceGroup":

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Próximas etapas

- [Desenvolver modelos para o Azure Stack](azure-stack-develop-templates.md)
- [Implantar modelos com o PowerShell](azure-stack-deploy-template-powershell.md)
- Se você quiser configurar o PowerShell para o ambiente de operador de nuvem, consulte o [configurar o ambiente do PowerShell do operador do Azure Stack](../azure-stack-powershell-configure-admin.md) artigo.
