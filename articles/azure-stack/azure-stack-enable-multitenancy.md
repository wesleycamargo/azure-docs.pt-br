---
title: A multilocação no Azure Stack
description: Saiba como dar suporte a vários diretórios do Active Directory do Azure no Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/6/2018
ms.author: patricka
ms.reviewer: bryanr
ms.openlocfilehash: 28bd314e2dd179d83d2880e3acbf39805b54d333
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263943"
---
# <a name="multi-tenancy-in-azure-stack"></a>A multilocação no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode configurar o Azure Stack para oferecer suporte a usuários de vários locatários do Azure Active Directory (Azure AD) para usar os serviços no Azure Stack. Por exemplo, considere o cenário a seguir:

- É o administrador do serviço de contoso.onmicrosoft.com, em que o Azure Stack está instalado.
- Mary é o administrador do diretório de fabrikam.onmicrosoft.com, onde os usuários convidados estão localizados.
- Empresa de Mary recebe serviços IaaS e PaaS de sua empresa e precisa permitir que os usuários do diretório convidado (fabrikam.onmicrosoft.com) entrar e usar recursos do Azure Stack no contoso.onmicrosoft.com.

Este guia fornece as etapas necessárias, no contexto desse cenário, para configurar a multilocação no Azure Stack. Nesse cenário, você e Mary devem concluir as etapas para permitir que os usuários da Fabrikam para entrar e consumir serviços de implantação do Azure Stack na Contoso.  

## <a name="enable-multi-tenancy"></a>Habilitar multilocação

Há alguns pré-requisitos para levar em conta antes de configurar a multilocação no Azure Stack:
  
 - Você e Mary devem coordenar as etapas administrativas entre o diretório que do Azure Stack está instalado (Contoso) e o diretório de convidado (Fabrikam).  
 - Certifique-se de que você [instalados](azure-stack-powershell-install.md) e [configurados](azure-stack-powershell-configure-admin.md) PowerShell para o Azure Stack.
 - [Baixe as ferramentas do Azure Stack](azure-stack-powershell-download.md)e importe os módulos de identidade e conectar-se:

    ````PowerShell  
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ````

### <a name="configure-azure-stack-directory"></a>Configurar o diretório do Azure Stack

Nesta seção, você configura o Azure Stack para permitir entradas de locatários de diretório da Fabrikam do Azure AD.

Integrar o locatário do diretório convidado (Fabrikam) para o Azure Stack por meio da configuração do Azure Resource Manager para aceitar os usuários e entidades do locatário do diretório convidado de serviço.

O administrador do serviço de contoso.onmicrosoft.com executa os comandos a seguir.

````PowerShell  
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group. 
$location = "local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName
````

### <a name="configure-guest-directory"></a>Configurar diretório convidado

Uma vez o administrador de pilha do Azure / operador tiver habilitado o diretório da Fabrikam a ser usado com o Azure Stack, Mary deve registrar o Azure Stack com locatário do diretório da Fabrikam.

#### <a name="registering-azure-stack-with-the-guest-directory"></a>Registrando o Azure Stack com o diretório de convidado

O administrador do diretório da Fabrikam de Mary executa os comandos a seguir em que o convidado directory fabrikam.onmicrosoft.com.

````PowerShell
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
````

> [!IMPORTANT]
> Se o administrador do Azure Stack instala os novos serviços ou atualizações no futuro, você precisa executar esse script novamente.
>
> Execute este script novamente a qualquer momento para verificar o status dos aplicativos do Azure Stack em seu diretório.
>
> Se você notou problemas com a criação de VMs em Managed Disks (introduzido na atualização 1808), uma nova **provedor de recursos de disco** foi adicionado, exigindo que esse script para ser executado novamente.

### <a name="direct-users-to-sign-in"></a>Direcionar os usuários para entrar

Agora que você e Mary concluiu as etapas para o diretório de Mary integrado, Mary pode direcionar os usuários de Fabrikam para entrar.  Usuários da Fabrikam (ou seja, os usuários com o sufixo fabrikam.onmicrosoft.com) entram visitando https://portal.local.azurestack.external.  

Mary qualquer direcionará [entidades externas](../role-based-access-control/rbac-and-directory-admin-roles.md) no diretório Fabrikam (ou seja, os usuários no diretório sem o sufixo de fabrikam.onmicrosoft.com Fabrikam) para entrar usando https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.  Se eles não usarem essa URL, eles são enviados em seu diretório padrão (Fabrikam) e recebem um erro informando que seu administrador não tenha consentido.

## <a name="disable-multi-tenancy"></a>Desabilitar a multilocação

Se você não quiser mais vários locatários no Azure Stack, você pode desabilitar a multilocação seguindo as etapas a seguir na ordem:

1. Como o administrador do diretório convidado (Mary neste cenário), execute *Unregister-AzsWithMyDirectoryTenant*. O cmdlet desinstala todos os aplicativos do Azure Stack do novo diretório.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Como o administrador de serviços do Azure Stack (você nesse cenário), execute *Unregister-AzSGuestDirectoryTenant*. 

    ``` PowerShell  
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
    $ResourceGroupName = "system.local"
    
    Unregister-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
     -DirectoryTenantName $azureStackDirectoryTenant `
     -GuestDirectoryTenantName $guestDirectoryTenantToBeDecommissioned `
     -ResourceGroupName $ResourceGroupName
    ```

    > [!WARNING]
    > As etapas de multilocação disable devem ser executadas na ordem. Etapa 1 # falhará se a etapa #2 for concluída primeiro.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar provedores delegados](azure-stack-delegated-provider.md)
- [Conceitos principais de pilha do Azure](azure-stack-key-features.md)
