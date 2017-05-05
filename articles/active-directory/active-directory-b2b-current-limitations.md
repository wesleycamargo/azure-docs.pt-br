---
title: "Limitações de colaboração B2B do Azure Active Directory | Microsoft Docs"
description: "Limitações atuais da visualização da colaboração B2B do Azure Active Directory"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/12/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: cdc951d4e16e7f0df425dba7c33d86255276f526
ms.lasthandoff: 04/13/2017


---

# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitações da colaboração B2B do Azure AD
A colaboração B2B do Azure Active Directory (Azure AD) está sujeita, atualmente, às limitações descritas neste artigo.

## <a name="possible-double-multi-factor-authentication"></a>Possível autenticação multifator dupla
Com B2B do Azure AD é possível impor a autenticação multifator na organização do recurso (a organização emissora do convite). Os motivos para essa abordagem são detalhados em [Acesso condicional para usuários de colaboração B2B](active-directory-b2b-mfa-instructions.md). Isso significa que se um parceiro já tiver a autenticação multifator configurada e imposta, os usuários do parceiro talvez tenham que executar a autenticação uma vez em sua organização inicial e, depois, novamente na sua.

Em uma versão futura, pretendemos introduzir uma política em que é possível evitar o problema da autenticação dupla ao optar por confiar na autenticação multifator do parceiro.


## <a name="instant-on"></a>Instant-on
Nos fluxos de colaboração B2B, adicionamos usuários ao diretório e os atualizamos dinamicamente durante o resgate do convite, atribuição do aplicativo e assim por diante. As atualizações e as gravações em geral ocorrem em uma instância do diretório e devem ser replicadas em todas as instâncias. Pode demorar uma quantidade de tempo não nulo para completar a replicação. Às vezes, quando o objeto é gravado ou atualizado em uma instância do diretório e a chamada para recuperar esse objeto é carregada em outra instância, isso causou problemas de autorização. Temos trabalhado muito para eliminar ou reduzir essas latências de replicação, mas é possível que ainda possam ocorrer em alguns raros casos. Se isso acontecer, atualize ou tente novamente. Se você estiver gravando um aplicativo utilizando nossa API, então, tentativas com algumas retiradas é uma boa prática defensiva para aliviar esse problema.

## <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriedades de usuário de colaboração B2B](active-directory-b2b-user-properties.md)
* [Adicionar um usuário de colaboração B2B a uma função](active-directory-b2b-add-guest-to-role.md)
* [Delegar convites de colaboração B2B](active-directory-b2b-delegate-invitations.md)
* [Grupos dinâmicos e colaboração B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboração B2B e exemplos do PowerShell](active-directory-b2b-code-samples.md)
* [Configurar aplicativos SaaS para colaboração B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de usuário de colaboração B2B](active-directory-b2b-user-token.md)
* [Mapeamento de declarações de usuário de colaboração B2B](active-directory-b2b-claims-mapping.md)
* [Compartilhamento externo do Office 365](active-directory-b2b-o365-external-user.md)

