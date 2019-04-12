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
ms.date: 04/10/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d26ff0f9259e3531259673f94fe477444cc786b
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491588"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Opções de entrada para contas da Microsoft no Azure Active Directory

A página de entrada do Microsoft 365 para o Azure Active Directory (Azure AD) dá suporte ao trabalho ou contas de estudante e contas da Microsoft, mas dependendo da situação do usuário, ele pode ser um ou outro ou ambos. Por exemplo, a página de entrada do AD do Azure dá suporte a:

* Aplicativos que aceitam entradas de ambos os tipos de conta
* Organizações que aceitam convidados

## <a name="identification"></a>Identificação
Você pode informar se a página de que sua organização usa dá suporte a contas da Microsoft examinando o texto de dica no campo de nome de usuário. Se o texto de dica diz "Email, telefone ou Skype", a página de entrada dá suporte a contas da Microsoft.

![Diferença entre páginas de entrada de conta](./media/signin-account-support/ui-prompt.png)

[Opções adicionais de entrada funcionam apenas para contas pessoais da Microsoft](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) , mas não podem ser usados para entrar ou de estudante da conta de recursos.

## <a name="next-steps"></a>Próximas etapas

[Personalizar a entrada identidade Visual](../fundamentals/add-custom-domain.md)