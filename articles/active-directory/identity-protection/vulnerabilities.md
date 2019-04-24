---
title: Vulnerabilidades detectadas pelo Azure Active Directory Identity Protection
description: Visão geral das vulnerabilidades detectadas pelo Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80faf1899da0f16d25b9b7c68cb0edcbc1276632
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60294219"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Vulnerabilidades detectadas pelo Azure Active Directory Identity Protection

Vulnerabilidades são pontos fracos em um ambiente que podem ser explorados por um invasor. É recomendável que os administradores resolver essas vulnerabilidades para melhorar a postura de segurança de sua organização.

![Vulnerabilidades relatadas pelo Identity Protection](./media/vulnerabilities/identity-protection-vulnerabilities.png)

As seções a seguir fornecem uma visão geral das vulnerabilidades relatadas pelo Identity Protection.

## <a name="multi-factor-authentication-registration-not-configured"></a>Registro da autenticação multifator não configurado

Essa vulnerabilidade ajuda a avaliar a implantação de autenticação multifator do Azure em sua organização.

Para exibir a contagem de usuários que não estão registrados para MFA, clique na vulnerabilidade e você será redirecionado para estatísticas em identidade Secure Score.

Autenticação multifator do Azure fornece uma segunda camada de segurança para a autenticação do usuário. Ela ajuda a proteger o acesso a dados e aplicativos ao mesmo tempo que atende à demanda dos usuários para um processo de entrada simples. Autenticação multifator do Azure oferece opções de verificação fácil de usar, como:

* chamada telefônica
* mensagem de texto
* Notificação de aplicativo móvel
* Código de verificação de OTP

Recomendamos exigir a Autenticação Multifator do Azure para entradas de usuário. A autenticação multifator desempenha um papel fundamental nas políticas de acesso condicional baseadas em risco disponíveis por meio do Identity Protection.

Para obter mais informações, consulte [O que é Autenticação Multifator do Microsoft Azure?](../authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>Aplicativos de nuvem não gerenciados

Essa vulnerabilidade ajuda a identificar aplicativos de nuvem não gerenciados na sua organização.

A equipe de TI geralmente não está ciente de todos os aplicativos de nuvem em sua organização. É fácil ver por que os administradores teriam preocupações sobre acesso não autorizado a dados corporativos, perda de dados e outros riscos de segurança.

É recomendável implantar o Cloud Discovery para descobrir aplicativos de nuvem não gerenciados e para gerenciar esses aplicativos usando o Azure Active Directory.

Para obter mais informações, confira [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Alertas de Segurança do Privileged Identity Management

Essa vulnerabilidade ajuda você a descobrir e resolver alertas sobre identidades com privilégios na sua organização.  

Para permitir que os usuários executem operações com privilégios, as organizações precisam oferecer aos usuários acesso privilegiado temporário ou permanente ao Azure AD, a recursos do Azure ou do Office 365 ou a outros aplicativos SaaS. Cada um desses usuários privilegiados aumenta a superfície de ataque da sua organização. Essa vulnerabilidade ajuda você a identificar os usuários com acesso privilegiado desnecessário e tomar as devidas providências para reduzir ou eliminar o risco que eles representam.

Recomendamos as organizações usam o Azure AD Privileged Identity Management para gerenciar, controlar e monitorar identidades com privilégios do AD do Azure, bem como outros serviços online da Microsoft, como o Office 365 ou Microsoft Intune.

Para obter mais informações, confira [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md).

## <a name="see-also"></a>Consulte também

[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
