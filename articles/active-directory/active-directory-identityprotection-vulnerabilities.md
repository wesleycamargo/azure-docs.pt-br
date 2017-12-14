---
title: Vulnerabilidades detectadas pelo Azure Active Directory Identity Protection | Microsoft Docs
description: "Visão geral das vulnerabilidades detectadas pelo Azure Active Directory Identity Protection."
services: active-directory
keywords: "azure active directory identity protection, cloud app discovery, gerenciamento de aplicativos, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 92233a5b-cb34-4d28-88cc-d5d29c0f3256
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: ce3d50c4c111c2a47b2732cb3a39bf0dfc28853e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Vulnerabilidades detectadas pelo Azure Active Directory Identity Protection
Vulnerabilidades são pontos fracos no seu ambiente que podem ser explorados por um invasor. É recomendável resolver essas vulnerabilidades para melhorar a postura de segurança de sua organização e impedir que invasores possam explorá-las.


![vulnerabilidades](./media/active-directory-identityprotection-vulnerabilities/101.png "vulnerabilidades")



As seções a seguir fornecem uma visão geral das vulnerabilidades relatadas pelo Identity Protection.

## <a name="multi-factor-authentication-registration-not-configured"></a>Registro de autenticação multifator não configurado
Essa vulnerabilidade ajuda a controlar a implantação da Autenticação Multifator do Azure na sua organização. 

A Azure Multi-Factor Authentication fornece uma segunda camada de segurança para a autenticação do usuário. Ela ajuda a proteger o acesso a dados e aplicativos ao mesmo tempo que atende à demanda dos usuários para um processo de entrada simples. Ele fornece autenticação forte por meio de uma variedade de opções de fácil verificação — chamada telefônica, mensagem de texto, notificação de aplicativo móvel ou verificação de código e tokens OATH de terceiros.

Recomendamos exigir a Autenticação Multifator do Azure para entradas de usuário. A autenticação multifator desempenha um papel fundamental nas políticas de acesso condicional baseadas em risco disponíveis por meio do Identity Protection.

Para obter mais detalhes, veja [O que é a Autenticação Multifator do Azure?](../multi-factor-authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>Aplicativos de nuvem não gerenciados
Essa vulnerabilidade ajuda a identificar aplicativos de nuvem não gerenciados na sua organização.

Nas empresas modernas, os departamentos de TI geralmente não estão cientes de todos os aplicativos de nuvem que os membros da sua organização usam para realizar seu trabalho. É fácil ver por que os administradores teriam preocupações sobre o acesso não autorizado aos dados corporativos, perda de dados e outros riscos de segurança. 

Recomendamos que sua organização implante o Cloud App Discovery para descobrir aplicativos de nuvem não gerenciados e gerencie esses aplicativos usando o Azure Active Directory.

Para obter mais detalhes, veja [Encontrando aplicativos de nuvem não gerenciados com o Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).

## <a name="security-alerts-from-privileged-identity-management"></a>Alertas de Segurança do Privileged Identity Management
Essa vulnerabilidade ajuda você a descobrir e resolver alertas sobre identidades com privilégios na sua organização.  

Para permitir que os usuários executem operações com privilégios, as organizações precisam oferecer aos usuários acesso privilegiado temporário ou permanente ao Azure AD, a recursos do Azure ou do Office 365 ou a outros aplicativos SaaS. Cada um desses usuários privilegiados aumenta a superfície de ataque da sua organização. Essa vulnerabilidade ajuda você a identificar os usuários com acesso privilegiado desnecessário e tomar as devidas providências para reduzir ou eliminar o risco que eles representam. 

Recomendamos que sua organização use o Azure AD Privileged Identity Management para gerenciar, controlar e monitorar as identidades com privilégios e o acesso delas a recursos no Azure AD e em outros serviços online da Microsoft como o Office 365 ou o Microsoft Intune.

Para obter mais detalhes, veja [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 

## <a name="see-also"></a>Confira também
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

