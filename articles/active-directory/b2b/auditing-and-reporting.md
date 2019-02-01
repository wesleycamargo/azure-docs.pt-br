---
title: Auditoria e relatórios de um usuário de colaboração B2B do Azure Active Directory | Microsoft Docs
description: As propriedades do usuário convidado são configuráveis na colaboração B2B do Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: 06622c093ca90b3873365e6c93c40fc7221a6398
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075186"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Auditoria e relatórios de um usuário de colaboração B2B
Com usuários convidados, você conta com funcionalidades de auditoria semelhantes àquelas dos usuários membros. 

## <a name="access-reviews"></a>Análises de acesso
É possível usar as revisões de acesso para verificar periodicamente se usuários convidados ainda precisam acessar os recursos. O recurso **Revisões de acesso** está disponível no **Azure Active Directory** em **Gerenciar** > **Relações Organizacionais**. (Também é possível procurar por "revisões de acesso" em **Todos os serviços** no portal do Azure.) Para saber como usar as revisões de acesso, consulte [Gerenciar acesso de convidado com revisões de acesso do Azure AD](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Logs de auditoria

Os logs de auditoria do Azure AD fornecem registros das atividades do usuário e do sistema, incluindo atividades iniciadas por usuários convidados. Para acessar os logs de auditoria, no **Azure Active Directory**, em **monitoramento**, selecione **Logs de auditoria**. Este é um exemplo do histórico de convites e resgates do convidado Davi Barros:

![log de auditoria](./media/auditing-and-reporting/audit-log.png)

Você pode se aprofundar em cada um desses eventos para obter detalhes. Por exemplo, vamos examinar os detalhes de aceitação.

![detalhes da atividade](./media/auditing-and-reporting/activity-details.png)

Você também pode exportar esses logs do Azure AD e usar a ferramenta de relatório de sua preferência para obter relatórios personalizados.

### <a name="next-steps"></a>Próximas etapas

- [Propriedades de usuário de colaboração B2B](user-properties.md)

