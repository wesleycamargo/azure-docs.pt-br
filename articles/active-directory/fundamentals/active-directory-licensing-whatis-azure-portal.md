---
title: O que é o licenciamento baseado em grupo no Azure Active Directory? | Microsoft Docs
description: Aprenda sobre o licenciamento baseado em grupo do Azure Active Directory, incluindo como ele funciona e as melhores práticas.
services: active-directory
keywords: Licenciamento do AD do Azure
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 3f23b28c1b20155e50fddf17db90cd2a53c04855
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209812"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>O que é o licenciamento baseado em grupo no Azure Active Directory?

Os serviços de nuvem pagos da Microsoft, como Office 365, Enterprise Mobility + Security, Dynamics 365 e outros produtos similares, exigem licenças. Essas licenças são atribuídas a cada usuário que precisa acessar esses serviços. Para gerenciar licenças, os administradores usam um dos portais de gerenciamento (Office ou Azure) e cmdlets do PowerShell. O Azure AD (Azure Active Directory) é a infraestrutura subjacente que dá suporte ao gerenciamento de identidade para todos os serviços de nuvem da Microsoft. O Azure AD armazena informações sobre estados de atribuição de licença para usuários.

Até agora, as licenças podiam ser atribuídas apenas no nível de usuário individual, o que pode dificultar o gerenciamento em larga escala. Por exemplo, para adicionar ou remover licenças de usuário com base nas alterações organizacionais, como usuários ingressando na organização ou em um departamento ou saindo deles, com frequência um administrador deve escrever um script complexo do PowerShell. Esse script faz chamadas individuais para o serviço de nuvem.

Para enfrentar esses desafios, o Azure AD agora inclui licenciamento baseado em grupo. Você pode atribuir uma ou mais licenças de produto a um grupo. O Azure AD faz com que as licenças sejam atribuídas a todos os membros do grupo. Os novos membros que ingressam no grupo recebem as licenças apropriadas. Quando eles deixam o grupo, essas licenças são removidas. Isso elimina a necessidade de automatizar o gerenciamento de licenças por meio do PowerShell para refletir as alterações na estrutura da organização e dos departamentos por usuário.


## <a name="features"></a>Recursos

Aqui estão os principais recursos do licenciamento baseado em grupo:

- As licenças podem ser atribuídas a qualquer grupo de segurança no Azure AD. Os grupos de segurança podem ser sincronizados localmente usando o Azure AD Connect. Você também pode criar grupos de segurança diretamente no Azure AD (também chamados de grupos somente de nuvem) ou automaticamente por meio do recurso de grupo dinâmico do Azure AD.

- Quando uma licença de produto é atribuída a um grupo, o administrador pode desabilitar um ou mais planos de serviço no produto. Normalmente, isso é feito quando a organização ainda não está pronta para começar a usar um serviço incluído em um produto. Por exemplo, o administrador pode atribuir o Office 365 a um departamento, mas desabilitar o serviço do Yammer temporariamente.

- Todos os serviços de nuvem da Microsoft que exigem licenciamento no nível do usuário têm suporte. Isso inclui todos os produtos do Office 365, Enterprise Mobility + Security e Dynamics 365.

- Atualmente, o licenciamento baseado em grupo está disponível apenas por meio [do portal do Azure](https://portal.azure.com). Se você usa outros portais de gerenciamento principalmente para gerenciamento de usuário e de grupo, como o portal do Office 365, pode continuar a fazê-lo. Mas você deve usar o portal do Azure para gerenciar licenças no nível do grupo.

- O Azure AD gerencia automaticamente as modificações de licença que resultam das alterações de associação de grupo. Normalmente, as modificações de licença entram em vigor dentro de minutos após uma alteração de associação.

- Um usuário pode ser membro de vários grupos com políticas de licença especificadas. Um usuário também pode ter algumas licenças que foram atribuídas diretamente, fora de todos os grupos. O estado do usuário resultante é uma combinação de todas as licenças de produto e de serviço atribuídas. Se um usuário receber a mesma licença de várias fontes, a licença será consumida apenas uma vez.

- Em alguns casos, as licenças não podem ser atribuídas a um usuário. Por exemplo, talvez não haja licenças suficientes disponíveis no locatário ou serviços conflitantes foram atribuídos ao mesmo tempo. Os administradores têm acesso às informações sobre os usuários que não puderam receber licenças de grupo do Azure AD. Eles podem agir corretivamente com base nessas informações.

- É necessário ter uma assinatura paga ou de avaliação do Azure AD Basic, ou do Office 365 Enterprise E3, Office 365 A3 e edições posteriores, no locatário para usar o gerenciamento de licenças baseado em grupo. Esse recurso exige licença para cada usuário exclusivo que seja membro dos grupos que receberam a licença. Você não precisa atribuir licenças aos usuários para que eles sejam membros de grupos que receberam licenças, mas é necessário ter o número mínimo de licenças no locatário para cobrir todos esses usuários. Por exemplo, se você tivesse o total de 1.000 usuários exclusivos em todos os grupos com licenças atribuídas no locatário, precisaria de pelo menos 1.000 licenças para atender ao requisito de licença.

## <a name="your-feedback-is-welcome"></a>Agradecemos seus comentários!

Se você tiver comentários ou solicitações de recurso, compartilhe-os conosco usando [este fórum de administradores do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre outros cenários de gerenciamento de licenças por meio de licenciamento baseado em grupo, confira:

* [Atribuição de licenças a um grupo no Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
* [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Como migrar usuários individuais licenciados para licenciamento baseado em grupo no Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
* [Como migrar usuários entre licenças de produto usando o licenciamento baseado em grupo no Microsoft Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)
* [Exemplos do PowerShell para licenciamento baseado em grupo no Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
