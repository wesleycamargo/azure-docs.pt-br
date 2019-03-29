---
title: Governança de identidade - Azure Active Directory | Microsoft Docs
description: Governança de identidade do Active Directory do Azure permite que você equilibre a necessidade de sua organização de segurança e produtividade dos funcionários com os processos e visibilidade.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7872a43d5000ddb1ba581f76358c8001902ab56b
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579709"
---
# <a name="what-is-azure-ad-identity-governance"></a>O que é a governança de identidade do Azure AD?

A governança de identidade do Azure AD (Azure Active Directory) permite que você equilibre a necessidade da sua organização de segurança e produtividade dos funcionários com os processos e a visibilidade. Ela fornece recursos para garantir que os usuários certos tenham o acesso certo aos recursos certos, e permite que você proteja, monitore e audite o acesso a ativos críticos, ao mesmo tempo garantindo a produtividade dos funcionários.  

A governança de identidade dá às organizações a capacidade de fazer o seguinte entre funcionários, parceiros empresariais e fornecedores, além de serviços e aplicativos:

- Controlar o ciclo de vida de identidade
- Controlar o ciclo de vida de acesso
- Administração segura

Especificamente, destina-se a ajudar as organizações a abordar estas quatro perguntas importantes:

- Quais usuários devem ter acesso a quais recursos?
- O que esses usuários estão fazendo com esse acesso?
- Existem controles organizacionais em vigor para gerenciar o acesso?
- Auditores podem verificar se os controles estão funcionando?

## <a name="identity-lifecycle"></a>Ciclo de vida de identidade

Governança de identidade ajuda as organizações a atingir um equilíbrio entre *produtividade* – Com que rapidez uma pessoa pode ter acesso aos recursos de que precisa, por exemplo, quando entra em minha organização? E *segurança* – Como o acesso deve mudar ao longo do tempo, como devido a alterações ao status de emprego da pessoa?  O gerenciamento do ciclo de vida de identidades é a base para governança de identidade, e governança efetiva em escala requer modernizar a infraestrutura de gerenciamento do ciclo de vida de identidades para aplicativos.

Para muitas organizações, o ciclo de vida de identidade para funcionários está vinculado à representação daquele usuário em um sistema HCM (gerenciamento de capital humano).  O Azure AD Premium mantém automaticamente as identidades do usuário para as pessoas representadas no Workday tanto no Active Directory quanto no Azure Active Directory, conforme descrito no [tutorial de provisionamento de entrada do Workday (versão prévia)](../saas-apps/workday-inbound-tutorial.md).  O Azure AD Premium também inclui o [Microsoft Identity Manager](/microsoft-identity-manager/), que pode importar registros de sistemas HCM locais como SAP, Oracle eBusiness e Oracle PeopleSoft.

Cada vez mais, os cenários exigem a colaboração com pessoas de fora da sua organização. A colaboração [B2B do Azure AD](/azure/active-directory/b2b/) permite compartilhar com segurança aplicativos e serviços da sua organização com usuários convidados e parceiros externos de qualquer organização, ao mesmo tempo mantendo o controle sobre seus próprios dados corporativos.

## <a name="access-lifecycle"></a>Ciclo de vida de acesso

As organizações precisam de um processo para gerenciar o acesso além do que foi inicialmente provisionado para um usuário quando a identidade do usuário foi criada.  Além disso, organizações empresariais precisam ser capazes de dimensionar com eficiência para poderem desenvolver e impor política de acesso e controles continuamente.

Em geral, a IT delega as decisões de aprovação de acesso aos tomadores de decisão empresariais.  Além disso, a IT pode envolver os próprios usuários.  Por exemplo, os usuários que acessam os dados confidenciais do cliente no aplicativo de marketing da empresa na Europa precisam conhecer as políticas da empresa. Usuários convidados podem não estar cientes dos requisitos de manipulação para os dados em uma organização para a qual foram convidados.

As organizações podem automatizar o processo de ciclo de vida de acesso por meio de tecnologias como [grupos dinâmicos](../users-groups-roles/groups-dynamic-membership.md), junto com o provisionamento de usuário para [aplicativos SaaS](../saas-apps/tutorial-list.md) ou [aplicativos integrados ao SCIM](../manage-apps/use-scim-to-provision-users-and-groups.md).  As organizações também podem controlar quais [usuários convidados têm acesso a aplicativos locais](../b2b/hybrid-cloud-to-on-premises.md).  Esses direitos de acesso podem então ser revisados regularmente usando [revisões de acesso do Azure AD](access-reviews-overview.md) recorrentes.

Quando um usuário tenta acessar aplicativos, o Azure AD impõe políticas de [acesso condicional](/azure/active-directory/conditional-access/). Por exemplo, políticas de acesso condicional podem incluir a exibição de [termos de uso](active-directory-tou.md) e [garantir que o usuário tenha concordado com esses termos](../conditional-access/require-tou.md) antes de poder acessar um aplicativo.

## <a name="privileged-access-lifecycle"></a>Ciclo de vida de acesso privilegiado

Historicamente, o acesso privilegiado é descrito por outros fornecedores como uma funcionalidade separada do controle de identidade. No entanto, na Microsoft, acreditamos que controlar o acesso privilegiado é uma parte importante da governança de identidade, especialmente considerando o potencial de estrago que o uso indevido associado a esses direitos de administrador pode causar a uma organização. Funcionários, fornecedores e prestadores de serviço que assumem direitos administrativos precisam ser controlados.

O Azure AD PIM (Privileged Identity Management) fornece controles adicionais adaptados para proteger direitos de acesso para recursos, entre o Azure AD, o Azure e outros serviços Online da Microsoft.  O acesso Just-In-Time e as funcionalidades de alerta de mudança de função oferecidos pelo Azure AD PIM, além da autenticação multifator e do acesso condicional, fornecem um conjunto abrangente de controles de governança para ajudar a proteger recursos da empresa (diretório, Office 365 e funções de recurso do Azure). Assim como acontece com outras formas de acesso, as organizações podem usar revisões de acesso para configurar a recertificação recorrente de acesso para todos os usuários nas funções de administrador.

## <a name="getting-started"></a>Introdução

Embora não haja nenhuma solução nem recomendação perfeita para todos os clientes, as configurações a seguir fornecem uma orientação quanto a quais políticas de linha de base a Microsoft recomenda para garantir uma força de trabalho mais segura e produtiva.

- [Configurações de acesso à identidade e ao dispositivo](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Protegendo o acesso privilegiado](../users-groups-roles/directory-admin-roles-secure.md)


### <a name="access-reviews"></a>Análises de acesso

- [O que é revisão de acesso?](access-reviews-overview.md)
- [Gerenciar o acesso do usuário com revisões de acesso](manage-user-access-with-access-reviews.md)
- [Gerenciar o acesso do convidado com revisões de acesso](manage-guest-access-with-access-reviews.md)
- [Iniciar uma revisão de acesso de funções do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)

### <a name="terms-of-use"></a>Termos de uso

- [O que posso fazer com os Termos de uso?](active-directory-tou.md)

### <a name="privileged-identity-management"></a>Privileged Identity Management

- [O que é o PIM do Azure AD?](../privileged-identity-management/pim-configure.md)
