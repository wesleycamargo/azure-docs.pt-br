---
title: Como configurar a política de registro de autenticação multifator no Azure Active Directory Identity Protection| Microsoft Docs
description: Saiba como configurar a política de registro de autenticação multifator do Azure AD Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f4083ddf849842358f7699badca6598e56e4dee
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65139376"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Como: Configurar a política de registro de autenticação multifator do Azure

O Azure AD Identity Protection ajuda a gerenciar a disponibilização de registro da autenticação multifator (MFA), configurando uma política de acesso condicional para exigir o registro MFA independente do aplicativo que você está tentando entrar. Este artigo explica o que a política pode ser usada para e como configurá-lo.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Qual é a política de registro de autenticação multifator do Azure?

Autenticação multifator do Azure fornece um meio para verificar quem você está usando mais do que apenas um nome de usuário e senha. Ele fornece uma segunda camada de segurança para logons de usuário. Em ordem para que os usuários possam responder a prompts do MFA, eles devem primeiro registrar para autenticação multifator do Azure.

É recomendável que você exija autenticação multifator do Azure para entradas do usuário porque ele:

- fornece autenticação forte com uma variedade de opções de verificação fácil
- Desempenha um papel fundamental na preparação de sua organização para proteger e recuperar eventos de risco do Identity Protection

Para obter mais detalhes sobre a MFA, consulte [o que é autenticação multifator do Azure?](../authentication/howto-mfa-getstarted.md)

## <a name="how-do-i-access-the-registration-policy"></a>Como fazer para acessar a política de registro?

A política de risco de MFA encontra-se na seção **Configurar** na página do [Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).

![Política do MFA](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>Configurações de política

Quando você configura a política de registro MFA, você precisa fazer as seguintes alterações de configuração:

- Os usuários e grupos que a política se aplica. Lembre-se de excluir da sua organização [contas de acesso de emergência](../users-groups-roles/directory-emergency-access.md).

    ![Usuários e grupos](./media/howto-mfa-policy/11.png)

- O controle que você deseja impor - **registro exigir MFA do Azure**

    ![Access](./media/howto-mfa-policy/12.png)

- Impor a política deve ser definida como **em**.

    ![Impor a política](./media/howto-mfa-policy/14.png)

- **Salvar** sua política

## <a name="user-experience"></a>Experiência do usuário

Para obter uma visão geral da experiência do usuário relacionada, confira:

- [Fluxo do registro de autenticação multifator](flows.md#multi-factor-authentication-registration).  
- [Experiências de entrada com o Azure AD Identity Protection](flows.md).  

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral do Azure AD Identity Protection, veja a [Visão geral do Azure AD Identity Protection](overview.md).
