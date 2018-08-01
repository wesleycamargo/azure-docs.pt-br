---
title: Como gerenciar identidades gerenciadas atribuídas pelo usuário do Azure usando REST
description: Instruções passo a passo de como criar, listar e excluir uma identidade gerenciada atribuída pelo usuário para fazer chamadas à API REST.
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
ms.date: 06/26/2018
ms.author: daveba
ms.openlocfilehash: 70a8c9018cdc2929abc85336211beecf82bf32cb
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188039"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-rest-api-calls"></a>Criar, listar ou excluir uma identidade atribuída pelo usuário usando chamadas à API REST

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

A identidade gerenciada fornece aos serviços do Azure a capacidade de autenticar os serviços compatíveis com a autenticação do Azure AD, sem precisar de credenciais no código. 

Neste artigo, você aprenderá como criar, listar e excluir uma identidade gerenciada atribuída pelo usuário usando o CURL para fazer chamadas à API REST.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a [seção de visão geral](overview.md). **Verifique se examinou a [diferença entre uma identidade atribuída pelo sistema e uma atribuída pelo usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Se você estiver usando o Windows, instale o [subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou use o [Azure Cloud Shell](../../cloud-shell/overview.md) no portal do Azure.
- Se você usa o [subsistema do Windows para Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) ou um [SO de distribuição do Linux](/azure/install-azure-cli), [instale o console local da CLI do Azure](https://msdn.microsoft.com/commandline/wsl/about).
- Se você estiver usando o console local da CLI do Azure, entre no Azure usando `az login` com uma conta associada à assinatura do Azure que você deseja para implantar ou recuperar informações de identidades gerenciadas atribuídas pelo usuário.
- Para realizar as operações de gerenciamento deste artigo, sua conta precisará das seguintes atribuições de função:
    - [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) função para criar, ler (listar), atualizar e excluir uma identidade atribuída ao usuário.
    - Função de [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para ler (listar) as propriedades de uma identidade atribuída ao usuário.
- Recupere um token de acesso de portador usando `az account get-access-token` para executar as seguintes operações de identidade gerenciada atribuída pelo usuário.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída pelo usuário 

Para criar uma identidade gerenciada atribuída pelo usuário, use a seguinte solicitação CURL para a API do Azure Resource Manager. Substitua os valores `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>`,`<LOCATION>` e `<ACCESS TOKEN>` pelos seus próprios valores:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="list-user-assigned-managed-identities"></a>Listar identidades gerenciadas atribuídas pelo usuário

Para listar as identidades gerenciadas atribuídas pelo usuário, use a seguinte solicitação CURL para a API do Azure Resource Manager. Substitua os valores `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` e `<ACCESS TOKEN>` pelos seus próprios valores:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```
## <a name="delete-a-user-assigned-managed-identity"></a>Excluir uma identidade gerenciada atribuída pelo usuário

Para excluir uma identidade gerenciada atribuída pelo usuário, use a seguinte solicitação CURL para a API do Azure Resource Manager. Substitua os parâmetros `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` e `<ACCESS TOKEN>` pelos seus próprios valores:

> [!NOTE]
> A exclusão de uma identidade atribuída pelo usuário não removerá a referência de nenhum recurso ao qual ela foi atribuída. Para remover uma identidade gerenciada atribuída pelo usuário de uma VM usando o CURL, confira [Remover uma identidade atribuída pelo usuário de uma VM do Azure](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>Próximas etapas

Para obter informações de como atribuir uma identidade atribuída pelo usuário a uma VM ou a um VMSS do Azure usando o CURL, confira [Configurar identidades gerenciadas em uma VM do Azure usando o CURL](qs-configure-rest-vm.md#user-assigned-identity) e [Configurar identidades gerenciadas em um conjunto de dimensionamento de máquinas virtuais usando o CURL](qs-configure-rest-vmss.md#user-assigned-identity).


