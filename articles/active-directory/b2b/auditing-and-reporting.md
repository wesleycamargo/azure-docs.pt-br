---
title: Auditoria e relatórios de um usuário de colaboração B2B – Azure Active Directory | Microsoft Docs
description: As propriedades do usuário convidado são configuráveis na colaboração B2B do Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5763a7e5f122702ddaf86246fbfbd18326878146
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60357149"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Auditoria e relatórios de um usuário de colaboração B2B
Com usuários convidados, você conta com funcionalidades de auditoria semelhantes àquelas dos usuários membros. 

## <a name="access-reviews"></a>Análises de acesso
É possível usar as revisões de acesso para verificar periodicamente se usuários convidados ainda precisam acessar os recursos. O recurso **Revisões de acesso** está disponível no **Azure Active Directory** em **Gerenciar** > **Relações Organizacionais**. (Também é possível procurar por "revisões de acesso" em **Todos os serviços** no portal do Azure.) Para saber como usar as revisões de acesso, consulte [Gerenciar acesso de convidado com revisões de acesso do Azure AD](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Logs de auditoria

Os logs de auditoria do Azure AD fornecem registros das atividades do usuário e do sistema, incluindo atividades iniciadas por usuários convidados. Para acessar os logs de auditoria, no **Azure Active Directory**, em **monitoramento**, selecione **Logs de auditoria**. Este é um exemplo do histórico de convites e resgates do convidado Davi Barros:

![Exemplo de auditoria e de captura de tela mostrando o log de saída](./media/auditing-and-reporting/audit-log.png)

Você pode se aprofundar em cada um desses eventos para obter detalhes. Por exemplo, vamos examinar os detalhes de aceitação.

![Captura de tela mostrando e exemplo de saída de detalhes da atividade](./media/auditing-and-reporting/activity-details.png)

Você também pode exportar esses logs do Azure AD e usar a ferramenta de relatório de sua preferência para obter relatórios personalizados.

### <a name="next-steps"></a>Próximas etapas

- [Propriedades de usuário de colaboração B2B](user-properties.md)

