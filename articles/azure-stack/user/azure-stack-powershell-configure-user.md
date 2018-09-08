---
title: Conectar-se ao Azure Stack com o PowerShell como um usuário | Microsoft Docs
description: Etapas para se conectar à instância do Azure Stack do usuário.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2018
ms.author: sethm
ms.reviewer: Balsu.G
ms.openlocfilehash: acdad9788737f4f552cedc1b5f42e03e2288dba8
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159072"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Conectar-se ao Azure Stack com o PowerShell como um usuário

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

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
  $AADTenantName = "yourdirectory.onmicrosoft.com"
  $ArmEndpoint = "https://management.local.azurestack.external"

  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
  $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantId
   ```

## <a name="connect-with-ad-fs"></a>Conectar-se com o AD FS

  ```PowerShell  
  $ArmEndpoint = "https://management.local.azurestack.external"

  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
  $tenantId = (invoke-restmethod "$($AuthEndpoint)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $tenantId
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