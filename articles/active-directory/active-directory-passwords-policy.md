---
title: "Política: SSPR do Azure AD | Microsoft Docs"
description: "Opções de política de autoatendimento de redefinição de senha do Azure AD"
services: active-directory
keywords: "Gerenciamento de senha do Active Directory, gerenciamento de senha, autoatendimento de redefinição de senha do Azure AD"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
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
ms.openlocfilehash: 6b1abd23c6f578a3a776e6174d1ea0f47b3ba27d
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Políticas e restrições de senha do Active Directory do Azure

Este artigo descreve as políticas de senha e os requisitos de complexidade associados a contas de usuário armazenadas no locatário do Azure Active Directory (Azure AD).

## <a name="administrator-password-policy-differences"></a>Diferenças de política de senha do administrador

A Microsoft impõe uma política padrão forte de redefinição de senha de *dois portões* para qualquer função de administrador do Azure. 

Com uma política de dois portões, os administradores não podem usar perguntas de segurança.

 Uma política de entrada de duas portas requer dois tipos de dados de autenticação, como um endereço de email *e* um número de telefone. Uma política de duas portas aplica-se nas seguintes circunstâncias:

* Todas as seguintes funções de administrador do Azure são afetadas:
  * Administrador de assistência técnica
  * Administrador de suporte a serviço
  * Administrador de cobrança
  * Suporte de camada 1 do parceiro
  * Suporte de camada 2 do parceiro
  * Administrador de serviços do Exchange
  * Administrador de serviços do Lync
  * Administrador da conta de usuário
  * Gravadores de diretório
  * Administrador global ou administrador da empresa
  * Administrador de serviços do SharePoint
  * Administrador de conformidade
  * Administrador de aplicativos
  * Administrador de segurança
  * Administrador de função com privilégios
  * Administrador de serviços do Microsoft Intune
  * Administrador de serviços de Proxy do aplicativo
  * Administrador de serviços do CRM
  * Administrador de serviços do Power BI
  
* Caso tenham se passado 30 dias decorridos de uma assinatura de avaliação

  ou o

* Um domínio personalizado está presente, como contoso.com

  ou o

* O Azure AD Connect está sincronizando identidades do seu diretório local

### <a name="exceptions"></a>Exceções
Uma política de duas portas requer um tipo de dados de autenticação, como um endereço de email *ou* um número de telefone. Uma política de uma porta aplica-se nas seguintes circunstâncias:

* Está dentro dos primeiros 30 dias de uma assinatura de avaliação

  ou o

* Um domínio personalizado não está presente (*.onmicrosoft.com) 

  e 

  O Azure AD Connect não está sincronizando identidades


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Políticas UserPrincipalName que se aplicam a todas as contas de usuário

Cada conta de usuário que precisa entrar no Azure AD deve ter um valor de atributo UPN (nome principal do usuário) exclusivo associado à respectiva conta. A tabela a seguir descreve as políticas que se aplicam a ambas as contas de usuário locais do Active Directory que são sincronizadas na nuvem e a contas de usuário somente em nuvem:

| Propriedade | Requisitos de UserPrincipalName |
| --- | --- |
| Caracteres permitidos |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| Caracteres não permitidos |<ul> <li>Qualquer caractere de '@' que não esteja separando o nome de usuário do domínio.</li> <li>Não pode conter um caractere de ponto '.' imediatamente antes do símbolo '@'</li></ul> |
| Restrições de comprimento |<ul> <li>O comprimento total não deve exceder 113 caracteres</li><li>Pode haver até 64 caracteres antes do símbolo de "@"</li><li>Pode haver até 48 caracteres após o símbolo de "@"</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Políticas de senha que se aplicam somente a contas de usuário na nuvem

A tabela a seguir descreve as configurações de política de senha disponíveis que podem ser aplicadas a contas de usuário que são criadas e gerenciadas no Azure AD:

| Propriedade | Requisitos |
| --- | --- |
| Caracteres permitidos |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Caracteres não permitidos |<ul><li>Caracteres Unicode.</li><li>Espaços.</li><li> Somente senhas fortes: não podem conter um caractere de ponto “.” imediatamente antes do símbolo “@”.</li></ul> |
| Restrições de senha |<ul><li>Um mínimo de 8 caracteres e um máximo de 16 caracteres.</li><li>Somente senhas fortes: requer três de quatro dos seguintes itens:<ul><li>Caracteres minúsculos.</li><li>Caracteres maiúsculos.</li><li>Números (0-9).</li><li>Símbolos (veja as restrições de senha acima).</li></ul></li></ul> |
| Tempo de expiração da senha |<ul><li>Valor padrão: **90** dias.</li><li>O valor é configurável usando o cmdlet `Set-MsolPasswordPolicy` do Módulo do Azure Active Directory para Windows PowerShell.</li></ul> |
| Notificação de expiração de senha |<ul><li>Valor padrão: **14** dias (antes do vencimento de senha).</li><li>O valor é configurável usando o cmdlet `Set-MsolPasswordPolicy`.</li></ul> |
| Expiração de senha |<ul><li>Valor padrão: **false** dias (indica que a expiração de senha está habilitada).</li><li>O valor pode ser configurado para contas de usuário individuais usando o cmdlet `Set-MsolUser`.</li></ul> |
| Histórico de alteração de senha |A última senha *não pode* ser usada novamente quando o usuário alterar uma senha. |
| Histórico de redefinição de senha | A última senha *pode* ser usada novamente quando o usuário redefine uma senha esquecida. |
| Bloqueio de conta |Após 10 tentativas malsucedidas de conexão com a senha incorreta, o usuário será bloqueado por um minuto. Quanto mais tentativas de conexão com senha incorreta, por mais tempo o usuário ficará bloqueado. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Definir políticas de expiração de senha no Azure AD

Um administrador global de um serviço de nuvem da Microsoft pode usar o Módulo Microsoft Azure AD para Windows PowerShell de modo a configurar senhas de usuário para não expirarem. Você também pode usar os cmdlets do Windows PowerShell para remover as configurações que nunca expiram ou para ver quais senhas de usuário são configuradas para não expirar. 

Esta instrução se aplica a outros provedores, como o Intune e o Office 365, que também dependem do Azure AD para serviços de identidade e diretório. A expiração da senha é a única parte da política que pode ser alterada.

> [!NOTE]
> Apenas as senhas de contas de usuário que não são sincronizadas por meio da sincronização de diretório podem ser configuradas para não expirar. Para saber mais sobre a sincronização de diretório, confira [Conectar AD ao Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>
>

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Definir ou verificar políticas de senha usando o PowerShell

Para começar, primeiramente é preciso [baixar e instalar o módulo PowerShell do Azure AD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Depois de instalá-lo, você poderá seguir as etapas abaixo para configurar cada campo.

### <a name="how-to-check-the-expiration-policy-for-a-password"></a>Como verificar a política de expiração de senha
1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de empresa.
2. Execute um dos seguintes comandos:

   * Para ver se a senha de um único usuário está definida para nunca expirar, execute o seguinte cmdlet usando o UPN (por exemplo, *aprilr@contoso.onmicrosoft.com*) ou a ID do usuário que você deseja verificar: `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * Para ver a configuração **Senha nunca expira** para todos os usuários, execute o seguinte cmdlet: `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

### <a name="set-a-password-to-expire"></a>Definir uma senha para expirar

1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de empresa.
2. Execute um dos seguintes comandos:

   * Para definir a senha de um usuário para que ela expire, execute o seguinte cmdlet usando o UPN ou a ID do usuário: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * Para definir as senhas de todos os usuários da organização para que elas expirem, use o seguinte cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>Definir uma senha para nunca expirar

1. Conecte-se ao Windows PowerShell usando suas credenciais de administrador de empresa.
2. Execute um dos seguintes comandos:

   * Para definir a senha de um usuário para que ela nunca expire, execute o seguinte cmdlet usando o UPN ou a ID do usuário: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * Para definir as senhas de todos os usuários em uma organização para nunca expirar, execute o seguinte cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

   > [!WARNING]
   > Senhas definidas como `-PasswordNeverExpires $true` ainda expiram com base no atributo `pwdLastSet`. Se você definir as senhas de usuário para nunca expirar e, então, mais de 90 dias depois, as senhas expiram. Com base no atributo `pwdLastSet`, se você alterar a expiração para `-PasswordNeverExpires $false`, todas as senhas que tenham um `pwdLastSet` com idade acima de 90 dias exigirão que o usuário as altere na próxima vez que entrarem. Essa alteração pode afetar um grande número de usuários. 

## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir fornecem informações adicionais sobre a redefinição de senha através do Azure AD:

* [Como concluir uma implementação do SSPR com êxito?](active-directory-passwords-best-practices.md)
* [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md).
* [Registro para redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md).
* [Você tem uma pergunta sobre licenciamento?](active-directory-passwords-licensing.md)
* [Quais dados são usados pelo SSPR e quais dados você deve preencher para seus usuários?](active-directory-passwords-data.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](active-directory-passwords-how-it-works.md#authentication-methods)
* [O que é o write-back de senha e por que devo me importar com isso?](active-directory-passwords-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](active-directory-passwords-how-it-works.md)
* [Acho que algo não está funcionando. Como faço para solucionar o problema no SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)
