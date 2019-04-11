---
title: Como saber se uma página de entrada do AD do Azure aceita as contas da Microsoft | Microsoft Docs
description: Como na tela de mensagens refletem a pesquisa de nome de usuário durante a entrada
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a62c4d56fbfca34ff6291863149b078f7ddc6680
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288585"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Opções de entrada para contas da Microsoft no Azure Active Directory

A página de entrada do Microsoft 365 para o Azure Active Directory (Azure AD) dá suporte a uma ou ambas de trabalho ou contas de estudante e contas da Microsoft, dependendo da situação, para dar suporte a:

* Aplicativos que aceitam entradas de ambos os tipos de conta
* Organizações que aceitam convidados

## <a name="identification"></a>Identificação
Você pode informar se a página de que sua organização usa dá suporte a contas da Microsoft examinando o texto de dica no campo de nome de usuário. Se o texto de dica diz "Email, telefone ou Skype", a página de entrada dá suporte a contas da Microsoft.

![Diferença entre páginas de entrada de conta](./media/signin-account-support/ui-prompt.png)

[Opções adicionais de entrada funcionam apenas para contas pessoais da Microsoft](index.yml) , mas não podem ser usados para entrar ou de estudante da conta de recursos.

## <a name="next-steps"></a>Próximas etapas

[Personalizar a entrada identidade Visual](../fundamentals/add-custom-domain.md)