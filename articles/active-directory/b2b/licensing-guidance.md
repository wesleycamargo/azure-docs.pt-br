---
title: Diretrizes - Azure Active Directory de licenciamento de colaboração de B2B | Microsoft Docs
description: A colaboração do Azure do Azure Active Directory B2B não exige licenças pagas do Azure AD, mas você pode também obter recursos pagos para usuários convidados de B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fa01a6bf522061e54e9622cb9201f81c699a8ec
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668445"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Diretrizes de licenciamento da colaboração B2B do Azure Active Directory

Com a colaboração do Azure AD (Azure Active Directory) B2B (entre empresas), você pode convidar usuários externos (ou "usuários convidados") para usar seus serviços pagos do Azure AD. Para cada licença paga do Azure AD que você atribui a um usuário, você pode convidar até cinco usuários convidados por meio da provisão de usuário externo.

O licenciamento do usuário convidado B2B é calculado automaticamente e relatado com base na proporção 1:5. Atualmente, não é possível atribuir licenças de usuário convidado B2B diretamente aos usuários convidados.

Além disso, os usuários convidados podem usar os recursos gratuitos do Azure AD sem requisitos adicionais de licenciamento. Os usuários convidados têm acesso aos recursos gratuitos do Azure AD, mesmo quando você não tem nenhuma licença paga do Azure AD. 

## <a name="examples-calculating-guest-user-licenses"></a>Exemplos: Calculando licenças de usuário convidado
Depois de determinar quantos usuários convidados precisam acessar seus serviços pagos do Azure AD, verifique se você tem licenças pagas do Azure AD suficientes para cobrir os usuários convidados na proporção 1:5 necessária. Estes são alguns exemplos:

- Você deseja convidar 100 usuários para seus aplicativos ou serviços do Azure AD e atribuir acesso de provisionamento e gerenciamento a todos os usuários convidados. Você também deseja exigir a MFA e o acesso condicional para 50 desses usuários convidados. Para cobrir essa combinação, você precisará de 10 licenças do Azure AD Básico e 10 licenças do Azure AD Premium P1. Se planejar usar recursos do Identity Protection com os usuários convidados, você precisará de licenças do Azure AD Premium P2 na mesma proporção de 1:5 para cobrir os usuários convidados.
- Você deseja convidar 60 usuários que exigirão MFA, portanto, você precisa ter pelo menos 12 licenças do Azure AD Premium P1. Você tem 10 funcionários com licenças do Azure AD Premium P1, o que permite até 50 usuários convidados na proporção de licenciamento 1:5. Você precisará comprar duas licenças Premium P1 adicionais para cobrir os 10 usuários convidados adicionais.

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes recursos sobre a colaboração B2B do Azure AD:

* [Preço do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
* [Perguntas frequentes sobre a colaboração B2B do Azure Active Directory](faq.md)
