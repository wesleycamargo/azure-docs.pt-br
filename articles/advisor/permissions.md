---
title: Permissões no Assistente do Azure
description: Permissões de Supervisor e como eles podem bloquear sua capacidade de configurar assinaturas ou adiar ou ignorar as recomendações.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: cbd2e456c96dbf8ca01387f0c7c17a1541dbfe55
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59052786"
---
# <a name="permissions-in-azure-advisor"></a>Permissões no Assistente do Azure

O Azure Advisor fornece recomendações com base no uso e da configuração de seus recursos do Azure e assinaturas. Supervisor usa o [funções internas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) fornecidas pelo [controle de acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) para gerenciar o acesso aos recursos do orientador e recomendações. 

## <a name="roles-and-their-access"></a>Funções e o acesso

A tabela a seguir define as funções e o acesso que eles têm no Advisor:

| **Função** | **Exibir recomendações** | **Editar regras** | **Editar configuração de assinatura** | **Editar configuração do grupo de recursos**| **Ignorar e adiar recomendações**|
|---|:---:|:---:|:---:|:---:|:---:|
|Proprietário da assinatura|**X**|**X**|**X**|**X**|**X**|
|Colaborador da assinatura|**X**|**X**|**X**|**X**|**X**|
|Leitor de assinatura|**X**|--|--|--|--|
|Proprietário do grupo de recursos|**X**|--|--|**X**|**X**|
|Colaborador do grupo de recursos|**X**|--|--|**X**|**X**|
|Leitor do grupo de recursos|**X**|--|--|--|--|
|Proprietário do recurso|**X**|--|--|--|**X**|
|Colaborador de recursos|**X**|--|--|--|**X**|
|Leitor de recursos|**X**|--|--|--|--|

> [!NOTE]
> Acesso para exibir as recomendações é dependente de seu acesso ao recurso de afetado da recomendação.

## <a name="permissions-and-unavailable-actions"></a>Permissões e ações disponíveis

Falta de permissões adequadas pode bloquear sua capacidade de executar ações no Advisor. A seguir estão alguns problemas comuns.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Não é possível configurar assinaturas ou grupos de recursos

Quando você tenta configurar assinaturas ou grupos de recursos no assistente, você poderá ver que a opção para incluir ou excluir está desabilitada. Este status indica que você não tem um nível suficiente de permissão para esse grupo de recursos ou assinatura. Para resolver esse problema, saiba como [conceder acesso de usuário](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Não é possível adiar ou ignorar uma recomendação

Se você receber um erro ao tentar adiar ou ignorar uma recomendação, você pode não ter permissões suficientes. Certifique-se de que você tenha pelo menos acesso de Colaborador para o recurso afetado da recomendação estiver adiar ou ignorar. Para resolver esse problema, saiba como [conceder acesso de usuário](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

## <a name="next-steps"></a>Próximas etapas

Este artigo forneceu uma visão geral de como o Supervisor usa o RBAC para controlar as permissões de usuário e como resolver problemas comuns. Para saber mais sobre o Assistente, consulte:

- [O que é Azure Advisor?](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Introdução ao Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-get-started)
