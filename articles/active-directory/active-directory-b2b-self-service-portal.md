---
title: Portal de inscrição para autoatendimento para colaboração do Azure Active Directory B2B | Microsoft Docs
description: A colaboração B2B do Active Directory do Azure dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos corporativos
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/08/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: b16f7c040212db6d0dbeb7161a18f205cf524090
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930428"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Portal de autoatendimento para inscrição na colaboração B2B do Azure AD

Os clientes podem fazer muito com os recursos internos expostos por meio do [Portal do Azure](https://portal.azure.com) e o [Painel de Acesso de Aplicativos](https://myapps.microsoft.com) para usuários finais. Porém, você pode precisar personalizar o fluxo de trabalho de integração para usuários B2B para se adequar às necessidades da sua organização. Você pode fazer isso com [a API de convite](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Como uma organização que está convidando pode não saber antecipadamente quem são os colaboradores externos individuais que precisam de acesso aos seus recursos. Você precisará de uma maneira para os usuários de empresas parceiras inscreverem-se com um conjunto de políticas que você, como organização convidando, controla. Este cenário é possível por meio de APIs. Há um [projeto de exemplo no GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) que faz exatamente isso.

Esse projeto no Github mostra como as organizações podem usar as APIs e fornecer uma capacidade de inscrição para autoatendimento baseada em política para seus parceiros confiáveis, com regras que determinam os aplicativos que podem acessar. Os usuários do parceiro podem obter acesso aos recursos quando necessário. Podem fazer isso com segurança, sem a necessidade de convidar a organização para convidá-los manualmente. É possível implantar facilmente o projeto em uma assinatura do Azure de sua escolha.

## <a name="as-is-code"></a>Código no estado em que se encontra

Esse código é disponibilizado como um exemplo para demonstrar o uso da API de convite do Azure Active Directory B2B. Ele deve ser personalizado por sua equipe de desenvolvimento ou um parceiro e deverá ser revisado antes de você implantar em um cenário de produção.

## <a name="next-steps"></a>Próximas etapas

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Licenciamento da colaboração B2B do Azure AD](active-directory-b2b-licensing.md)
* [Perguntas frequentes sobre a colaboração B2B do Azure Active Directory](active-directory-b2b-faq.md)