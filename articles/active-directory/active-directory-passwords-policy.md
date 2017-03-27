---
title: "Políticas e restrições de senha do Azure Active Directory | Microsoft Docs"
description: "Descreve as políticas que se aplicam a senhas no Active Directory do Azure, incluindo caracteres permitidos, comprimento e expiração"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e7cae5cf-e0ee-467b-9a90-db6fdf56cd52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 52c58c5c8ea35bf29f8b847e5b7d119d6ab85da4
ms.lasthandoff: 03/10/2017


---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Políticas e restrições de senha do Active Directory do Azure
Este artigo descreve as políticas de senha e requisitos de complexidade associados a contas de usuário armazenadas no diretório do Azure AD.

> [!IMPORTANT]
> **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md#how-to-reset-your-password).
>
>

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Políticas UserPrincipalName que se aplicam a todas as contas de usuário
Cada conta de usuário que precisa entrar no sistema de autenticação do Azure AD deve ter um valor de atributo UPN (nome principal do usuário) exclusivo associado a essa conta. A tabela a seguir descreve as políticas que se aplicam a contas de usuário de origem do Active Directory no local (sincronizadas na nuvem) e a contas de usuário somente em nuvem.

| Propriedade | Requisitos de UserPrincipalName |
| --- | --- |
| Caracteres permitidos |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| Caracteres não permitidos |<ul> <li>Qualquer caractere '@' que não esteja separando o nome de usuário do domínio.</li> <li>Não pode conter um caractere de ponto '.' imediatamente antes do símbolo '@'</li></ul> |
| Restrições de comprimento |<ul> <li>O comprimento total não deve exceder 113 caracteres</li><li>64 caracteres antes do símbolo '@'</li><li>48 caracteres depois do símbolo '@'</li></ul> |

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>Políticas de senha que se aplicam somente a contas de usuário na nuvem
A tabela a seguir descreve as configurações de política de senha disponíveis que podem ser aplicadas a contas de usuário que são criadas e gerenciadas no Azure AD.

| Propriedade | Requisitos |
| --- | --- |
| Caracteres permitidos |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Caracteres não permitidos |<ul><li>Caracteres Unicode</li><li>Espaços</li><li> **Somente senhas fortes**: não pode conter um caractere de ponto '.' imediatamente antes do símbolo '@'</li></ul> |
| Restrições de senha |<ul><li>Mínimo de 8 caracteres e no máximo 16 caracteres</li><li>**Somente senhas fortes**: Requer 3 de 4 dos seguintes:<ul><li>Caracteres minúsculos</li><li>Caracteres maiúsculos</li><li>Números (0-9)</li><li>Símbolos (veja as restrições de senha acima)</li></ul></li></ul> |
| Tempo de expiração da senha |<ul><li>Valor padrão: **90** dias </li><li>O valor é configurável usando o cmdlet Set-MsolPasswordPolicy do Módulo do Azure Active Directory para Windows PowerShell.</li></ul> |
| Notificação de expiração de senha |<ul><li>Valor padrão: **14** dias (antes do vencimento de senha)</li><li>O valor é configurável usando o cmdlet Set-MsolPasswordPolicy.</li></ul> |
| Expiração de senha |<ul><li>Valor padrão: **false** dias (indica que a expiração de senha está habilitada) </li><li>O valor pode ser configurado para contas de usuário individuais usando o cmdlet Set-MsolUser. </li></ul> |
| Histórico de **alteração** de senha |A última senha **não pode** ser usada novamente ao **alterar** uma senha. |
| Histórico de **redefinição** de senha | A última senha **pode** ser usada novamente ao **redefinir** uma senha esquecida. |
| Bloqueio de conta |Após 10 tentativas malsucedidas de entrar (senha incorreta), o usuário será bloqueado por um minuto. Mais tentativas de entrar incorretas farão com que o usuário seja bloqueado por durações cada vez maiores. |

## <a name="next-steps"></a>Próximas etapas
* **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md#how-to-reset-your-password).
* [Gerenciar suas senhas de qualquer lugar](active-directory-passwords.md)
* [Como funciona o gerenciamento de senhas](active-directory-passwords-how-it-works.md)
* [Introdução ao gerenciamento de senhas](active-directory-passwords-getting-started.md)
* [Personalizar o gerenciamento de senhas](active-directory-passwords-customize.md)
* [Práticas recomendadas de gerenciamento de senhas](active-directory-passwords-best-practices.md)
* [Como obter percepções operacionais com relatórios de gerenciamento de senhas](active-directory-passwords-get-insights.md)
* [Perguntas frequentes sobre Gerenciamento de Senhas](active-directory-passwords-faq.md)
* [Solucionar problemas do Gerenciamento de Senhas](active-directory-passwords-troubleshoot.md)
* [Saiba mais](active-directory-passwords-learn-more.md)

