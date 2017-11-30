---
title: Requisitos de dados SSPR do Azure AD | Microsoft Docs
description: "Requisitos de dados para autoatendimento de redefinição de senha do Azure AD e como atendê-los"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: ed82200bf81702bbe35a371e7d86676c2c27d8f4
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2017
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Implantar redefinição de senha sem exigir registro do usuário final

Para implantar a redefinição de senha de autoatendimento (SSPR) do Azure Active Directory (Azure AD), os dados de autenticação precisam estar presentes. Algumas organizações fazem com que seus usuários insiram seus próprios dados de autenticação. Mas muitas organizações preferem a sincronização com os dados que já existem no Active Directory. Os dados sincronizados serão disponibilizados para o Azure AD e SSPR sem a necessidade de interação do usuário se você:
   * Formatar corretamente os dados em seu diretório local.
   * Configurar o [Azure AD Connect usando as configurações expressas](./connect/active-directory-aadconnect-get-started-express.md).

Para funcionarem adequadamente, os números de telefone devem estar no formato *+CountryCode PhoneNumber*, por exemplo: +1 4255551234.

> [!NOTE]
> A redefinição de senha não dá suporte a ramais telefônicos. Mesmo no formato +1 4255551234X12345, as extensões são removidas antes que a chamada seja completada.

## <a name="fields-populated"></a>Campos populados

Se você usar as configurações padrão no Azure AD Connect, serão realizados os seguintes mapeamentos:

| Active Directory local | AD do Azure | Informações de contato para Autenticação do Azure AD |
| --- | --- | --- |
| telephoneNumber | Telefone comercial | Telefone alternativo |
| Serviço Móvel | Telefone celular | Telefone |


## <a name="security-questions-and-answers"></a>Perguntas e respostas de segurança

As perguntas e respostas de segurança são armazenadas em seu locatário do Azure AD e podem ser acessadas somente por usuários pelo [Portal de registro do SSPR](https://aka.ms/ssprsetup). Os administradores não podem ver nem modificar o conteúdo das perguntas e respostas dos outros usuários.

### <a name="what-happens-when-a-user-registers"></a>O que acontece quando um usuário se registra

Quando um usuário se registra, a página de registro define os seguintes campos:

* **Telefone de autenticação**
* **Email de autenticação**
* **Perguntas e respostas de segurança**

Se você forneceu um valor para **Celular** ou **Email Alternativo**, os usuários poderão usar esses valores imediatamente para redefinir as senhas, mesmo que não tenham se registrado no serviço. Além disso, os usuários visualizam esses valores ao se registrarem pela primeira vez, e os modificam, se for desejado. Após o registro bem-sucedido, esses valores serão mantidos nos campos **Telefone de Autenticação** e **Email de Autenticação**, respectivamente.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Definir e ler os dados de autenticação usando o PowerShell

Os campos a seguir podem ser definidos usando o PowerShell:

* **Email alternativo**
* **Celular**
* **Telefone comercial**: só poderá ser definido se não for sincronizar com um diretório local

### <a name="use-powershell-version-1"></a>Usar o PowerShell versão 1

Para começar, primeiramente é preciso [baixar e instalar o módulo PowerShell do Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Depois de instalá-lo, você poderá seguir as etapas abaixo para configurar cada campo.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Definir dados de autenticação com o PowerShell versão 1

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Ler dados de autenticação com o PowerShell versão 1

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Ler as opções de Telefone de Autenticação e Email de Autenticação

Para ler o **Telefone de Autenticação** e **Email de Autenticação** quando você usa o PowerShell versão 1, use os seguintes comandos:

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Usar o PowerShell versão 2

Para começar, é preciso [baixar e instalar o módulo PowerShell do Azure AD versão 2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md). Depois de instalá-lo, você poderá seguir as etapas abaixo para configurar cada campo.

Para instalar rapidamente de versões recentes do PowerShell que dão suporte ao Módulo de instalação, execute os seguintes comandos. (A primeira linha verifica se o módulo já está instalado.)

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Definir dados de autenticação com o PowerShell versão 2

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Ler dados de autenticação com o PowerShell versão 2

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Próximas etapas

* [Como concluir uma implementação do SSPR com êxito?](active-directory-passwords-best-practices.md)
* [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md)
* [Registro de redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md)
* [Você tem uma pergunta sobre licenciamento?](active-directory-passwords-licensing.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política com o SSPR?](active-directory-passwords-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](active-directory-passwords-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](active-directory-passwords-how-it-works.md)
* [Acho que algo não está funcionando. Como faço para solucionar o problema no SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)
