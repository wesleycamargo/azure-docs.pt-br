---
title: Início Rápido - exibir funções atribuídas a um usuário usando o portal do Azure | Microsoft Docs
description: Saiba como exibir as permissões de RBAC (Controle de Acesso Baseado em Função) atribuídas a um usuário, grupo, entidade de serviço ou a uma identidade gerenciada usando o portal do Azure.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b755dd6223c21084cafea82a1c8857f9f54b03b5
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52641861"
---
# <a name="quickstart-view-roles-assigned-to-a-user-using-the-azure-portal"></a>Início Rápido - exibir funções atribuídas a um usuário usando o portal do Azure

Você pode usar a folha **controle de acesso (IAM)** no [controle de acesso baseado em função (RBAC)](overview.md) para exibir as atribuições de função para vários usuários, grupos, entidades de serviço e identidades gerenciadas, mas, às vezes, basta exibir rapidamente as atribuições de função para um único usuário, grupo, entidade de serviço ou identidade gerenciada. A maneira mais fácil de fazer isso é usar o recurso **Verificar acesso** no portal do Azure.

## <a name="view-role-assignments"></a>Exibir atribuições de função

Siga essas etapas para exibir as atribuições de função para um único usuário, grupo, entidade de serviço ou identidade gerenciada no escopo de assinaturas.

1. No portal do Microsoft Azure, clique em **Todos os serviços** e, em seguida, em **Assinaturas**.

1. Clique em sua assinatura.

1. Clique em **Controle de acesso (IAM)**.

1. Clique na guia **Verificar acesso**.

    ![Controle de acesso - verificar a guia de acesso](./media/check-access/access-control-check-access.png)

1. Na lista **Localizar**, selecione o tipo de entidade de segurança para a qual você deseja verificar o acesso.

1. Na caixa de pesquisa, insira uma cadeia de caracteres para pesquisar no diretório nomes de exibição, endereços de e-mail ou identificadores de objetos.

    ![Verifique a lista de seleção de acesso](./media/check-access/check-access-select.png)

1. Clique na entidade de segurança para abrir o painel **atribuições**.

    ![painel atribuições](./media/check-access/check-access-assignments.png)

    Nesse painel, você pode ver as funções atribuídas à entidade de segurança selecionada e o escopo. Se houver alguma atribuição de negação nesse escopo ou herdada para esse escopo, ela será listada.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Conceder acesso a um usuário usando o RBAC e o portal do Azure](quickstart-assign-role-user-portal.md)
