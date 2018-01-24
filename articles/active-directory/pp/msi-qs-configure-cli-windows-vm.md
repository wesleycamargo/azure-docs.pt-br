---
title: "Como configurar uma MSI atribuída pelo usuário para uma VM do Azure usando a CLI do Azure"
description: "Instruções passo a passo para configurar uma MSI (Identidade de Serviço Gerenciado) atribuída pelo usuário em uma VM do Azure usando a CLI do Azure."
services: active-directory
documentationcenter: 
author: BryanLa
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4b6f4e2b0e42724276448fd4726c8326de8ea6ee
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-azure-cli"></a>Configurar uma MSI (Identidade do Serviço Gerenciado) para uma VM usando a CLI do Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que seja compatível com a autenticação do Azure AD sem precisar ter as credenciais no seu código. 

Neste artigo, você aprenderá a habilitar e a remover uma MSI atribuída pelo usuário de uma VM do Microsoft Azure usando a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Para executar os exemplos de script CLI neste tutorial, você tem duas opções:

- Usar o [Azure Cloud Shell](~/articles/cloud-shell/overview.md), seja pelo Portal do Azure ou por meio do botão "Experimentar", localizado no canto superior direito de cada bloco de código.
- [Instale a versão mais recente da CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 ou posterior) se preferir usar um console local da CLI. Em seguida, entre Azure usando [az login](/cli/azure/#login). Use uma conta que esteja associada à assinatura do Azure sob a qual você deseja implantar a VM e a MSI atribuída pelo usuário:

   ```azurecli
   az login
   ```

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Habilitar o MSI durante a criação de uma VM do Azure

Esta seção orienta você pela criação da VM e a atribuição da MSI atribuída pelo usuário à VM. Se você já tiver uma VM que você deseja usar, ignore esta seção e vá para a próxima.

1. Se você já tiver um grupo de recursos e quiser usá-lo, ignore esta etapa. Crie um [grupo de recursos](~/articles/azure-resource-manager/resource-group-overview.md#terminology) para contenção e implantação de sua MSI usando [az group create](/cli/azure/group/#create). Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<LOCATION>` pelos seus próprios valores. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Crie uma MSI atribuída pelo usuário usando [az identity create](/cli/azure/identity#az_identity_create).  O parâmetro `-g` especifica o grupo de recursos no qual o MSI é criado, enquanto o parâmetro `-n` especifica seu nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<MSI NAME>` pelos seus próprios valores:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
A resposta contém detalhes para a MSI criada atribuída pelo usuário, semelhante ao que é demonstrado a seguir. O valor do recurso `id` atribuído ao MSI é usado na etapa a seguir.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Crie uma VM usando [az vm create](/cli/azure/vm/#create). O exemplo a seguir cria uma VM associada com a nova MSI atribuída pelo usuário, conforme especificado pelo parâmetro `--assign-identity`. Substitua `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` e a propriedade `<`MSI ID>` parameter values with your own values. For `<MSI ID>`, use the user-assigned MSI's resource `id criada na etapa anterior: 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Habilitar MSI em uma VM do Azure existente

1. Crie uma MSI atribuída pelo usuário usando [az identity create](/cli/azure/identity#az_identity_create).  O parâmetro `-g` especifica o grupo de recursos no qual o MSI é criado, enquanto o parâmetro `-n` especifica seu nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<MSI NAME>` pelos seus próprios valores:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
A resposta contém detalhes para a MSI criada atribuída pelo usuário, semelhante ao que é demonstrado a seguir. O valor do recurso `id` atribuído ao MSI é usado na etapa a seguir.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Atribuir a MSI atribuída pelo usuário à VM usando [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. O `<MSI ID>` será a propriedade `id` do recurso da MSI atribuída pelo usuário, conforme criada na etapa anterior:

    ```azurecli-interactive
    az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

## <a name="remove-msi-from-an-azure-vm"></a>Remover o MSI de uma VM do Azure

1. Remover a MSI atribuída pelo usuário da VM usando [az vm remove-identity](/cli/azure/vm#az_vm_remove_identity). Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. O `<MSI NAME>` será a propriedade `name` da MSI atribuída pelo usuário, conforme fornecido durante o comando `az identity create` (consulte os exemplos na seções anteriores):

   ```azurecli-interactive
   az vm remove-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
   ```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da Identidade do Serviço Gerenciado](msi-overview.md)
- Para os guias de início rápido completos sobre VM do Azure, consulte: 

  - [Crie máquinas virtuais Windows com o CLI](~/articles/virtual-machines/windows/quick-create-cli.md)  
  - [Crie máquinas virtuais Linux com o CLI](~/articles/virtual-machines/linux/quick-create-cli.md) 

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.
















