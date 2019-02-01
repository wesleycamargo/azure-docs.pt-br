---
title: MFA (Autenticação Multifator) e PIM - Azure | Microsoft Docs
description: Saiba como o PIM (Azure AD Privileged Identity Management) valida a MFA (autenticação multifator).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: c26050eb8844368554726d0e642b7b7384649763
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195572"
---
# <a name="multi-factor-authentication-mfa-and-pim"></a>MFA (Autenticação Multifator) e PIM

É recomendável que você exija MFA (autenticação multifator) a todos os administradores. Isso reduz o risco de um ataque devido a uma senha comprometida.

Você pode exigir que os usuários concluam um desafio de MFA quando entrarem. Também é possível exigir que os usuários concluam um desafio de MFA quando ativarem uma função no PIM (Azure AD Privileged Identity Management). Dessa forma, se o usuário não concluir um desafio de MFA quando ao entrar, será solicitado pelo PIM que ele faça isso.

> [!IMPORTANT]
> Agora, o Azure MFA funciona apenas com contas corporativas ou de estudante, não com contas da Microsoft (normalmente uma conta pessoal usada para entrar nos serviços Microsoft como Skype, Xbox, Outlook.com etc.). Por isso, pessoas usando uma conta Microsoft não podem ser um administrador qualificado porque não podem usar a MFA para ativar as funções. Se esses usuários precisarem continuar a gerenciar cargas de trabalho usando uma conta da Microsoft, eleve-os a administradores permanentes por enquanto.

## <a name="how-pim-validates-mfa"></a>Como o PIM valida MFA

Há duas opções de validação de MFA quando um usuário ativa uma função.

A opção mais simples é contar com o Azure MFA para usuários que estão ativando uma função privilegiada. Para fazer isso, primeiro verifique se esses usuários são licenciados, se necessário, e se estão registrados para o Azure MFA. Para obter mais informações sobre como implantar a MFA do Azure, consulte [Implantar Autenticação Multifator do Microsoft Azure baseada em nuvem](../authentication/howto-mfa-getstarted.md). É recomendável, mas não obrigatório, configurar o Azure AD para impor o MFA a esses usuários quando eles entram. Isso ocorre porque as verificações de MFA serão feitas pelo próprio PIM.

Alternativamente, se os usuários se autenticarem localmente, você poderá fazer com que o provedor de identidade seja responsável pelo MFA. Por exemplo, se você tiver configurado os Serviços de Federação do AD para exigir a autenticação baseada em cartão inteligente antes de acessar o Azure AD, [Protegendo os recursos de nuvem usando a Autenticação Multifator do Azure e o AD FS](../authentication/howto-mfa-adfs.md) inclui instruções para configurar o AD FS a fim de enviar solicitações ao Azure AD. Quando um usuário tentar ativar uma função, o PIM aceitará que a MFA já foi validada para o usuário após receber as declarações apropriadas.

## <a name="next-steps"></a>Próximas etapas

- [Definir configurações de função do diretório do Azure AD no PIM](pim-how-to-change-default-settings.md)
- [Definir configurações de função de recurso do Azure no PIM](pim-resource-roles-configure-role-settings.md)
