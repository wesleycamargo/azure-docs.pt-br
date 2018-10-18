---
title: Quais são os fundamentos da identidade e do gerenciamento de acesso do Azure? - Active Directory do Azure | Microsoft Docs
description: Aprenda sobre os recursos avançados de proteção e as ferramentas adicionais disponíveis nas edições do Azure Active Directory Premium.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2018
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: f7baa29c77ae4af9813bfc755a39cc07288a3ad2
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734657"
---
# <a name="what-are-the-fundamentals-of-azure-identity-and-access-management"></a>Quais são os fundamentos da identidade e do gerenciamento de acesso do Azure?
O Azure AD Premium é uma solução de gerenciamento de acesso e identidade baseada em nuvem, com recursos avançados de proteção. Esses recursos avançados ajudam a fornecer uma identidade segura para todos os seus aplicativos, proteção de identidade (aprimorada com o [gráfico de segurança de inteligência da Microsoft](https://www.microsoft.com/security/intelligence)) e [Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md). O Azure AD ajuda a proteger as identidades de seus usuários em tempo real, ajudando você a criar políticas de acesso adaptáveis e baseadas em risco em torno dos dados de sua organização.

Assista a este vídeo curto para obter uma visão geral da proteção e do gerenciamento de identidades do Azure AD:
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

O Azure AD também fornece um conjunto de ferramentas que podem ajudá-lo a proteger, automatizar e gerenciar seu ambiente, incluindo redefinição de senhas, gerenciamento de usuários e grupos e solicitações de aplicativos. O Azure AD também pode ajudá-lo a gerenciar dispositivos de propriedade do usuário e acessar e controlar aplicativos de software como serviço (SaaS).

Para obter mais informações sobre os custos de edições do Azure Active Directory Premium e as ferramentas associadas, consulte [preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>Conectar-se o Active Directory no local com o Azure AD e Office 365
Estenda sua implementação do Active Directory local para a nuvem, integrando seus diretórios locais com o Azure AD por meio do [gerenciamento de identidade híbrida](https://aka.ms/aadframework). [O Azure AD Connect](../connect/active-directory-aadconnect.md) fornece essa integração, oferecendo a seus usuários um acesso único de identidade e logon único (SSO) aos seus recursos locais e seus serviços de nuvem, como o Office 365.

O Azure AD Connect substitui as versões mais antigas das ferramentas de integração de identidades, como DirSync e Azure AD Sync, ajudando a suportar suas necessidades de sincronização de identidades entre o AD local e o Azure AD. A sincronização do Azure AD Connect é ativada por meio de:

- **Sincronização.** Responsável pela criação de usuários, grupos e outros objetos. Também é responsável por garantir que as informações de identidade dos usuários locais correspondam ao que está no Azure AD. A ativação da reversão de senha também ajuda a manter seus diretórios locais sincronizados quando os usuários atualizam senhas no Azure AD.

- Autenticação. A escolha do método de autenticação correto é importante ao configurar sua solução de identidade híbrida do Azure AD. Você pode escolher a autenticação na nuvem (Senha Hash Synchronization / Pass-through Authentication) ou autenticação federada (AD FS) para sua organização. Para obter mais informações sobre as opções disponíveis, consulte [Escolha o método de autenticação correto para a solução de identidade híbrida do Active Directory do Azure](https://aka.ms/auth-options).

- **Monitoramento de integridade.** Azure AD Connect Health fornece monitoramento e um local central no portal do Azure para exibir essa atividade. Para saber mais, confira [Monitorar a infraestrutura de identidade local e os serviços de sincronização na nuvem](../connect-health/active-directory-aadconnect-health.md).

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>Aumentar a produtividade e reduzir os custos de assistência técnica com um únicos e autoatendimento experiências de logon
Os usuários economizam tempo quando eles têm um único nome de usuário e senha, juntamente com uma experiência consistente em todos os dispositivos. Os usuários também economizam tempo executando tarefas de autoatendimento, como [redefinir uma senha esquecida](../user-help/active-directory-passwords-update-your-own-password.md) ou solicitar acesso a um aplicativo sem aguardar assistência do suporte técnico.

Aprimorando o SSO e a experiência consistente, o Azure AD [estende seu Active Directory local](../connect/active-directory-aadconnect.md) para a nuvem, permitindo que seus usuários usem sua conta organizacional primária para seus dispositivos associados a domínio, recursos da empresa e toda a Web e SaaS aplicativos que eles precisam usar para realizar um trabalho. 

Além disso, o acesso a aplicativos pode ser provisionado automaticamente (ou desprovisionado) com base em associações a grupos e status de funcionários de um usuário, ajudando você a controlar o acesso a aplicativos de galeria ou seus próprios aplicativos locais desenvolvidos e publicados por meio do [Proxy de Aplicativo do Azure AD](../manage-apps/application-proxy.md).

## <a name="manage-and-control-access-to-your-organizational-resources"></a>Gerenciar e controlar o acesso aos seus recursos organizacionais
As soluções de gerenciamento de identidade e acesso da Microsoft ajudam você a proteger o acesso a aplicativos e recursos no datacenter da sua organização e na nuvem. Esse gerenciamento de acesso ajuda a fornecer níveis adicionais de validação, como [Autenticação de vários fatores](../authentication/concept-mfa-howitworks.md) e [políticas de acesso condicional](../conditional-access/overview.md). Monitorar atividades suspeitas por meio de relatórios, auditorias e alertas avançados de segurança também pode ajudar a reduzir possíveis problemas de segurança.

O uso de políticas de acesso condicional no Azure AD Premium permite criar regras de acesso baseadas em diretivas para qualquer aplicativo conectado ao AD do Azure, como aplicativos SaaS, aplicativos personalizados em execução na nuvem ou no local ou aplicativos da Web). O Azure AD avalia suas regras em tempo real, aplicando-as sempre que um usuário tentar acessar um aplicativo. As políticas de proteção de identidade do Azure permitem que você execute uma ação automaticamente (bloqueando o acesso, impondo a autenticação de vários fatores ou redefinindo as senhas de usuário) se a atividade suspeita for descoberta.

## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management
[O Privileged Identity Management (PIM)](../privileged-identity-management/pim-getting-started.md), incluído na edição do Azure Active Directory Premium 2, ajuda a descobrir, restringir e monitorar contas administrativas e seu acesso a recursos no Active Directory do Azure e em outros serviços online da Microsoft. O PIM também ajuda a administrar o acesso administrativo sob demanda pelo período exato de tempo necessário, o que significa que você pode permitir que os administradores solicitem a elevação temporária autenticada de seus privilégios por um período de tempo pré-configurado antes que suas contas retornem a um estado de usuário normal.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a arquitetura do Azure AD, consulte [O que é a arquitetura do Azure AD?](active-directory-architecture.md).
