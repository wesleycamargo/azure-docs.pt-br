<properties
	pageTitle="Definir políticas de expiração de senha no Active Directory do Azure | Microsoft Azure"
	description="Saiba como verificar as políticas de expiração e alterar a expiração de senha de usuário individualmente ou em massa para senhas do Active Directory do Azure"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/10/2015"
	ms.author="curtand"/>


# Definir políticas de expiração de senha no Active Directory do Azure
> [AZURE.NOTE]Este tópico fornece conteúdo de ajuda online para serviços de nuvem, como o Microsoft Intune e o Office 365, que dependem do Active Directory do Microsoft Azure para serviços de identidade e diretório.

Como administrador global de um serviço de nuvem da Microsoft, você pode usar o Módulo Active Directory do Microsoft Azure para Windows PowerShell para configurar senhas de usuário de modo que não expirem. Você também pode usar os cmdlets do Windows PowerShell para remover as configurações que nunca expiram ou para ver quais senhas de usuário são configuradas para não expirar.

  >[AZURE.NOTE]Apenas as senhas de contas de usuário que não são sincronizadas por meio da sincronização de diretório podem ser configuradas para não expirar. Para obter mais informações sobre sincronização de diretórios, consulte a lista de tópicos no [Roteiro de sincronização de diretório](https://msdn.microsoft.com/library/azure/hh967642.aspx).

Para usar os cmdlets do Windows PowerShell, primeiro você deve instalá-los.

## O que você deseja fazer?

- [Como verificar a política de expiração de senha](#how-to-check-expiration-policy-for-a-password)

- [Definir uma senha para expirar](#set-a-password-to-expire)

- [Definir uma senha para que ela não expire](#set-a-password-not-to-expire)

## Como verificar a política de expiração de senha

1.  Conecte-se ao Windows PowerShell usando suas credenciais de administrador de empresa.

2.  Faça uma das opções a seguir:

	- Para ver se a senha de um único usuário está definida para nunca expirar, execute o seguinte cmdlet usando o nome UPN (por exemplo, aprilr@contoso.onmicrosoft.com) ou a ID do usuário que você deseja verificar: `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`

	- Para ver a configuração "Senha nunca expira" para todos os usuários, execute o seguinte cmdlet: `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## Definir uma senha para expirar

1.  Conecte-se ao Windows PowerShell usando suas credenciais de administrador de empresa.

2.  Faça uma das opções a seguir:

	- Para definir a senha de um usuário para que ela expire, execute o seguinte cmdlet usando o nome UPN ou a ID do usuário: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires \$false`
  	
	- Para definir as senhas de todos os usuários da organização para que elas expirem, use o seguinte cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires \$false`

## Definir uma senha para não expirar

1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de empresa.

2.  Faça uma das opções a seguir:

	- Para definir a senha de um usuário para que ela nunca expire, execute o seguinte cmdlet usando o nome UPN ou a ID do usuário: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires \$true`

	- Para definir as senhas de todos os usuários em uma organização para nunca expirar, execute o seguinte cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires \$true`

<!---HONumber=Sept15_HO3-->