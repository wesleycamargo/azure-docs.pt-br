---

title: "O que é o licenciamento baseado em grupo no Azure Active Directory? | Microsoft Docs"
description: "Descrição de licenciamento baseado em grupo do Azure Active Directory, como ele funciona, como começar e práticas recomendadas"
services: active-directory
keywords: Licenciamento do AD do Azure
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f8b63e5831897d3a45298b0415bb2d6d44ab0de1
ms.openlocfilehash: 0591b536a9be901085074f4b49fc65b097382835
ms.lasthandoff: 03/01/2017


---

# <a name="group-based-licensing-basics-in-azure-active-directory"></a>Noções básicas de licenciamento baseado em grupo no Azure Active Directory

Os serviços de nuvem da Microsoft, como o Office 365 Enterprise Mobility + Security, Dynamics CRM e outros produtos similares exigem licenças a serem atribuídas a cada usuário que precisar acessar esses serviços. O gerenciamento de licenças é exposto aos administradores por meio de portais de gerenciamento (Office, Azure) e cmdlets do PowerShell. O estado de atribuição de licença é armazenado no Azure Active Directory – a infraestrutura subjacente com suporte a gerenciamento de identidade para todos os serviços de nuvem da Microsoft.

Até agora, as licenças podiam ser atribuídas apenas no nível de usuário individual, o que pode dificultar o gerenciamento em larga escala para os nossos clientes. Por exemplo, para adicionar ou remover licenças de usuário com base nas alterações organizacionais, como usuários ingressando na organização ou em um departamento ou saindo deles, com frequência um administrador deve escrever um script complexo do PowerShell para fazer chamadas individuais para o serviço de nuvem.

Para enfrentar esses desafios, apresentamos um novo recurso do sistema de gerenciamento de licença do Azure AD: licenciamento baseado em grupo. Agora é possível atribuir uma ou mais licenças de produto a um grupo. O Azure AD irá assegurar que as licenças sejam atribuídas a todos os membros do grupo. Qualquer novo membro que ingresse no grupo terá atribuídas as licenças apropriadas e quando ele deixar o grupo, as licenças serão removidas. Isso elimina a necessidade de automatizar o gerenciamento de licenças por meio do PowerShell para refletir as alterações na estrutura da organização e dos departamentos por usuário.

## <a name="features"></a>Recursos

Aqui estão os principais recursos do recurso de licenciamento baseado em grupo:

- As licenças podem ser atribuídas a qualquer grupo de segurança no Azure AD. Os grupos de segurança podem ser sincronizados no local usando o Azure AD Connect, criados diretamente no Azure AD (também chamado de grupos somente de nuvem) ou criados automaticamente por meio do recurso Grupo Dinâmico do Azure AD.

- Quando uma licença de produto é atribuída a um grupo, o administrador pode desabilitar um ou mais planos de serviço no produto. Normalmente, isso é feito quando a organização ainda não está pronta para começar a usar um serviço incluído em um produto, por exemplo, o administrador quer atribuir produtos do Office 365 E3 a um departamento mas desabilitar temporariamente o serviço do Yammer Enterprise.

- Todos os serviços de nuvem da Microsoft que exigem licenciamento no nível do usuário têm suporte. Isso inclui todos os produtos do Office 365, Enterprise Mobility + Security, Dynamics CRM etc.

- Atualmente, o licenciamento baseado em grupo está disponível apenas por meio [do portal do Azure](https://portal.azure.com). Os clientes que usam principalmente outros portais de gerenciamento para o gerenciamento de usuários e grupos, como o portal do Office 365, podem continuar a fazer isso, mas precisarão usar o portal do Azure para gerenciar licenças no nível do grupo.

- O Azure AD gerencia automaticamente as modificações de licença resultantes das alterações de associação de grupo. Normalmente, um usuário ingressando em um grupo ou saindo dele terá suas licenças modificadas minutos depois da alteração de associação.

- Um usuário pode ser um membro de vários grupos com políticas de licença especificadas; ele também pode ter algumas licenças que foram atribuídas diretamente ao usuário fora de todos os grupos. O estado do usuário resultante é uma combinação de todas as licenças de produto e de serviço atribuídas.

- Em alguns casos, as licenças não podem ser atribuídas a um usuário; por exemplo, porque não há licenças disponíveis suficientes no locatário ou porque serviços conflitantes foram atribuídos ao mesmo tempo. Os administradores têm acesso a informações sobre os usuários para os quais o Azure AD não pôde totalmente processar licenças do grupo; eles podem tomar uma ação corretiva com base nessas informações.

- Durante a visualização pública, uma assinatura paga ou de avaliação para o Azure AD Basic ou superior é necessária no locatário para usar o gerenciamento de licenças baseado em grupo. Além disso, cada usuário que herdar licenças de grupos deverá ter a licença da edição paga do Azure AD atribuída a ele.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre outros cenários de gerenciamento de licenças por meio de licenciamento baseado em grupo, leia

* [Atribuição de licenças a um grupo no Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Como migrar usuários individuais licenciados para licenciamento baseado em grupo no Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory](active-directory-licensing-group-advanced.md)

