---
title: Notificações por email no PIM – Azure | Microsoft Docs
description: Descreve as notificações por email no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/05/2019
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f4eab44a55574f84f29488e78cd60522d7a52eb
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56171189"
---
# <a name="email-notifications-in-pim"></a>Notificações por email no PIM

O PIM (Azure AD Privileged Identity Management) permite saber quando eventos importantes ocorrem como, por exemplo, quando uma função é atribuída ou ativada. O PIM irá mantê-lo informado, enviando notificações de email a você e outros participantes. Adicionalmente, esses emails podem incluir links para tarefas relevantes como ativar ou renovar uma função. Este artigo descreve a aparência desses emails, quando são enviados e quem recebe os emails.

## <a name="sender-email-address-and-subject-line"></a>Endereço de email do remetente e linha do assunto

Os emails enviados do PIM para ambas as funções de recursos do Azure e Azure AD têm o seguinte endereço de email do remetente:

- Endereço de email:  **azure-noreply@microsoft.com**
- Nome de exibição: Microsoft Azure

Esses emails incluem um prefixo **PIM** na linha de assunto. Aqui está um exemplo:

- PIM: Alain Charon foi atribuído permanentemente à função Leitor de Backup

## <a name="pim-emails-for-azure-ad-roles"></a>Emails do PIM para funções do Azure AD

O PIM envia emails quando os seguintes eventos ocorrem nas funções do Azure AD:

- Quando uma ativação de função com privilégios está com aprovação pendente
- Quando uma solicitação de ativação de função com privilégios é concluída
- Quando o Azure AD PIM é habilitado

Quem recebe esses emails para as funções do Azure AD depende da função, do evento e da configuração de notificações:

| Usuário | Ativação de função está pendente de aprovação | A solicitação de ativação de função está concluída | O PIM está habilitado |
| --- | --- | --- | --- |
| Administrador de função com privilégios</br>(Ativado/Qualificado) | Sim</br>(somente se nenhum aprovador explícito for especificado) | Sim* | Sim |
| Administrador de segurança</br>(Ativado/Qualificado) | Não  | Sim* | Sim |
| Administrador global</br>(Ativado/Qualificado) | Não  | Sim* | Sim |

\*Se as [**configurações de** Notificações](pim-how-to-change-default-settings.md#notifications) estiver definida como **Habilitar**.

A seguir, é mostrado um email de exemplo enviado quando um usuário ativa uma função do Azure AD para a organização fictícia Contoso.

![Novo email do PIM para funções do Azure AD](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-pim-digest-email-for-azure-ad-roles"></a>Email de resumo semanal do PIM para funções do Azure AD

Um email de resumo semanal do PIM para funções do Azure AD é enviado para Administradores com Função com Privilégios, Administradores de Segurança e Administradores Globais que habilitaram o PIM. Esse email semanal fornece um instantâneo das atividades do PIM da semana, bem como atribuições de função com privilégios. É disponibilizado apenas para locatários na nuvem pública. Aqui está um exemplo de e-mail:

![Email de resumo semanal do PIM para funções do Azure AD](./media/pim-email-notifications/email-directory-weekly.png)

O email inclui quatro blocos:

| Bloco | DESCRIÇÃO |
| --- | --- |
| **Usuários ativados** | Número de vezes que os usuários ativaram a função qualificada no locatário. |
| **Usuários tornados permanentes** | Número de vezes que usuários com uma atribuição qualificada tornam-se permanentes. |
| **Atribuições de função no PIM** | Número de vezes que os usuários recebem uma função qualificada dentro do PIM. |
| **Atribuições de função fora do PIM** | Número de vezes que os usuários recebem uma função permanente fora do PIM (dentro do Azure AD). |

A seção **Visão geral das principais funções** lista as cinco principais funções no locatário com base no número total de administradores permanentes e qualificados para cada função. O link **Executar ação** abre o [assistente do PIM](pim-security-wizard.md), onde é possível converter administradores permanentes em administradores qualificados em lotes.

## <a name="pim-emails-for-azure-resource-roles"></a>Emails do PIM para funções de recurso do Azure

O PIM enviará emails para Proprietários e Administradores de Acesso do Usuário quando os seguintes eventos ocorrerem nas funções de recursos do Azure:

- Quando uma atribuição de função estiver com aprovação pendente
- Quando uma função for atribuída
- Quando uma função estiver prestes a expirar
- Quando uma função for qualificada para estender
- Quando uma função estiver sendo renovada por um usuário final
- Quando uma solicitação de ativação de função for concluída

O PIM enviará emails para usuários finais quando os seguintes eventos ocorrerem nas funções de recursos do Azure:

- Quando uma função for atribuída ao usuário
- Quando a função de um usuário expirar
- Quando a função do usuário for estendida
- Quando a solicitação de ativação de função de um usuário for concluída

A seguir, é mostrado um email de exemplo enviado quando um usuário recebe uma função de recurso do Azure para a organização fictícia Contoso.

![Novo email do PIM para funções de recurso do Azure](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Próximas etapas

- [Definir configurações de função do diretório do Azure AD no PIM](pim-how-to-change-default-settings.md)
- [Aprovar ou negar solicitações para funções de diretório do Azure AD no PIM](azure-ad-pim-approval-workflow.md)
