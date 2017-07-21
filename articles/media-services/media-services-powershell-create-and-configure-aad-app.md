---
title: "Usar o PowerShell para criar um aplicativo do Azure AD para acessar a API dos Serviços de Mídia do Azure | Microsoft Docs"
description: "Saiba como usar o PowerShell para criar um aplicativo do Azure AD (Azure Active Directory) e configurá-lo para acessar a API dos Serviços de Mídia do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: f1d7ad94686d633b6353f39cef91d1cb1efab773
ms.contentlocale: pt-br
ms.lasthandoff: 07/01/2017


---

# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Usar o PowerShell para criar um aplicativo do Azure AD para usá-lo com a API dos Serviços de Mídia do Azure

Saiba como usar um script do PowerShell para criar um aplicativo do Azure AD (Azure Active Directory) e uma entidade de serviço para acessar os recursos dos Serviços de Mídia do Azure.  

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se você não tiver uma conta, comece com uma [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Serviços de Mídia. Para obter mais informações, consulte [Criar uma conta dos Serviços de Mídia do Azure no portal do Azure](media-services-portal-create-account.md).
- Azure PowerShell versão 0.8.8 ou uma versão posterior. Para obter mais informações, consulte [Como usar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
- Cmdlets do Azure Resource Manager.  

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Criar um aplicativo do Azure AD usando o PowerShell  

```powershell
Login-AzureRmAccount
Import-Module AzureRM.Resources
Set-AzureRmContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

Para obter mais informações, consulte os seguintes artigos:

- [Usar o Azure PowerShell para criar uma entidade de serviço a fim de acessar recursos](../azure-resource-manager/resource-group-authenticate-service-principal.md)
- [Gerenciar o Controle de Acesso Baseado em Função usando o Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
- [Como configurar aplicativos daemon manualmente usando certificados](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>Próximas etapas

Comece a [carregar arquivos para sua conta](media-services-portal-upload-files.md).

