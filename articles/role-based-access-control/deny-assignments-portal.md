---
title: Gerenciar dispositivos usando o portal do Azure | Microsoft Docs
description: Aprenda a exibir os usuários, grupos, entidades de serviço e identidades gerenciadas que tiveram acesso negado a ações específicas em determinado escopo usando o portal do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f5870ddbbb8be0ebbeae7656485521a327b86d5b
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637721"
---
# <a name="view-deny-assignments-using-the-azure-portal"></a>Ver negação de atribuições usando o portal do Azure

[Negar designações](deny-assignments.md) impedem que os usuários executem ações específicas, mesmo que uma atribuição de função conceda a eles acesso. Mesmo que você não possa criar suas próprias atribuições de negação, ainda será necessário visualizar as atribuições de negação, pois elas podem afetar suas permissões gerais. Para obter informações sobre uma atribuição de negação, você precisa ter a permissão `Microsoft.Authorization/denyAssignments/read`, que está incluída na maioria das [funções internas](built-in-roles.md).

Este artigo descreve como usar o portal do Azure para exibir atribuições de negação.

> [!NOTE]
> No momento, as atribuições de negação são somente leitura e só podem ser definidas pelo Azure.

## <a name="view-deny-assignments"></a>Modo de exibição negar atribuições

Siga estas etapas para visualizar as atribuições de negação no escopo da assinatura ou do grupo de gerenciamento.

1. No portal do Azure, clique em **Todos os serviços** e, em seguida, em **Grupos de gerenciamento** ou **Assinaturas**.

1. Clique no grupo de gerenciamento ou na assinatura que você deseja visualizar.

1. Clique em **controle de acesso (IAM)**.

1. Clique na guia **Negar atribuições** (ou clique no botão **Visualizar** na peça Visualizar negar atribuições).

    Se houver alguma atribuição de negação neste escopo ou herdada para esse escopo, ela será listada.

    ![Controle de acesso - guia Negar atribuições](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Para exibir colunas adicionais, clique em **Editar colunas**.

    ![Negar atribuições - colunas](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Nome** | Nome da atribuição de negação. |
    | **Tipo principal** | Usuário, grupo, grupo definido pelo sistema ou principal do serviço. |
    | **Negado**  | Nome do principal de segurança incluído na atribuição de negação. |
    | **Id** | Identificador exclusivo para a atribuição de negação. |
    | **Principais Excluídos** | Se existem entidades de segurança que são excluídas da atribuição de negação. |
    | **Não se aplica a crianças** | Se a atribuição de negação é herdada para subescópios. |
    | **Protegidos do sistema** | Se a atribuição de negação é gerenciada pelo Azure. Atualmente, sempre sim. |
    | **Escopo** | Grupo de gerenciamento, assinatura, grupo de recursos ou recurso. |

1. Adicione uma marca de seleção a qualquer um dos itens ativados e clique em **OK** para exibir as colunas selecionadas.

## <a name="view-details-about-a-deny-assignment"></a>Visualizar detalhes sobre uma atribuição de negação

Siga estas etapas para visualizar detalhes adicionais sobre uma atribuição de negação.

1. Abra o painel **Negar atribuições** conforme descrito na seção anterior.

1. Clique no nome da atribuição de negação para abrir a folha **Usuários**.

    ![Negar designação - usuários](./media/deny-assignments-portal/deny-assignment-users.png)

    A folha **Usuários** inclui as duas seções a seguir.

    |  |  |
    | --- | --- |
    | **Negar atribuição se aplica a**  | Entidades de segurança às quais a atribuição de negação se aplica. |
    | **Negar a exclui da atribuição** | Entidades de segurança que são excluídas da atribuição de negação. |

    **A diretiva definida pelo sistema** representa todos os usuários, grupos, entidades de serviço e identidades gerenciadas em um Azure Active Directory.

1. Para ver uma lista das permissões que são negados, clique em **permissões negadas**.

    ![Negar - atribuição de permissões negado](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Tipo de ação | DESCRIÇÃO |
    | --- | --- |
    | **Ações**  | Operações de gerenciamento negado. |
    | **NotActions** | Operações de gerenciamento excluídas da operação de gerenciamento negado. |
    | **DataActions**  | Operações de dados negado. |
    | **NotDataActions** | Operações de dados excluídas da operação de dados negados. |

    Para o exemplo mostrado na captura de tela anterior, as seguintes são as permissões efetivas:

    - Todas as operações de armazenamento no plano de dados são negadas, exceto para operações de computação.

1. Para ver as propriedades de uma atribuição de negação, clique em **Propriedades**.

    ![Negar atribuição - propriedades](./media/deny-assignments-portal/deny-assignment-properties.png)

    Na folha **Propriedades**, você pode ver o nome, a ID, a descrição e o escopo da atribuição de negação. A opção **Não se aplica a filhos** indica se a atribuição de negação é herdada para os subescópios. A chave **System protected** indica se essa atribuição de negação é gerenciada pelo Azure. Atualmente, isso é **Sim** em todos os casos.

## <a name="next-steps"></a>Próximas etapas

* [Compreender atribuições de negação](deny-assignments.md)
* [Listar atribuições de negação usando RBAC e a API REST](deny-assignments-rest.md)
