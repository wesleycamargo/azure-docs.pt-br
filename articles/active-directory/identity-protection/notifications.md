---
title: Notificações do Azure Active Directory Identity Protection | Microsoft Docs
description: Saiba como as notificações dão suporte às suas atividades de investigação.
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gerenciamento de aplicativos, segurança, risco, nível de risco, vulnerabilidade, política de segurança
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 0a546acd05246e011fa66abea8a667d0b3513588
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005891"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notificações do Azure Active Directory Identity Protection

O Azure AD Identity Protection envia dois tipos de emails de notificação automatizados para ajudar você a gerenciar o risco do usuário e eventos de risco:

- Email de usuários em risco detectado
- Email de resumo semanal

Este artigo fornece uma visão geral de ambos os emails de notificação.


## <a name="users-at-risk-detected-email"></a>Email de usuários em risco detectado

Em resposta a uma conta detectada em risco, o Azure AD Identity Protection gera um alerta de email com **Usuários em risco detectado** como assunto. O email inclui um link para o relatório **[Usuários sinalizados por risco](../reports-monitoring/concept-user-at-risk.md)**. Como prática recomendada, você deve investigar imediatamente os usuários em risco.

![Email de usuários em risco detectado](./media/notifications/01.png)


### <a name="configuration"></a>Configuração

Como administrador, você pode definir:

- **O nível de risco que dispara a geração desse email** -Por padrão, o nível de risco é definido como "Alto".
- **Os destinatários deste email** -Por padrão, os destinatários incluem todos os administradores globais. Os administradores globais também podem adicionar outros administradores globais, administradores de segurança, leitores de segurança como destinatários.  


Para abrir a caixa de diálogo correspondente, clique em **Alertas** na seção **Configurações** da página **Proteção da Identidade**.

![Email de usuários em risco detectado](./media/notifications/05.png)


## <a name="weekly-digest-email"></a>Email de resumo semanal

O email de resumo semanal contém um resumo dos novos eventos de risco.  
Ele inclui:

- Usuários em risco

- Atividades suspeitas

- Vulnerabilidades detectadas

- Links para os relatórios relacionados no Identity Protection

    ![Correção](./media/notifications/400.png "Correção")

### <a name="configuration"></a>Configuração

Como administrador, é possível desativar o envio de um email de resumo semanal.

![Riscos de usuário](./media/notifications/62.png "Riscos de usuário")

Para abrir a caixa de diálogo correspondente, clique em **Resumo Semanal** na seção **Configurações** da página **Proteção da Identidade**.

![Email de usuários em risco detectado](./media/notifications/04.png)


## <a name="see-also"></a>Consulte também

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
