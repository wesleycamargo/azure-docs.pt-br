---
title: "Habilitar multilocação na pilha do Azure | Microsoft Docs"
description: "Saiba como dar suporte a vários diretórios do Active Directory do Azure na pilha do Azure"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: bdf92b8b73dca55e819545913931c0a79a366486
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2018
---
# <a name="enable-multi-tenancy-in-azure-stack"></a>Habilitar multilocação na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode configurar a pilha do Azure para oferecer suporte a usuários de vários locatários do Azure Active Directory (AD do Azure) para usar os serviços na pilha do Azure. Por exemplo, considere o seguinte cenário:

 - Você está o administrador do serviço de contoso.onmicrosoft.com, em que a pilha do Azure está instalada.
 - Mary é o administrador do diretório de fabrikam.onmicrosoft.com, onde os usuários convidados estão localizados. 
 - Empresa de Mary recebe serviços IaaS e PaaS de sua empresa e precisa permitir que os usuários do diretório de convidado (fabrikam.onmicrosoft.com) entrar e usar recursos de pilha do Azure no contoso.onmicrosoft.com.

Este guia fornece as etapas necessárias, no contexto desse cenário, para configurar a multilocação na pilha do Azure.  Nesse cenário, você e Mary devem concluir as etapas para permitir que os usuários da Fabrikam entrar e consumir serviços de implantação da pilha do Azure na Contoso.  

## <a name="before-you-begin"></a>Antes de começar
Há alguns pré-requisitos para levar em consideração antes de configurar a multilocação na pilha do Azure:
  
 - Você e Mary devem coordenar as etapas administrativas entre o diretório que do Azure pilha está instalada (Contoso) e o diretório de convidado (Fabrikam).  
 - Certifique-se de que você [instalado](azure-stack-powershell-install.md) e [configurado](azure-stack-powershell-configure-admin.md) PowerShell para Azure pilha.
 - [Baixar as ferramentas do Azure pilha](azure-stack-powershell-download.md)e importar os módulos conectar e identidade:

    ````PowerShell
        Import-Module .\Connect\AzureStack.Connect.psm1
        Import-Module .\Identity\AzureStack.Identity.psm1
    ```` 
 - Mary exigirá [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) acesso a pilha do Azure. 

## <a name="configure-azure-stack-directory"></a>Configurar o diretório de pilha do Azure
Nesta seção, você deve configurar a pilha do Azure para permitir logons de locatários de diretório do AD do Azure da Fabrikam.

### <a name="onboard-guest-directory-tenant"></a>Locatário de diretório convidado integrado
Em seguida, carregar o locatário de diretório do convidado (Fabrikam) a pilha do Azure.  Esta etapa configura o Gerenciador de recursos do Azure para aceitar os usuários e entidades de serviço do locatário de diretório do convidado.

````PowerShell
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location "local" `
 -ResourceGroupName $ResourceGroupName
````



## <a name="configure-guest-directory"></a>Configurar o diretório de convidado
Depois de concluir as etapas no diretório do Azure pilha, Mary deve fornecer autorização para acessar o diretório de convidado de pilha do Azure e registrar a pilha do Azure com o diretório de convidado. 

### <a name="registering-azure-stack-with-the-guest-directory"></a>Registrando a pilha do Azure com o diretório de convidado
Depois que o administrador de diretório do convidado tiver fornecido consentimento para a pilha do Azure acessar o diretório da Fabrikam, eles devem se registrar pilha do Azure com o locatário de diretório da Fabrikam.

````PowerShell
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
````
## <a name="direct-users-to-sign-in"></a>Direcionar os usuários para entrar
Agora que você e Mary concluiu as etapas para o diretório de Mary integrado, Mary pode direcionar usuários Fabrikam para entrar.  Usuários da Fabrikam (ou seja, os usuários com o sufixo fabrikam.onmicrosoft.com) entram visitando https://portal.local.azurestack.external.  

Mary direcionará qualquer [entidades externas](../active-directory/active-directory-understanding-resource-access.md) no diretório Fabrikam (ou seja, os usuários no diretório Fabrikam sem o sufixo do fabrikam.onmicrosoft.com) para entrar usando https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.  Se eles não usam essa URL, eles são enviados ao seu diretório padrão (Fabrikam) e receber um erro informando que seu administrador não consentiu.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar provedores delegados](azure-stack-delegated-provider.md)
- [Conceitos principais de pilha do Azure](azure-stack-key-features.md)
