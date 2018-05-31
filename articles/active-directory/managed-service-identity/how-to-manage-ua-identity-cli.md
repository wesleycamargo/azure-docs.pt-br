---
title: Como gerenciar uma Identidade do Serviço Gerenciado (MSI) atribuída pelo usuário usando a CLI do Azure
description: Instruções passo a passo sobre como criar, listar e excluir uma identidade de serviço gerenciado atribuída pelo usuário usando a CLI do Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 5262914e469bdc07921c3b82e990d544349b5fd4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930659"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-cli"></a>Criar, listar ou excluir uma identidade atribuída pelo usando a CLI do Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que seja compatível com a autenticação do Azure Active Directory sem precisar ter as credenciais no seu código. 

Neste artigo, você aprenderá a criar, listar e excluir uma identidade atribuída pelo usuário usando a CLI do Azure.

## <a name="prerequisites"></a>pré-requisitos

- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a [seção de visão geral](overview.md). **Verifique se examinou a [diferença entre uma identidade atribuída pelo sistema e uma atribuída pelo usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

- Para executar os exemplos de script da CLI, você tem três opções:

    - Usar o [Azure Cloud Shell](../../cloud-shell/overview.md) no Portal do Azure (confira a próxima seção).
    - Usar o Azure Cloud Shell inserido por meio do botão "Experimentar", localizado no canto superior direito de cada bloco de código.
    - [Instale a versão mais recente da CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou posterior) se você preferir usar um console local da CLI. Entre no Azure usando `az login`, uma conta que esteja associada à assinatura do Azure sob a qual você deseja implantar a identidade atribuída pelo usuário.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída pelo usuário 

Para criar uma identidade atribuída pelo usuário, use o comando [az identity create](/cli/azure/identity#az-identity-create). O parâmetro `-g` especifica o grupo de recursos no qual a identidade atribuída pelo usuário é criada, e o parâmetro `-n` especifica seu nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

> [!IMPORTANT]
> A criação de identidades atribuídas pelo usuário oferece suporte somente a caracteres alfanuméricos e hífen (0-9 ou a-z ou A-Z ou -). Além disso, o nome deve ter um limite de 24 caracteres para que a atribuição a VM/VMSS funcione corretamente. Procure novamente por atualizações. Para obter mais informações, consulte [Perguntas frequentes e problemas conhecidos](known-issues.md)

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Listar identidades atribuídas pelo usuário

Para listar as identidades atribuídas pelo usuário, use o comando [az identity list](/cli/azure/identity#az-identity-list).  O parâmetro `-g` especifica o grupo de recursos onde a identidade atribuída pelo usuário foi criada.  Substitua o `<RESOURCE GROUP>` por seu próprio valor:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
Na resposta json, identidades de usuário têm valor `"Microsoft.ManagedIdentity/userAssignedIdentities"` retornado para a chave, `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-identity"></a>Excluir uma identidade atribuída pelo usuário

Para excluir uma identidade atribuída pelo usuário, use o comando [az identity delete](/cli/azure/identity#az-identity-delete).  O parâmetro -n especifica seu nome e o parâmetro -g especifica o grupo de recursos onde a identidade definida pelo usuário foi criada.  Substitua os valores de parâmetros `<USER ASSIGNED IDENTITY NAME>` e `<RESOURCE GROUP>` pelos seus próprios valores:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Excluir uma identidade atribuída pelo usuário não removerá a referência de nenhum recurso ao qual ela foi atribuída. Remova-as da VM/VMSS usando o comando `az vm/vmss identity remove`

## <a name="related-content"></a>Conteúdo relacionado

Para obter uma lista completa de comandos de identidade da CLI do Azure, consulte [az identity](/cli/azure/identity).

Para obter informações sobre como atribuir uma identidade atribuída pelo usuário para uma VM do Azure, consulte [Configurar Identidade de Serviço Gerenciada (MSI) usando a CLI do Azure](qs-configure-cli-windows-vm.md#user-assigned-identity)


 
