---
title: Gerenciar permissões para recursos por usuário no Azure pilha | Microsoft Docs
description: Como um administrador de serviços ou Locatário, saiba como gerenciar permissões RBAC.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 026c686b1d5654aa50dd63b9addd619dd5322da0
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808138"
---
# <a name="manage-access-to-resources-with-azure-stack-role-based-access-control"></a>Gerenciar o acesso a recursos com controle de acesso Azure Stack Role-Based

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

A pilha do Azure oferece suporte a controle de acesso baseado em função (RBAC), o mesmo [modelo de segurança para gerenciamento de acesso](https://docs.microsoft.com/azure/role-based-access-control/overview) que usa o Microsoft Azure. Você pode usar o RBAC para gerenciar o acesso do aplicativo para assinaturas, recursos e serviços, grupo ou usuário.

## <a name="basics-of-access-management"></a>Noções básicas de gerenciamento de acesso

Controle de acesso baseado em função fornece controle de acesso refinado que você pode usar para proteger seu ambiente. Você dar aos usuários as permissões exatas necessárias ao atribuir uma função RBAC em um determinado escopo. O escopo da atribuição de função pode ser uma assinatura, um grupo de recursos ou um único recurso. Leitura de [controle de acesso baseado em função no portal do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) artigo para obter informações mais detalhadas sobre o gerenciamento de acesso.

### <a name="built-in-roles"></a>Funções internas

A pilha do Azure tem três funções básicas que você pode aplicar a todos os tipos de recursos:

* **Proprietário** podem gerenciar tudo, incluindo o acesso aos recursos.
* **Colaborador** podem gerenciar tudo, exceto o acesso aos recursos.
* **Leitor** podem exibir tudo, mas não é possível fazer alterações.

### <a name="resource-hierarchy-and-inheritance"></a>Herança e hierarquia de recursos

A pilha do Azure tem a hierarquia de recursos a seguir:

* Cada assinatura pertence a um diretório.
* Cada grupo de recursos pertence a uma assinatura.
* Cada recurso pertence a um grupo de recursos.

Acesso concedido em um escopo pai é herdado em escopos filho. Por exemplo: 

* Você pode atribuir a função Leitor a um grupo do Azure AD no escopo de assinatura. Os membros desse grupo podem exibir cada recurso e grupo de recursos na assinatura.
* Você pode atribuir a função Colaborador para um aplicativo no escopo do grupo de recursos. O aplicativo pode gerenciar recursos de todos os tipos de grupo, mas não de outros grupos de recursos na assinatura.

### <a name="assigning-roles"></a>Atribuição de funções

Você pode atribuir mais de uma função a um usuário e cada função pode ser associada um escopo diferente. Por exemplo: 

* Atribua a função de leitor a TestUser-A assinatura-1.
* Você pode atribuir função de proprietário o TestUser-A TestVM-1.

O Azure [atribuições de função](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) artigo fornece informações detalhadas sobre como exibir, atribuir e excluir funções.

### <a name="resource-hierarchy-and-inheritance"></a>Herança e hierarquia de recursos

A pilha do Azure tem a hierarquia de recursos a seguir:

* Cada assinatura pertence a um diretório.
* Cada grupo de recursos pertence a uma assinatura.
* Cada recurso pertence a um grupo de recursos.

Acesso concedido em um escopo pai é herdado em escopos filho. Por exemplo: 

* Você atribui o **leitor** função a um grupo do AD do Azure no escopo da assinatura. Os membros desse grupo podem exibir cada recurso e grupo de recursos na assinatura.
* Você atribui o **Colaborador** função para um aplicativo no escopo do grupo de recursos. O aplicativo pode gerenciar recursos de todos os tipos de grupo, mas não de outros grupos de recursos na assinatura.

### <a name="assigning-roles"></a>Atribuição de funções

Você pode atribuir mais de uma função a um usuário e cada função pode ser associada um escopo diferente. Por exemplo: 

* Atribua a função de leitor a TestUser-A assinatura-1.
* Você pode atribuir função de proprietário o TestUser-A TestVM-1.

O Azure [atribuições de função](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) artigo fornece informações detalhadas sobre como exibir, atribuir e excluir funções.

## <a name="set-access-permissions-for-a-user"></a>Definir permissões de acesso para um usuário

As etapas a seguir descrevem como configurar permissões para um usuário.

1. Entre com uma conta que tenha permissões de proprietário ao recurso que deseja gerenciar.
2. No painel de navegação esquerdo, selecione **Grupos de recursos**.
3. Escolha o nome do grupo de recursos que você deseja definir permissões.
4. No painel de navegação do grupo de recursos, escolha **(IAM) do controle de acesso**. O **controle de acesso** exibição lista os itens que têm acesso ao grupo de recursos. Você pode filtrar esses resultados e use uma barra de menus para adicionar ou remover permissões.
5. Sobre o **controle de acesso** menu da barra, escolha **+ adicionar**.
6. Em **adicionar permissões**:

   * Escolha a função que você deseja atribuir a partir de **função** lista suspensa.
   * Escolha o recurso que você deseja atribuir a partir de **atribuir acesso** lista suspensa.
   * Selecione o usuário, o grupo ou o aplicativo ao qual você deseja conceder acesso. Você pode pesquisar o diretório por nomes para exibição, endereços de email e identificadores de objeto.

7. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

[Criar entidades de serviço](azure-stack-create-service-principals.md)