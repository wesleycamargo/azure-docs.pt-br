---
title: "Limitações atuais na colaboração B2B do Azure Active Directory | Microsoft Docs"
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
ms.sourcegitcommit: 0c07c842ba8c6214d6746b0361af7b416069a6f5
ms.openlocfilehash: 4dae74740d8fd00d36492e5a9fad8848b5d03952
ms.lasthandoff: 02/17/2017


---

# <a name="azure-active-directory-b2b-collaboration-current-limitations"></a>Limitações atuais na colaboração B2B do Azure Active Directory

1. As APIs de convite de colaboração B2B do Azure Active Directory (Azure AD) estão em versão Beta de teste Essa superfície da API é a que deve ser seguida, no entanto, como toda versão beta, ela está sujeita ao contrato de namespace de beta. Mudaremos a API para uma versão numerada com nossa versão de lançamento.
2. A possibilidade de MFA (autenticação multifator) dupla. Se o seu parceiro já tiver uma política de MFA em vigor, a MFA da colaboração B2B será executada e gerenciada na organização que convida. Isso é desejável, pois cobre todas as identidades e permite que você tenha controle sobre o nível de autenticação dos convidados de colaboração B2B. No entanto, se você tiver um parceiro que já possui a MFA configurada e aplicá-la, talvez seus precisem realizar a MFA uma vez na organização inicial e novamente em sua organização. Em versões futuras, apresentaremos uma política na qual você pode optar por confiar na MFA de certos parceiros a fim de evitar o problema de MFA dupla.
3. Instantâneo Ativado nos fluxos de colaboração B2B, adicionamos usuários ao diretório e os atualizamos dinamicamente durante o resgate do convite, atribuição do aplicativo e assim por diante. As atualizações e as gravações geralmente ocorrem em uma instância do diretório e precisam ser replicadas em todas as instâncias. Observamos que, em alguns casos, a quantidade finita de tempo que pode demorar para concluir a replicação pode resultar em problemas manifestados como problemas de autorização. Estamos trabalhando para minimizar/eliminar esse conjunto de problemas antes da Disponibilidade Geral. Nesse ínterim, isso não deve ser um problema para você, mas se for, atualizar ou repetir normalmente ajudará.

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

