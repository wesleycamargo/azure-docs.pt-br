---
title: Gerenciar permissões aos recursos por usuário no Azure Stack | Microsoft Docs
description: Como um administrador de serviços ou Locatário, saiba como gerenciar permissões de RBAC.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: patricka
ms.reviewer: ''
ms.openlocfilehash: 70641084d2213b50803800a64000611d139facec
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53075754"
---
# <a name="manage-access-to-resources-with-azure-stack-role-based-access-control"></a>Gerenciar o acesso a recursos com controle de acesso Azure Stack Role-Based

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O Azure Stack oferece suporte a controle de acesso baseado em função (RBAC), o mesmo [modelo de segurança para gerenciamento de acesso](https://docs.microsoft.com/azure/role-based-access-control/overview) que usa o Microsoft Azure. Você pode usar o RBAC para gerenciar o usuário, grupo ou aplicativo acesso aos serviços, recursos e assinaturas.

## <a name="basics-of-access-management"></a>Noções básicas de gerenciamento de acesso

Controle de acesso baseado em função fornece controle de acesso refinado que você pode usar para proteger seu ambiente. Você dar aos usuários as permissões exatas necessárias ao atribuir uma função RBAC em um determinado escopo. O escopo da atribuição de função pode ser uma assinatura, um grupo de recursos ou um único recurso. Leia as [controle de acesso baseado em função no portal do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) artigo para obter informações mais detalhadas sobre o gerenciamento de acesso.

### <a name="built-in-roles"></a>Funções internas

O Azure Stack tem três funções básicas que você pode aplicar a todos os tipos de recursos:

* **Proprietário** pode gerenciar tudo, incluindo o acesso aos recursos.
* **Colaborador** pode gerenciar tudo, exceto o acesso aos recursos.
* **Leitor** podem ver tudo, mas não é possível fazer alterações.

### <a name="resource-hierarchy-and-inheritance"></a>Herança e hierarquia de recursos

O Azure Stack tem a hierarquia de recursos a seguir:

* Cada assinatura pertence a um diretório.
* Cada grupo de recursos pertence a uma assinatura.
* Cada recurso pertence a um grupo de recursos.

O acesso concedido a um escopo pai é herdado em escopos filho. Por exemplo: 

* Você pode atribuir a função Leitor a um grupo do Azure AD no escopo de assinatura. Os membros desse grupo podem exibir cada recurso e grupo de recursos na assinatura.
* Você pode atribuir a função Colaborador para um aplicativo no escopo do grupo de recursos. O aplicativo pode gerenciar recursos de todos os tipos no grupo de recursos, mas não outros grupos de recursos na assinatura.

### <a name="assigning-roles"></a>Atribuição de funções

Você pode atribuir mais de uma função a um usuário e cada função pode ser associada um escopo diferente. Por exemplo: 

* Você pode atribuir a função de leitor de TestUser-A o à assinatura 1.
* Você atribui a função TestUser-A o proprietário a TestVM-1.

O Azure [atribuições de função](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) artigo fornece informações detalhadas sobre como exibir, atribuir e excluir funções.

### <a name="resource-hierarchy-and-inheritance"></a>Herança e hierarquia de recursos

O Azure Stack tem a hierarquia de recursos a seguir:

* Cada assinatura pertence a um diretório.
* Cada grupo de recursos pertence a uma assinatura.
* Cada recurso pertence a um grupo de recursos.

O acesso concedido a um escopo pai é herdado em escopos filho. Por exemplo: 

* Você atribui a **leitor** função para um grupo do AD do Azure no escopo da assinatura. Os membros desse grupo podem exibir cada recurso e grupo de recursos na assinatura.
* Você atribui a **Colaborador** função a um aplicativo no escopo do grupo de recursos. O aplicativo pode gerenciar recursos de todos os tipos no grupo de recursos, mas não outros grupos de recursos na assinatura.

### <a name="assigning-roles"></a>Atribuição de funções

Você pode atribuir mais de uma função a um usuário e cada função pode ser associada um escopo diferente. Por exemplo: 

* Você pode atribuir a função de leitor de TestUser-A o à assinatura 1.
* Você atribui a função TestUser-A o proprietário a TestVM-1.

O Azure [atribuições de função](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) artigo fornece informações detalhadas sobre como exibir, atribuir e excluir funções.

## <a name="set-access-permissions-for-a-user"></a>Definir permissões de acesso para um usuário

As etapas a seguir descrevem como configurar permissões para um usuário.

1. Entre com uma conta que tenha permissões de proprietário ao recurso que deseja gerenciar.
2. No painel de navegação esquerdo, selecione **Grupos de recursos**.
3. Escolha o nome do grupo de recursos que você deseja definir permissões em.
4. No painel de navegação do grupo de recursos, escolha **controle de acesso (IAM)**. O **atribuições de função** exibição lista os itens que têm acesso ao grupo de recursos. Você pode filtrar e agrupar os resultados.
5. Sobre o **controle de acesso** menu da barra, escolha **Adicionar atribuição de função**.
6. Na **Adicionar atribuição de função** painel:

   * Escolha a função que você deseja atribuir a partir de **função** lista suspensa.
   * Escolha o recurso que você deseja atribuir a partir de **atribuir acesso a** lista suspensa.
   * Selecione o usuário, o grupo ou o aplicativo ao qual você deseja conceder acesso. Você pode pesquisar o diretório por nomes para exibição, endereços de email e identificadores de objeto.

7. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

[Criar entidades de serviço](azure-stack-create-service-principals.md)