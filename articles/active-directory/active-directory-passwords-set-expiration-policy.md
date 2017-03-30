---
title: "Definir políticas de expiração de senha no Azure Active Directory | Microsoft Docs"
description: "Saiba como verificar as políticas de expiração e alterar a expiração de senha de usuário individualmente ou em massa para senhas do Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 6887250c-15d4-4b59-a161-f0380c0f0acb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: ab55d08c78f6c333819dc31936b24ae6797870bb
ms.lasthandoff: 03/28/2017


---
# <a name="set-password-expiration-policies-in-azure-active-directory"></a>Definir políticas de expiração de senha no Active Directory do Azure
> [!IMPORTANT]
> **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md#reset-your-password).
>
>

Como administrador global de um serviço de nuvem da Microsoft, você pode usar o Módulo Active Directory do Microsoft Azure para Windows PowerShell para configurar senhas de usuário de modo que não expirem. Você também pode usar os cmdlets do Windows PowerShell para remover as configurações que nunca expiram ou para ver quais senhas de usuário são configuradas para não expirar. Este artigo fornece ajuda para serviços de nuvem, como o Microsoft Intune e o Office 365, que dependem do Microsoft Azure Active Directory para serviços de identidade e diretório.

> [!NOTE]
> Apenas as senhas de contas de usuário que não são sincronizadas por meio da sincronização de diretório podem ser configuradas para não expirar. Para saber mais sobre sincronização de diretórios, consulte a lista de tópicos no [Roteiro de sincronização de diretório](https://msdn.microsoft.com/library/azure/hh967642.aspx).
>
>

Para usar os cmdlets do Windows PowerShell, primeiro você deve instalá-los.

## <a name="what-do-you-want-to-do"></a>O que você deseja fazer?
* [Como verificar a política de expiração de senha](#how-to-check-expiration-policy-for-a-password)
* [Definir uma senha para expirar](#set-a-password-to-expire)
* [Definir uma senha para que ela não expire](#set-a-password-to-never-expire)

## <a name="how-to-check-expiration-policy-for-a-password"></a>Como verificar a política de expiração de senha
1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de empresa.
2. Faça uma das opções a seguir:

   * Para ver se a senha de um único usuário está definida para nunca expirar, execute o seguinte cmdlet usando o nome UPN (por exemplo, aprilr@contoso.onmicrosoft.com) ou a ID do usuário que você deseja verificar: `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * Para ver a configuração "Senha nunca expira" para todos os usuários, execute o seguinte cmdlet: `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## <a name="set-a-password-to-expire"></a>Definir uma senha para expirar
1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de empresa.
2. Faça uma das opções a seguir:

   * Para definir a senha de um usuário para que ela expire, execute o seguinte cmdlet usando o nome UPN ou a ID do usuário: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * Para definir as senhas de todos os usuários da organização para que elas expirem, use o seguinte cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

## <a name="set-a-password-to-never-expire"></a>Definir uma senha para nunca expirar
1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de empresa.
2. Faça uma das opções a seguir:

   * Para definir a senha de um usuário para que ela nunca expire, execute o seguinte cmdlet usando o nome UPN ou a ID do usuário: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * Para definir as senhas de todos os usuários em uma organização para nunca expirar, execute o seguinte cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>Próximas etapas
* **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md#reset-your-password).

