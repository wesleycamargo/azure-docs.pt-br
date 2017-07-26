---
title: "Diretrizes de licenciamento da colaboração B2B do Azure Active Directory | Microsoft Docs"
description: "A colaboração do Azure do Azure Active Directory B2B não exige licenças pagas do Azure AD, mas você pode também obter recursos pagos para usuários convidados de B2B"
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
ms.date: 06/29/2017
ms.author: curtand
ms.reviewer: sasubram
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 972b37e0009b3096691784d785901b0a5585eb4c
ms.contentlocale: pt-br
ms.lasthandoff: 06/30/2017


---

# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Diretrizes de licenciamento da colaboração B2B do Azure Active Directory

Use a colaboração B2B para convidar usuários convidados em seu locatário do Azure AD para ter acesso a serviços e recursos do Azure AD que você disponibilizar.  

Não há nenhum custo para convidar usuários B2B e atribuí-los a um aplicativo no Azure AD. Até 10 aplicativos por usuário convidado e 3 relatórios básicos também são gratuitos para usuários de colaboração B2B. Se o usuário convidado tiver licenças relevantes atribuídas no locatário do Azure AD do parceiro, elas também estarão licenciadas no seu.

Se você desejar fornecer acesso a recursos pagos do Azure AD, os usuários convidados B2B deverão ser licenciados com licenças adequadas do Azure AD. Um locatário que está convidando com uma licença paga do Azure AD pode atribuir direitos de usuário de colaboração B2B a outros cinco usuários convidados adicionais para o locatário. 

## <a name="licensing-examples"></a>Exemplos de licenciamento
- Um cliente deseja convidar 100 usuários de colaboração B2B para seu locatário do Azure AD. O cliente atribui o gerenciamento de acesso e o provisionamento de todos os usuários, mas 50 usuários também exigem a MFA e o acesso condicional. Esse cliente deve comprar 10 licenças do Azure AD Basic e 10 licenças do Azure AD Premium P1 para cobrir esses usuários B2B corretamente. Se o cliente planejar usar recursos de proteção de identidade com usuários B2B, eles deverão ter licenças do Azure AD Premium P2 para cobrir os usuários convidados na mesma proporção de 5:1.
- Um cliente tem 10 funcionários que no momento estão licenciados com Azure AD Premium P1. Agora eles desejam convidar 60 usuários B2B, que exigirão a MFA (autenticação multifator). Pela regra de licenciamento de 5:1, o cliente deve ter pelo menos 12 licenças do Azure AD Premium P1 para cobrir os 60 usuários de colaboração B2B. Como eles já têm 10 licenças Premium P1 para seus 10 funcionários, eles já têm direito de convidar 50 usuários B2B com recursos Premium P1 como a MFA. Neste exemplo, em seguida, eles compram 2 licenças Premium P1 adicionais para cobrir os 10 usuários de colaboração B2B restantes.

> [!NOTE]
> Ainda não é possível atribuir licenças diretamente aos usuários B2B para habilitar esses direitos de usuário de colaboração B2B.

O cliente que possui o locatário que está convidando será aquele que determinará quantos usuários de colaboração B2B precisam de recursos pagos do Azure AD. Dependendo de quais recursos pagos do Azure AD você desejar para os usuários convidados, você deverá ter licenças pagas do Azure AD suficientes para cobrir os usuários de colaboração B2B na proporção de 5:1. 

## <a name="additional-licensing-details"></a>Detalhes adicionais do licenciamento
- Não é necessário realmente atribuir licenças às contas de usuário B2B. Com base na proporção de 5:1, o licenciamento é calculado e relatado automaticamente.
- Cada usuário convidado obterá os direitos que a edição do Azure AD Gratuito oferece se não houver nenhuma licença paga do Azure AD no locatário.
- Se um usuário de colaboração B2B já tiver uma licença paga do Azure AD de sua organização, ele não consumirá uma das licenças de colaboração B2B do locatário que está convidando.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>Discussão avançada: Quais são as considerações de licenciamento quando adicionamos usuários de uma organização de conglomerados como "membros" utilizando suas APIs?
Um usuário convidado B2B é aquele que é convidado de uma organização parceira para trabalhar com a organização anfitriã. Normalmente, nenhum outro caso além desse é qualificado como B2B, embora use recursos B2B. Vejamos dois casos em particular:

1. Se um anfitrião convida um funcionário usando um endereço de consumidor
  1. Este cenário não está em conformidade com nossas políticas de licenciamento e não é recomendado.

2. Se uma organização anfitriã adiciona um usuário de outra organização de conglomerado
  1. Nesse caso, o usuário é convidado usando APIs de B2B, mas neste caso, não é tradicionalmente B2B. Idealmente, deveríamos dispor a essas organizações convidar os usuários de outras organizações como membros (nossa API permite isso). Nesse caso, as licenças devem ser atribuídas a esses membros para que acessem os recursos na organização emissora do convite.

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

