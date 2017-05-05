---
title: "Portal de inscrição para autoatendimento para colaboração do Azure Active Directory B2B | Microsoft Docs"
description: "A colaboração B2B do Active Directory do Azure dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos corporativos"
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
ms.date: 04/11/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: f629eeb6f12c8785cab2585190f70e98b02fa5b4
ms.lasthandoff: 04/12/2017


---


# <a name="self-service-sign-up-portal-for-azure-ad-b2b-collaboration"></a>Portal de inscrição para autoatendimento para colaboração do Azure AD B2B

Os clientes podem fazer muito com os recursos de produto internos expostos por meio das nossas experiências de administração de TI no [portal do Azure](https://portal.azure.com) e nosso [Painel de acesso do aplicativo](https://myapps.microsoft.com) para não administradores. Mas nós também estamos cientes de que as empresas precisam personalizar o fluxo de trabalho de integração para usuários B2B para se adequar às necessidades da organização deles. Eles podem fazer isso com a [nossa API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Ao discutir isso com muitos de nossos clientes, vimos uma necessidade comum se destacar das outras. Esse foi o caso em que a organização que está convidando pode não saber (ou não querer saber) antes do tempo quem são os colaboradores externos individuais que precisaram acessar seus recursos. Eles queriam uma maneira em que os usuários de empresas parceiras pudessem fazer a própria inscrição com um conjunto de políticas que a organização que está convidando controlava. Isso é possível por meio de nossas APIs – portanto, publicamos um projeto no GitHub que fez exatamente isso: [exemplo de projeto no GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

Nosso projeto no GitHub demonstra como as organizações podem usar as nossas APIs e fornecer uma capacidade de inscrição para autoatendimento baseada em política para seus parceiros confiáveis, com regras que determinam a quais aplicativos eles devem ter acesso. Dessa forma, usuários de parceiros poderão obter acesso aos recursos certos quando eles forem necessários, com segurança, mas sem que qualquer pessoa da organização que está convidando precise integrá-los manualmente. É possível implantar facilmente o projeto com o clique de um botão em uma assinatura do Azure de sua escolha. Experimente!

## <a name="as-is-code"></a>Código no estado em que se encontra

Lembre-se de que esse código é disponibilizado como um exemplo para demonstrar o uso da API de convite do Azure Active Directory B2B. Ele deve ser personalizado pela sua equipe de desenvolvimento ou por um parceiro e deve ser revisado antes de ser implantado em um cenário de produção.

## <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:
* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B?](active-directory-b2b-admin-add-users.md)
* [Como os operadores de informação adicionam usuários de colaboração B2B?](active-directory-b2b-iw-add-users.md)
* [The elements of the B2B collaboration invitation email](active-directory-b2b-invitation-email.md) (Os elementos do email de convite para colaboração B2B)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Licenciamento da colaboração B2B do Azure AD](active-directory-b2b-licensing.md)
* [Solução de problemas de colaboração B2B do Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Perguntas frequentes sobre a colaboração B2B do Azure Active Directory](active-directory-b2b-faq.md)
* [Autenticação multifator para usuários de colaboração B2B](active-directory-b2b-mfa-instructions.md)
* [Adicionar usuários de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
