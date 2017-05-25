---
title: "Diretrizes de licenciamento da colaboração B2B do Azure Active Directory | Microsoft Docs"
description: "A colaboração B2B do Azure Active Directory requer licenças pagas do Azure AD dependendo dos recursos desejados para os usuários da colaboração B2B"
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
ms.date: 05/23/2017
ms.author: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: 4e620f3d76caa25ac0e5afb134f37ffe263935f0
ms.contentlocale: pt-br
ms.lasthandoff: 04/13/2017


---

# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Diretrizes de licenciamento da colaboração B2B do Azure Active Directory

A colaboração do Azure AD (Azure Active Directory) B2B estende um conjunto selecionado de recursos existentes do Azure AD para usuários convidados no locatário do Azure AD. Portanto, os usuários convidados da colaboração B2B do Azure AD serão licenciados por meio de licenças do Azure AD e se alinham às camadas de licença Free, Basic e Premium P1/P2 conforme visto aqui: https://azure.microsoft.com/pricing/details/active-directory/.

Não há nenhum custo para convidar usuários B2B e atribuí-los a um aplicativo no Azure AD. Além disso, até 10 aplicativos por usuário convidado e 3 relatórios básicos também são gratuitos para usuários B2B, uma vez que eles fazem parte da camada “Free” do Azure AD.
Quaisquer recursos do Azure AD pagos, estendidos aos usuários B2B por meio da funcionalidade de colaboração de B2B, precisarão ser licenciados com licenças pagas do Azure AD (Basic, Premium P1 ou Premium P2, dependendo dos recursos que serão usados). O locatário que está convidando obterá cinco direitos de usuário B2B com cada licença paga do Azure AD. Ou seja, cada licença paga do Azure AD que fornece os direitos aos recursos pagos do Azure AD para um usuário funcionário em um locatário também fornecerá os direitos para os mesmos recursos pagos do Azure AD a outros cinco usuários B2B convidados para o locatário.

## <a name="licensing-examples"></a>Exemplos de licenciamento
- Um cliente deseja convidar 100 usuários B2B para seu locatário do Azure AD e usará o provisionamento e o gerenciamento de acesso baseado em Grupo para todos os usuários, mas 50 desses usuários também exigirão MFA e acesso condicional. Aqui, o cliente deve adquirir 10 licenças do Azure AD Basic e 10 licenças do Azure AD Premium P1 para abranger todos os usuários B2B corretamente. Da mesma forma, se um locatário que está convidando planeja usar recursos de Proteção de Identidade com usuários B2B, ele deve ter licenças do Azure AD Premium P2 suficientes para abranger todos esses usuários B2B na proporção de 5:1.
- Um cliente tem 10 funcionários que no momento estão licenciados com Azure AD Premium P1. Agora eles desejam convidar 60 usuários B2B, que exigirão o MFA (Autenticação Multifator). Pela regra de licenciamento de 5:1, o cliente deve ter pelo menos 12 licenças do Azure AD Premium P1 para abranger todos os 60 usuários de colaboração B2B. Como eles já têm 10 licenças Premium P1 para seus 10 funcionários, eles já têm direitos para convidar 50 usuários B2B com recursos Premium P1 como o MFA. Portanto, nesse exemplo, eles precisam comprar mais duas licenças Premium P1 adicionais para abranger os 10 usuários de colaboração B2B restantes.

> [!NOTE]
> Não há nenhum recurso ou necessidade de atribuir licenças aos usuários B2B para habilitar esses direitos de usuário de colaboração B2B.

O cliente que possui o locatário que está convidando deve ser o único a determinar quantos usuários de colaboração B2B precisam de recursos pagos do Azure AD e dependendo de se são recursos do nível Basic, Premium P1 ou Premium P2, os clientes devem ter o número suficiente de licenças pagas adequadas do Azure AD para abranger seus usuários de colaboração B2B na proporção de 5:1. Se uma empresa precisar de direitos de usuário de colaboração B2B adicionais, ela deverá adquirir licenças pagas do Azure AD.

## <a name="additional-licensing-details"></a>Detalhes adicionais do licenciamento
- A colaboração B2B pode fornecer recursos diferentes para usuários de colaboração B2B diferentes com base no modelo existente das edições do Azure AD.
- Cada licença paga do Azure AD incluirá direitos para cinco usuários de colaboração B2B (modelo de 5:1).
- Não é necessário realmente atribuir licenças às contas de usuário B2B. Haverá cálculo e relatórios automáticos.
- Cada usuário convidado obterá os direitos que a edição do Azure AD Free oferece se não houver uma licença paga do Azure AD no locatário.
- Se um usuário de colaboração B2B tiver uma licença paga do Azure AD como um funcionário de sua organização, ele não consumirá uma das licenças de colaboração B2B do locatário que está convidando.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>Discussão avançada: Quais são as considerações de licenciamento quando adicionamos usuários de uma organização de conglomerados como "membros" utilizando suas APIs?
Um usuário convidado B2B é aquele que é convidado de uma organização parceira para trabalhar com a organização anfitriã. Normalmente, qualquer outro caso além desse não se qualifica como B2B, mesmo que use recursos B2B. Vejamos dois casos em particular:

1. Se um anfitrião convida um funcionário usando um endereço de consumidor
  1. Isso não é compatível com nossas políticas de licenciamento e atualmente não é recomendado.

2. Se uma organização anfitriã adiciona um usuário de outra organização de conglomerado
  1. Esse é o caso onde o usuário é convidado usando APIs B2B, mas não é tradicionalmente B2B. Idealmente, deveríamos dispor a essas organizações convidar os usuários de outras organizações como membros (nossa API permite isso). Nesse caso, as licenças devem ser atribuídas a esses membros para que acessem os recursos na organização emissora do convite.

  2. Como uma política, algumas organizações podem querer que os outros usuários da organização sejam adicionados como "Convidado". Há dois casos distintos aqui:
      * A organização de conglomerado já está usando o Azure AD e os usuários convidados estão licenciados na outra organização: nesse caso, não esperamos que os usuários convidados precisem seguir a fórmula 1:5 apresentada anteriormente neste documento. 

      * A organização de conglomerado não está usando o Azure AD ou não tem licenças adequadas: nesse caso, siga a fórmula 1: 5 apresentada anteriormente neste documento.


## <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B?](active-directory-b2b-admin-add-users.md)
* [Como os operadores de informação adicionam usuários de colaboração B2B?](active-directory-b2b-iw-add-users.md)
* [The elements of the B2B collaboration invitation email](active-directory-b2b-invitation-email.md) (Os elementos do email de convite para colaboração B2B)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Solução de problemas de colaboração B2B do Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Perguntas frequentes sobre a colaboração B2B do Azure Active Directory](active-directory-b2b-faq.md)
* [API e personalização da colaboração B2B do Azure Active Directory](active-directory-b2b-api.md)
* [Autenticação multifator para usuários de colaboração B2B](active-directory-b2b-mfa-instructions.md)
* [Adicionar usuários de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

