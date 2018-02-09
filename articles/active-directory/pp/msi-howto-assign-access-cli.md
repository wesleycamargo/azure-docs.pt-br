---
title: "Como atribuir um acesso de MSI atribuída pelo usuário a um recurso do Azure usando a CLI do Azure"
description: "Instruções passo a passo sobre como atribuir uma MSI atribuída pelo usuário em um recurso, acesso a outro recurso, usando a CLI do Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5bea41999f59fe8be7ae0a0bd5b726527beeddd5
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="assign-a-user-assigned-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Atribuir um acesso de MSI (Identidade de Serviço Gerenciado) atribuída pelo usuário a um recurso usando a CLI do Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Depois de criar uma MSI atribuída pelo usuário, você pode conceder o acesso de MSI a outro recurso, assim como qualquer entidade de segurança. Este exemplo mostra como conceder o acesso de MSI atribuída pelo usuário a uma conta de Armazenamento do Azure usando a CLI do Azure.

## <a name="prerequisites"></a>pré-requisitos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Para executar os exemplos de script CLI neste tutorial, você tem duas opções:

- Usar o [Azure Cloud Shell](~/articles/cloud-shell/overview.md), seja pelo Portal do Azure ou por meio do botão "Experimentar", localizado no canto superior direito de cada bloco de código.
- [Instale a versão mais recente da CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 ou posterior) se preferir usar um console local da CLI. Em seguida, entre Azure usando [az login](/cli/azure/#az_login). Use uma conta que esteja associada à assinatura do Azure sob a qual você deseja implantar a VM e a MSI atribuída pelo usuário:

   ```azurecli
   az login
   ```

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Usar o RBAC para atribuir o acesso de MSI a outro recurso

Para usar uma MSI com um recurso de host (como uma VM), para acesso de entrada ou de recurso, a MSI deve primeiro receber acesso a recursos por meio da atribuição de função. Você pode fazer isso antes de associar a MSI ao host ou depois. Para ver as etapas completas sobre como criar e associar a uma VM, consulte [Configurar uma MSI atribuída pelo usuário para uma VM usando a CLI do Azure](msi-qs-configure-cli-windows-vm.md).

No exemplo a seguir, uma MSI atribuída pelo usuário recebe acesso a uma conta de armazenamento.  

1. Para conceder o acesso de MSI, é necessário a ID do cliente (também conhecida como ID de aplicativo) da entidade de serviço da MSI. A ID do cliente é fornecida por [az identity create](/cli/azure/identity#az_identity_create), após o provisionamento da MSI e de sua entidade de serviço (mostrados aqui como referência):

   ```azurecli-interactive
   az identity create -g rgPrivate -n msiServiceApp
   ```

   Uma resposta bem-sucedida contém a ID do cliente da entidade de serviço da MSI atribuída pelo usuário, na propriedade `clientId`:

   ```json
   {
        "clientId": "9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp/credentials?tid=733a8f0p-ec41-4e69-8adz-971fc4b533bl&oid=z4baa4fc-fce4-4202-8250-5fb359abblll&aid=9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp",
        "location": "westcentralus",
        "name": "msiServiceApp",
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "resourceGroup": "rgPrivate",
        "tags": {},
        "tenantId": "733a8f0p-ec41-4e69-8adz-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
   }
   ```

2. Depois que a ID do cliente é conhecida, use [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) para atribuir o acesso de MSI a outro recurso. Certifique-se de usar sua ID do cliente para o parâmetro `--assignee`, e a ID de assinatura e o nome do grupo de recursos correspondentes, conforme retornado ao executar `az identity create`. Aqui a MSI recebe acesso de "Leitor" a uma conta de armazenamento chamada "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee 9391e5b1-dada-4a8z-834a-43ad44f296bl --role Reader --scope /subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

   Uma resposta bem-sucedida se assemelha à seguinte:

   ```json
   {
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct/providers/Microsoft.Authorization/roleAssignments/f4766864-9493-43c6-91d7-abd131c3c017",
        "name": "f4766864-9493-43c6-91d7-abd131c3c017",
        "properties": {
            "additionalProperties": {
            "createdBy": null,
            "createdOn": "2017-12-21T20:49:37.5590544Z",
            "updatedBy": "pd78b21f-17a4-41az-b7db-9aadec3376bl",
            "updatedOn": "2017-12-21T20:49:37.5590544Z"
            },
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "roleDefinitionId": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "scope": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct"
        },
        "resourceGroup": "rgPrivate",
        "type": "Microsoft.Authorization/roleAssignments"
   }
   ```

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do MSI, confira [Visão geral da Identidade de Serviço Gerenciado](msi-overview.md).
- Para habilitar a MSI atribuída pelo usuário em uma VM do Azure, confira [Configurar uma MSI (Identidade de Serviço Gerenciado) atribuída pelo usuário para uma VM usando a CLI do Azure](msi-qs-configure-cli-windows-vm.md).

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.

