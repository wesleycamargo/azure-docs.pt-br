---
title: Exibir logs e relatórios no gerenciamento de direitos do AD do Azure (visualização) – Azure Active Directory
description: Saiba como exibir o relatório de atribuições de usuário e logs de auditoria no gerenciamento de direitos do Active Directory do Azure (visualização).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a61a581574c77a57939ea23fdadc7b060b82af
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541534"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Exibir relatórios e logs no gerenciamento de direitos do AD do Azure (visualização)

> [!IMPORTANT]
> Gerenciamento de direitos do Active Directory (Azure AD) do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="view-resources-a-user-has-access-to"></a>Exibir recursos de um usuário tem acesso ao

1. Clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **relatório de atribuições de usuário**.

1. Clique em **selecionar usuários** para abrir o painel Selecionar usuários.

1. Localize o usuário na lista que você deseja exibir os recursos que eles têm acesso.

1. Clique no usuário e, em seguida, clique em **selecionar**.

    É exibida uma lista de recursos que o usuário tem acesso. Ele inclui o pacote de acesso, política e datas.

    ![Relatório de atribuições de usuário](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Determinar o status de uma solicitação do usuário

Para obter detalhes adicionais sobre como um usuário é solicitado e recebido acesso a um pacote de acesso, você pode usar o log de auditoria do AD do Azure. Em particular, você pode usar os registros de log na `EntitlementManagement` e `UserManagement` categorias para obter detalhes adicionais sobre as etapas de processamento para cada solicitação.  

1. Clique em **Azure Active Directory** e, em seguida, clique em **logs de auditoria**.

1. Na parte superior, alterar o **categoria** para um `EntitlementManagement` ou `UserManagement`, dependendo do registro de auditoria que você está procurando.  

1. Clique em **Aplicar**.

1. Para baixar os logs, clique em **baixar**.

Quando o Azure AD recebe uma nova solicitação, ele grava um registro de auditoria, na qual o **categoria** é `EntitlementManagement` e o **atividade** normalmente é `User requests access package assignment`.  No caso de uma atribuição direta criada no portal do Azure, o **atividade** campo do registro de auditoria é `Administrator directly assigns user to access package`, e o usuário que está executando a atribuição é identificado pelo **ActorUserPrincipalName**.

O Azure AD será gravar registros de auditoria adicionais enquanto a solicitação está em andamento, incluindo:

| Category | Atividade | Status da solicitação |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Solicitação não requer aprovação |
| `UserManagement` | `Create request approval` | Solicitação requer aprovação |
| `UserManagement` | `Add approver to request approval` | Solicitação requer aprovação |
| `EntitlementManagement` | `Approve access package assignment request` | Solicitação aprovada |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Solicitação aprovada ou não requer aprovação |

Quando um usuário é atribuído o acesso, o Azure AD grava um registro de auditoria para o `EntitlementManagement` categoria com **atividade** `Fulfill access package assignment`.  O usuário que recebeu o acesso é identificado por **ActorUserPrincipalName** campo.

Se o acesso não foi atribuído, o Azure AD grava um registro de auditoria para o `EntitlementManagement` categoria com **atividade** ambos `Deny access package assignment request`, se a solicitação foi negada por um aprovador ou `Access package assignment request timed out (no approver action taken)`, se a solicitação atingiu o tempo limite antes de um aprovador pode aprovar.

Quando a atribuição do pacote de acesso do usuário expira, é cancelada pelo usuário ou removido por um administrador, em seguida, o Azure AD grava um registro de auditoria para o `EntitlementManagement` categoria com **atividade** de `Remove access package assignment`.

## <a name="next-steps"></a>Próximas etapas

- [Solucionar problemas do gerenciamento de direitos do AD do Azure](entitlement-management-troubleshoot.md)
- [Cenários comuns](entitlement-management-scenarios.md)
