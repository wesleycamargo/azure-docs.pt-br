---
title: Assistente de segurança no PIM – Azure | Microsoft Docs
description: Descreve o Assistente de segurança que aparece na primeira vez que você usar o Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 02/27/2017
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.openlocfilehash: 178a4c5e978075f2a59b22a1cccf462138527964
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189072"
---
# <a name="security-wizard-in-pim"></a>Assistente de segurança no PIM
Se você for a primeira pessoa a executar o Azure PIM (Privileged Identity Management) para sua organização, verá um assistente. O assistente ajuda você a entender os riscos à segurança de identidades com privilégios e como usar o PIM para reduzir esses riscos. Você não precisará fazer nenhuma alteração nas atribuições de função existentes no assistente, se preferir fazer isso posteriormente.

## <a name="what-to-expect"></a>O que esperar
Antes de sua organização começar a usar o PIM, todas as atribuições de função são permanentes: os usuários sempre estarão sempre nessas funções, mesmo se não precisarem dos privilégios no momento.  A primeira etapa do assistente mostra uma lista de funções com privilégios altos e quantos usuários atualmente estão nessas funções. Você poderá analisar uma função específica para saber mais sobre os usuários se um ou mais não forem familiares.

A segunda etapa do assistente lhe fornece a oportunidade de alterar as atribuições de função do administrador.  

> [!WARNING]
> É importante que você tenha pelo menos um administrador global e mais de um administrador de função com privilégios com uma conta organizacional (não uma conta da Microsoft). Se houver apenas um administrador de função com privilégios, a organização não poderá gerenciar o PIM se essa conta for excluída.
> Além disso, mantenha as atribuições de função permanentes se um usuário tiver uma conta da Microsoft (uma conta que ele usa para entrar nos serviços Microsoft como o Outlook.com e o Skype). Se você planejar exigir o MFA para ativação para essa função, esse usuário será bloqueado.
> 
> 

Depois de ter feito alterações, o assistente não aparecerá mais. Na próxima vez que você ou outro administrador de função com privilégios usar o PIM, você verá o painel do PIM.  

* Se você desejar adicionar ou remover usuários das funções ou alterar as atribuições de permanentes para qualificadas, leia mais em [como adicionar ou remover uma função de usuário](pim-how-to-add-role-to-user.md).
* Se desejar fornecer aos usuários mais acesso para gerenciar o PIM, leia mais em [como conceder acesso para gerenciar no PIM](pim-how-to-give-access-to-pim.md).

## <a name="next-steps"></a>Próximas etapas

- [Começar a usar o PIM](pim-getting-started.md)
- [Atribuir funções de diretório do Azure AD no PIM](pim-how-to-add-role-to-user.md)
- [Permitir acesso a outros administradores para gerenciar o PIM](pim-how-to-give-access-to-pim.md)
