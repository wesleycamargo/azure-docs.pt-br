---
title: Diretrizes de licenciamento da colaboração B2B do Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: e876c720e9cac4f05fd0776a3aba24976720fb15
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075832"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Diretrizes de licenciamento da colaboração B2B do Azure Active Directory

Com a colaboração do Azure AD (Azure Active Directory) B2B (entre empresas), você pode convidar usuários externos (ou "usuários convidados") para usar seus serviços pagos do Azure AD. Para cada licença paga do Azure AD que você atribui a um usuário, você pode convidar até cinco usuários convidados por meio da provisão de usuário externo.

Um usuário convidado é alguém que não é membro de sua organização nem de nenhuma das afiliadas da sua organização. Os usuários convidados são definidos por sua relação com a sua organização, não pelas credenciais que usam para entrar. Na verdade, um usuário convidado pode entrar com uma identidade de externa ou com as credenciais que pertencem à sua organização.

Os seguintes *não* são usuários convidados:
- Seus funcionários, prestadores de serviço no local ou agentes no local
- Funcionários, prestadores de serviço no local ou agentes no local de suas afiliadas

O licenciamento do usuário convidado B2B é calculado automaticamente e relatado com base na proporção 1:5. Atualmente, não é possível atribuir licenças de usuário convidado B2B diretamente aos usuários convidados.

Há algumas situações em que um usuário convidado não é relatado usando a provisão de usuário externo 1:5. Se um usuário convidado já tiver uma licença paga do Azure AD de sua própria organização, ele não consumirá uma das suas licenças de usuário convidado B2B. Além disso, os usuários convidados podem usar os recursos gratuitos do Azure AD sem requisitos adicionais de licenciamento. Os usuários convidados têm acesso aos recursos gratuitos do Azure AD, mesmo quando você não tem nenhuma licença paga do Azure AD. 

## <a name="examples-calculating-guest-user-licenses"></a>Exemplos: Calculando licenças de usuário convidado
Depois de determinar quantos usuários convidados precisam acessar seus serviços pagos do Azure AD, verifique se você tem licenças pagas do Azure AD suficientes para cobrir os usuários convidados na proporção 1:5 necessária. Estes são alguns exemplos:

- Você deseja convidar 100 usuários para seus aplicativos ou serviços do Azure AD e atribuir acesso de provisionamento e gerenciamento a todos os usuários convidados. Você também deseja exigir a MFA e o acesso condicional para 50 desses usuários convidados. Para cobrir essa combinação, você precisará de 10 licenças do Azure AD Básico e 10 licenças do Azure AD Premium P1. Se planejar usar recursos do Identity Protection com os usuários convidados, você precisará de licenças do Azure AD Premium P2 na mesma proporção de 1:5 para cobrir os usuários convidados.
- Você deseja convidar 60 usuários que exigirão MFA, portanto, você precisa ter pelo menos 12 licenças do Azure AD Premium P1. Você tem 10 funcionários com licenças do Azure AD Premium P1, o que permite até 50 usuários convidados na proporção de licenciamento 1:5. Você precisará comprar duas licenças Premium P1 adicionais para cobrir os 10 usuários convidados adicionais.

## <a name="using-the-b2b-collaboration-api-to-invite-users-from-your-affiliates"></a>Usando a API de colaboração B2B para convidar usuários de suas afiliadas

Por definição, um usuário convidado B2B é um usuário externo que você convida para usar seus serviços e aplicativos pagos do Azure AD. Um funcionário, um prestador de serviço no local ou um agente no local de sua empresa ou de uma de suas afiliadas não se qualifica para colaboração B2B, mesmo quando são usados recursos B2B. Estes são alguns exemplos: 
- Você deseja usar credenciais externas (por exemplo, uma identidade social) para convidar um usuário que seja um funcionário da sua organização. Esse cenário não está em conformidade com os requisitos de licenciamento e não é permitido. As credenciais externas não fazem com que um funcionário seja um usuário externo.  
- Você deseja usar as APIs B2B para convidar um usuário de uma das afiliadas da sua organização. Embora esse cenário use APIs B2B para convidar o usuário, essa não é considerada uma colaboração B2B. Ela não está em conformidade com os requisitos de licenciamento porque um usuário de suas afiliadas não é um usuário externo. 

Em ambos os cenários, a melhor solução é usar a API B2B para convidar os usuários como membros (invitedUserType = Member) e atribuir a cada um deles uma licença do Azure AD. 

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes recursos sobre a colaboração B2B do Azure AD:

* [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
* [Perguntas frequentes sobre a colaboração B2B do Azure Active Directory](faq.md)
