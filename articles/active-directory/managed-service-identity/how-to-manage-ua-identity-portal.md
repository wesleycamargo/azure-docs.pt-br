---
title: Como gerenciar uma identidade gerenciada atribuída ao usuário usando o portal do Azure
description: Instruções passo a passo sobre como criar, listar e excluir uma identidade gerenciada atribuída ao usuário.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: bd6327aa5c16d57c550025667659f9245a5b0b65
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42143938"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-portal"></a>Criar, listar ou excluir uma identidade atribuída pelo usando o portal do Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que seja compatível com a autenticação do Azure Active Directory sem precisar ter as credenciais no seu código. 

Neste artigo, você aprenderá a criar, listar e excluir uma identidade atribuída pelo usuário usando o Portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a [seção de visão geral](overview.md). **Verifique se examinou a [diferença entre uma identidade atribuída pelo sistema e uma atribuída pelo usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações de gerenciamento deste artigo, a conta precisará das seguintes atribuições de função:
    - [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) função para criar, ler (listar), atualizar e excluir uma identidade atribuída ao usuário.
    - Função de [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para ler (listar) as propriedades de uma identidade atribuída ao usuário.

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída pelo usuário

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure para criar a identidade gerenciada atribuída ao usuário.
2. Na caixa de pesquisa, digite *Identidades Gerenciadas*e, em **Serviços**, clique em **Identidades Gerenciadas**.
3. Clique em **Adicionar** e insira valores nos campos a seguir no painel **Criar identidade gerenciada atribuída ao usuário**:
   - **Nome do Recurso**: esse é o nome da identidade gerenciada atribuída ao usuário, por exemplo, UAI1.
   - **Assinatura**: escolha a assinatura para criar a identidade gerenciada atribuída ao usuário
   - **Grupo de Recursos**: crie um novo grupo de recursos para conter a identidade atribuída pelo usuário ou escolha **Usar existente** para criar a identidade gerenciada atribuída ao usuário em um grupo de recursos existente.
   - **Local**: escolha um local para implantar a identidade gerenciada atribuída ao usuário, por exemplo **Oeste dos EUA**.
4. Clique em **Criar**.

![Criar uma identidade gerenciada atribuída pelo usuário](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-identities"></a>Listar identidades atribuídas pelo usuário

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure para listar as identidades gerenciadas atribuídas ao usuário.
2. Na caixa de pesquisa, digite *Identidades Gerenciadas*e, em Serviços, clique em **Identidades Gerenciadas**.
3. Uma lista de identidades gerenciadas atribuídas ao usuário para a sua assinatura é retornada.  Para ver os detalhes de um usuário atribuído, clique em seu nome.

![Listar identidade gerenciada atribuída pelo usuário](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-identity"></a>Excluir uma identidade atribuída pelo usuário

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure para excluir a identidade gerenciada atribuída ao usuário.
2. Selecione a identidade atribuída ao usuário e clique em **Excluir**.
3. Na caixa de confirmação, escolha, **Sim**.

![Excluir identidade gerenciada atribuída ao usuário](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)