---
title: Conceder permissão para vários aplicativos acessarem um Azure Key Vault | Microsoft Docs
description: Saiba como conceder permissão para vários aplicativos acessarem um Key Vault
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: ambapat
ms.openlocfilehash: 14e6e8bb723eb236f8fb315454b8697a3bd947ef
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286420"
---
# <a name="grant-permission-to-many-applications-to-access-a-key-vault"></a>Conceder permissão para vários aplicativos acessarem um Key Vault

## <a name="q-i-have-several-applications-that-need-to-access-a-key-vault-how-can-i-give-these-applications-up-to-1024-access-to-key-vault"></a>P: Tenho vários aplicativos que precisam acessar um cofre de chaves, como posso conceder acesso a esses aplicativos (até 1024) para o Key Vault?

A política de controle de acesso do Key Vault dá suporta a até 1024 entradas. No entanto, você pode criar um grupo de segurança do Azure Active Directory. Adicione todas as entidades de serviço associadas a esse grupo de segurança e, em seguida, conceda acesso a esse grupo de segurança para o Key Vault.

Veja os pré-requisitos:
* [Instalar o módulo do PowerShell do Azure Active Directory V2](https://www.powershellgallery.com/packages/AzureAD).
* [Instalar o Azure PowerShell](/powershell/azure/overview).
* Para executar os comandos a seguir, você precisa ter permissões para criar/editar grupos no locatário do Azure Active Directory. Se não tiver permissões, você poderá precisar entrar em contato com o administrador do Azure Active Directory.

Agora, execute os seguintes comandos no PowerShell.

```powershell
# Connect to Azure AD 
Connect-AzureAD 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId -PermissionsToKeys all –PermissionsToSecrets all –PermissionsToCertificates all 
 
# Of course you can adjust the permissions as required 
```

Se você precisar conceder um conjunto diferente de permissões para um grupo de aplicativos, crie um grupo de segurança do Azure Active Directory separado para tais aplicativos.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [Proteger seu Key Vault](key-vault-secure-your-key-vault.md).
