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
ms.date: 01/17/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: eed3cbbcdc02d0d2faa5f9076bd6fc2dd4328bd8
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54391060"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Conectar-se ao Azure Stack com o PowerShell como um operador

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode configurar o Azure Stack para usar o PowerShell para gerenciar recursos como a criação de planos, ofertas, cotas e alertas. Este tópico ajuda você a configurar o ambiente de operador.

## <a name="prerequisites"></a>Pré-requisitos

Execute os seguintes pré-requisitos a partir de [kit de desenvolvimento](./asdk/asdk-connect.md#connect-with-rdp) ou de um cliente de externo com base no Windows se você estiver [conectado para o ASDK por meio de VPN](./asdk/asdk-connect.md#connect-with-vpn). 

 - Instale [módulos do Azure Stack-compatível com o Azure PowerShell](azure-stack-powershell-install.md).  
 - Baixe o [as ferramentas necessárias para trabalhar com o Azure Stack](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Conectar-se com o Azure AD

Configure o ambiente de operador do Azure Stack com o PowerShell. Execute um dos seguintes scripts: Substitua o nome do locatário do Azure Active Directory (Azure AD) e valores de ponto de extremidade do Azure Resource Manager com a configuração do seu próprio ambiente. <!-- GraphAudience endpoint -->

```PowerShell  
    # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external"

    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Conectar-se com o AD FS

Conecte-se ao ambiente do Azure Stack operador com o PowerShell com o Azure Active Directory Federated Services (AD FS do Azure). Para o kit de desenvolvimento do Azure Stack, esse ponto de extremidade do Azure Resource Manager é definido como `https://adminmanagement.local.azurestack.external`. Para obter o ponto de extremidade do Azure Resource Manager para sistemas integrados do Azure Stack, entre em contato com seu provedor de serviços.

<!-- GraphAudience endpoint -->

  ```PowerShell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external"

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
  $tenantId = (invoke-restmethod "$($AuthEndpoint)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment

  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $tenantId
  ```

> [!Note]  
> O AD FS dá suporte apenas a autenticação interativa com identidades de usuário. Se um objeto de credencial for necessário, você deve usar uma entidade de serviço (SPN). Para obter mais informações sobre como configurar uma entidade de serviço com o Azure Stack e AS FS como seu serviço de gerenciamento de identidade, consulte [gerenciar a entidade de serviço do AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

## <a name="test-the-connectivity"></a>Testar a conectividade

Agora que você tem tudo o que a instalação, use o PowerShell para criar recursos no Azure Stack. Por exemplo, você pode criar um grupo de recursos para um aplicativo e adicionar uma máquina virtual. Use o seguinte comando para criar um grupo de recursos denominado **MyResourceGroup**.

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Próximas etapas

 - [Desenvolver modelos para o Azure Stack](user/azure-stack-develop-templates.md)
 - [Implantar modelos com o PowerShell](user/azure-stack-deploy-template-powershell.md)
