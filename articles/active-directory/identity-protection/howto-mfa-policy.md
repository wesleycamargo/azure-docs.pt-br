---
title: Como configurar a política de registro de autenticação multifator no Azure Active Directory Identity Protection| Microsoft Docs
description: Saiba como configurar a política de registro de autenticação multifator do Azure AD Identity Protection.
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gerenciamento de aplicativos, segurança, risco, nível de risco, vulnerabilidade, política de segurança
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: b563b0fb8dc12cbdcdd256a97134f9ff02988915
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698955"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>Como: configurar a política de registro de autenticação multifator

O Azure AD Identity Protection ajuda a gerenciar a disponibilização de registro da autenticação multifator (MFA), configurando uma política para exigir o registro de MFA. Este artigo explica para o que a política pode ser usada e como configurá-la.

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>O que é a política de registro de autenticação multifator?

A autenticação multifator do Azure é um método de verificar quem você é e que requer o uso de mais do que apenas um nome de usuário e uma senha. Ele fornece uma segunda camada de segurança para logons de usuário e transações. Em ordem para que os usuários possam responder a prompts do MFA, eles devem primeiro registrar para MFA. 

Recomendamos exigir a autenticação multifator do Azure para entradas de usuário porque:

- fornece autenticação forte com uma variedade de opções de verificação fácil

- desempenha um papel fundamental na preparação de sua organização para proteger e recuperar comprometimentos de conta


Para obter mais detalhes sobre a MFA, consulte [o que é autenticação multifator do Azure?](../authentication/multi-factor-authentication.md)


## <a name="how-do-i-access-the-mfa-registration-policy"></a>Como fazer para acessar a política de registro de MFA?
   
A política de risco de MFA encontra-se na seção **Configurar** na página do [Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Política do MFA](./media/howto-mfa-policy/1014.png)




## <a name="policy-settings"></a>Configurações de política

Quando você configura a política de registro MFA, você precisa definir:

- Os usuários e grupos aos quais a política se aplica:

    ![Usuários e grupos](./media/howto-mfa-policy/11.png)

- O controle que você deseja impor-requisito de registro de MFA:  

    ![Access](./media/howto-mfa-policy/12.png)

- O estado da sua política:

    ![Impor a política](./media/howto-mfa-policy/14.png)


A caixa de diálogo de configuração de política fornece uma opção para estimar o impacto da sua configuração.

![Impacto estimado](./media/howto-mfa-policy/15.png)




## <a name="user-experience"></a>Experiência do usuário


Para obter uma visão geral da experiência do usuário relacionada, confira:

* [Fluxo do registro de autenticação multifator](flows.md#multi-factor-authentication-registration).  
* [Experiências de entrada com o Azure AD Identity Protection](flows.md).  



## <a name="next-steps"></a>Próximos passos

Para obter uma visão geral do Azure AD Identity Protection, veja a [Visão geral do Azure AD Identity Protection](overview.md).
