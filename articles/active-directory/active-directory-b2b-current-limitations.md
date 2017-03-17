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
ms.date: 02/16/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0e71a840d4f503779131ee4a21fe6063d33185f1
ms.openlocfilehash: cbefca2d45a332cd57cfea49dfeaa300426d5502
ms.lasthandoff: 02/24/2017


---

# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitações da colaboração B2B do Azure AD
A colaboração B2B do Azure Active Directory (Azure AD) está sujeita, atualmente, às limitações descritas neste artigo.

## <a name="invitation-apis-are-in-preview"></a>As APIs de convite estão em visualização
A superfície de API é a nossa direção de planejamento antecipada. No entanto, como todas as versões de pré-lançamento, a API está sujeito ao contrato de namespace de visualização. Mudaremos a API para uma versão numerada com nossa versão de lançamento de disponibilidade geral (GA).

## <a name="possible-double-multi-factor-authentication"></a>Possível Autenticação Multifator dupla
Essa redundância pode surgir se o seu parceiro já tiver uma política de Autenticação Multifator do Azure estabelecida. A Autenticação Multifator de colaboração B2B é realizada e gerenciada na organização que está convidando. Essa autenticação é desejável, pois cobre todas as identidades e garante a você o controle sobre o nível de autenticação dos convidados de colaboração B2B.

No entanto, se um parceiro já tiver uma configuração de Autenticação Multifator e aplicá-la, é possível que os usuários do parceiro tenham que executar a autenticação uma vez na organização inicial e novamente na sua.

Em uma versão futura, pretendemos introduzir uma política em que é possível evitar o problema da autenticação dupla ao optar por confiar na Autenticação Multifator do parceiro.

## <a name="instant-on"></a>Instant-On
Nos fluxos de colaboração B2B, adicionamos usuários ao diretório e os atualizamos dinamicamente durante o resgate do convite, atribuição do aplicativo e assim por diante. As atualizações e as gravações em geral ocorrem em uma instância do diretório e devem ser replicadas em todas as instâncias. Observamos que, devido à quantidade finita de tempo que pode ser necessária para concluir a replicação, às vezes podem surgir problemas de autorização. Estamos trabalhando para minimizar ou eliminar esses problemas até o lançamento da versão GA. É pouco provável você encontre um desses problemas até lá, mas caso isso aconteça, atualizar ou tentar novamente deve solucionar o problema.

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
* [Mapeamento de declarações do usuário de colaboração B2B](active-directory-b2b-claims-mapping.md)
* [Compartilhamento externo do Office 365](active-directory-b2b-o365-external-user.md)

