---
title: Solicitação de processo e notificações por email no gerenciamento de direitos do AD do Azure (visualização) – Azure Active Directory
description: Saiba mais sobre o processo de solicitação para um pacote de acesso e as notificações de email são enviadas no gerenciamento de direitos do Active Directory do Azure (visualização).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/26/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ab18c8f165fc30636cd05091be1181743f9972d
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64873626"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>Solicitação de processo e notificações por email no gerenciamento de direitos do AD do Azure (visualização)

> [!IMPORTANT]
> Gerenciamento de direitos do Active Directory (Azure AD) do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Quando um usuário envia uma solicitação para um pacote de acesso, um processo é iniciado para fornecer essa solicitação. Gerenciamento de direitos do AD do Azure também envia notificações por email aos aprovadores e solicitantes quando ocorrem eventos chave durante o processo.

Este artigo descreve o processo de solicitação e as notificações de email são enviadas.

## <a name="request-process"></a>Processo de solicitação

Um usuário que precise de acesso a um pacote de acesso pode enviar uma solicitação de acesso. Dependendo da configuração da política, a solicitação pode exigir uma aprovação. Quando uma solicitação for aprovada, começa um processo atribuir o acesso de usuário a cada recurso no pacote de acesso. O diagrama a seguir mostra uma visão geral do processo e os diferentes estados.

![Diagrama do processo de aprovação](./media/entitlement-management-process/request-process.png)

| Estado | DESCRIÇÃO |
| --- | --- |
| Enviado | O usuário envia uma solicitação. |
| Aprovação pendente | Se a política para um pacote de acesso exige aprovação, uma solicitação se move com aprovação pendente. |
| Expirado | Se nenhum aprovador revisar uma solicitação dentro do tempo limite de solicitação de aprovação, a solicitação expira. Para tentar novamente, o usuário precisará reenviar a solicitação. |
| Negado | Aprovador nega uma solicitação. |
| Aprovado | Aprovador aprovar uma solicitação. |
| Fornecimento | Usuário tem **não** foi atribuído acesso para todos os recursos no pacote de acesso. Se esse for um usuário externo, o usuário ainda não acessou o diretório de recursos e aceita o prompt de permissões. |
| Entregue | Usuário recebeu acesso a todos os recursos no pacote de acesso. |
| Acesso estendido | Se as extensões são permitidas na política, o usuário estendido a atribuição. |
| Acesso expirado | Acesso do usuário para o pacote de acesso expirou. Para obter acesso novamente, o usuário terá que enviar uma solicitação. |

## <a name="email-notifications"></a>Notificações por email

Se você for um aprovador, são enviadas notificações por email, quando você precisa aprovar uma solicitação de acesso e quando foi concluída uma solicitação de acesso. Se você for um solicitante, você recebe notificações por email que indicam o status de sua solicitação. A diagrama a seguir mostra quando essas notificações por email é enviadas.

![Processo de email do gerenciamento de direitos](./media/entitlement-management-process/email-notifications.png)

A tabela a seguir fornece mais detalhes sobre cada uma dessas notificações de email.

| # | Assunto do email | Quando enviado | Enviado para |
| --- | --- | --- | --- |
| 1 | Ação necessária: Solicitação de revisão de acesso de *[solicitante]* à *[pacote de acesso]* por *[Data]* | Quando um solicitante envia uma solicitação para um pacote de acesso | Todos os aprovadores |
| 2 | Ação necessária: Solicitação de revisão de acesso de *[solicitante]* à *[pacote de acesso]* por *[Data]* | Tempo limite da solicitação X dias antes da aprovação | Todos os aprovadores |
| 3 | Notificação de status: *[solicitante]* da solicitação de acesso ao *[pacote de acesso]* expirou | Quando os aprovadores não aprovar ou negar uma solicitação de acesso dentro da duração da solicitação | Solicitante |
| 4 | Notificação de status: *[solicitante]* solicitação de acesso ao *[pacote de acesso]* foi concluída | Quando o primeiro o aprovador aprova ou recusa uma solicitação de acesso | Todos os aprovadores |
| 5 | Você teve o acesso negado ao *[pacote de acesso]* | Quando um solicitante foi negado acesso ao pacote de acesso | Solicitante |
| 6 | Agora você tem acesso ao *[pacote de acesso]*  | Quando um solicitante recebeu acesso a todos os recursos no pacote de acesso | Solicitante |
| 7 | Seu acesso aos *[pacote de acesso]* expirará em X dias | X dias antes do acesso do solicitante para o pacote de acesso expira | Solicitante |
| 8 | Seu acesso aos *[pacote de acesso]* expirou | Quando o acesso do solicitante a um pacote de acesso expira | Solicitante |

### <a name="review-access-request-emails"></a>Emails de revisão de solicitação de acesso

Quando um solicitante envia uma solicitação de acesso para um pacote de acesso está configurado para exigir a aprovação, todos os aprovadores configurados na política de recebem uma notificação por email com detalhes da solicitação. Detalhes incluem o nome do solicitante, organização, acesso a data de início e término se fornecido, justificativa de negócios, quando a solicitação foi enviada e quando a solicitação irá expirar. O email inclui um link em que os aprovadores podem aprovar ou negar a solicitação de acesso. Aqui está uma notificação de email de exemplo que é enviada a um aprovador quando um solicitante envia uma solicitação de acesso.

![Email de solicitação de acesso de revisão](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>Emails de aprovada ou negadas

Solicitantes são notificados quando sua solicitação de acesso é aprovado e está disponível para acesso, ou quando a solicitação de acesso é negada. Quando um aprovador revisões de uma solicitação de acesso enviada por um solicitante, eles podem aprovar ou negar a solicitação de acesso. O aprovador precisa adicionar uma justificativa comercial para sua decisão.

Quando uma solicitação de acesso for aprovada, o gerenciamento de direitos de inicia o processo de conceder acesso ao solicitante para cada um dos recursos no pacote de acesso. Depois que o solicitante recebeu acesso a todos os recursos no pacote de acesso, uma notificação por email é enviada ao solicitante que sua solicitação de acesso foi aprovada e que agora eles têm acesso ao pacote de acesso. Aqui está uma notificação de email de exemplo que é enviada para um solicitante, quando eles recebem acesso a um pacote de acesso.

Quando uma solicitação de acesso é negada, uma notificação por email é enviada ao solicitante. Aqui está uma notificação de email de exemplo que é enviada para um solicitante, quando a solicitação de acesso é negada.

### <a name="expired-access-request-emails"></a>Expirado emails de solicitação de acesso

Os solicitadores serão notificados quando a solicitação de acesso expirou. Quando um solicitante envia uma solicitação de acesso, a solicitação tem uma duração de solicitação após o qual ele expira. Se não houver nenhum aprovador que enviarem uma decisão de aprovar/negar, a solicitação continua a permanecer em um estado de aprovação pendente. Quando a solicitação atinge sua duração de expiração configurada, a solicitação expira e pode não ser aprovada ou negada por aprovadores. Nesse caso, a solicitação irá para um estado expirado. Uma solicitação expirada não pode ser aprovada ou negada. Uma notificação por email é enviada ao solicitante que sua solicitação de acesso expirou, e que eles precisam para reenviar a solicitação de acesso. Aqui está uma notificação de email de exemplo que é enviada para um solicitante, quando a solicitação de acesso expirou.

![Email de solicitação de acesso expirada](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>Próximas etapas

- [Solicitar acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Aprovar ou negar as solicitações de acesso](entitlement-management-request-approve.md)
