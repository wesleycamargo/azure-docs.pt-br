---
title: Como configurar identidades atribuídas pelo sistema e pelo usuário em uma VM do Azure usando a CLI do Azure
description: Instruções passo a passo para configurar identidades atribuídas pelo sistema e pelo usuário em uma VM do Azure usando a CLI do Azure.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: db32f56c55f189001e51a727ca6b5703e82dafe4
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903990"
---
# <a name="configure-managed-service-identity-msi-on-an-azure-vm-using-azure-cli"></a>Configurar uma MSI (Identidade do Serviço Gerenciado) em uma VM do Azure usando a CLI do Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá como executar as seguintes operações de Identidade de Serviço Gerenciado em uma VM do Azure usando a CLI do Azure:
- Habilitar e desabilitar a identidade atribuída pelo sistema em uma VM do Azure
- Adicionar e remover uma identidade atribuída pelo sistema em uma VM do Azure

## <a name="prerequisites"></a>pré-requisitos

- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a [seção de visão geral](overview.md). **Verifique se examinou a [diferença entre uma identidade atribuída pelo sistema e uma atribuída pelo usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os exemplos de script da CLI, você tem três opções:

    - Usar o [Azure Cloud Shell](../../cloud-shell/overview.md) no Portal do Azure (confira a próxima seção).
    - Usar o Azure Cloud Shell inserido por meio do botão "Experimentar", localizado no canto superior direito de cada bloco de código.
    - [Instale a versão mais recente da CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou posterior) se você preferir usar um console local da CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Identidade atribuída pelo sistema

Nesta seção, você aprenderá como habilitar e desabilitar a identidade atribuída pelo sistema em uma VM do Azure usando a CLI do Azure.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Habilitar identidade atribuída ao sistema durante a criação de uma VM do Azure

Para criar uma VM do Azure com a identidade atribuída ao sistema ativada:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/reference-index#az_login). Use uma conta que esteja associada à assinatura do Azure sob a qual você deseja implantar a VM:

   ```azurecli-interactive
   az login
   ```

2. Criar um [grupo de recursos](../../azure-resource-manager/resource-group-overview.md#terminology) para contenção e implantação de VM e seus recursos relacionados usando [az group create](/cli/azure/group/#az_group_create). Ignore esta etapa, se você já tiver o grupo de recursos que deseja usar:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Crie uma VM usando [az vm create](/cli/azure/vm/#az_vm_create). O exemplo a seguir cria uma VM denominada *myVM* com uma identidade atribuída pelo sistema, como solicitado pelo parâmetro `--assign-identity`. Os parâmetros `--admin-username` e `--admin-password` especificam o nome de usuário e a senha do usuário administrativo para a entrada na máquina virtual. Atualize esses valores como adequado ao seu ambiente: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Habilitar a identidade atribuída pelo sistema em uma VM existente do Azure

Caso precise habilitar a identidade atribuída pelo sistema em uma VM existente do Azure:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/reference-index#az_login). Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM. Verifique também se sua conta pertence a uma função que fornece permissões de gravação na VM, como "Colaborador da Máquina Virtual":

   ```azurecli-interactive
   az login
   ```

2. Usar [az vm identity assign](/cli/azure/vm/identity/#az_vm_identity_assign) com o comando `identity assign` habilita a identidade atribuída pelo sistema a uma VM existente:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Desativar a identidade atribuída pelo o sistema de uma VM do Azure

> [!NOTE]
> Atualmente, não há suporte para desabilitar a Identidade de Serviço Gerenciada de uma de máquina virtual. Enquanto isso, você pode alternar entre usar identidades atribuídas pelo sistema ou pelo usuário.

Se você tiver uma máquina virtual que não precise mais da identidade atribuída pelo sistema, mas ainda precisar de identidades atribuídas pelo usuário, use o seguinte comando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```
Para remover a extensão de VM da MSI, use `-n ManagedIdentityExtensionForWindows` ou `-n ManagedIdentityExtensionForLinux` (dependendo do tipo de VM) com [az vm extension deletez](https://docs.microsoft.com/cli/azure/vm/#assign-identity):

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

## <a name="user-assigned-identity"></a>Identidade atribuída pelo usuário

Nesta seção, você aprenderá como adicionar e remover uma identidade atribuída pelo usuário de uma VM do Azure usando a CLI do Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Atribuir uma identidade atribuída pelo usuário durante a criação de uma VM do Azure

Esta seção orienta você pela criação de uma VM com atribuição de uma identidade atribuída pelo usuário. Se você já tiver uma VM que você deseja usar, ignore esta seção e vá para a próxima.

1. Se você já tiver um grupo de recursos e quiser usá-lo, ignore esta etapa. Crie um [grupo de recursos](~/articles/azure-resource-manager/resource-group-overview.md#terminology) para contenção e implantação de sua MSI usando [az group create](/cli/azure/group/#az_group_create). Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<LOCATION>` pelos seus próprios valores. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Crie uma identidade atribuída pelo usuário usando [ az identity create ](/cli/azure/identity#az_identity_create).  O parâmetro `-g` especifica o grupo de recursos onde a identidade definida pelo usuário é criada e o parâmetro `-n` especifica seu nome.    
    
[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


```azurecli-interactive
az identity create -g myResourceGroup -n myUserAssignedIdentity
```
A resposta contém detalhes para a identidade atribuída pelo usuário criada, semelhante ao seguinte. O valor de ID do recurso atribuído à identidade atribuída pelo usuário é usado na etapa a seguir.

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

3. Crie uma VM usando [az vm create](/cli/azure/vm/#az_vm_create). O exemplo a seguir cria uma VM associada com a nova identidade atribuída pelo usuário, conforme especificado pelo parâmetro `--assign-identity`. Substitua os valores dos parâmetros `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` e `<MSI ID>` pelos seus próprios valores. Para `<MSI ID>`, use a propriedade `id` do recurso da identidade atribuída pelo usuário, criada na etapa anterior: 

```azurecli-interactive 
az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade atribuída pelo usuário a uma VM do Azure existente

1. Crie uma identidade atribuída pelo usuário usando [ az identity create ](/cli/azure/identity#az-identity-create).  O parâmetro `-g` especifica o grupo de recursos onde a identidade definida pelo usuário é criada e o parâmetro `-n` especifica seu nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<MSI NAME>` pelos seus próprios valores:

    > [!IMPORTANT]
    > No momento, não há suporte para a criação de identidades atribuídas pelo usuário com caracteres especiais (isto é, sublinhado) no nome. Use caracteres alfanuméricos. Procure novamente por atualizações.  Para mais informações, consulte [Perguntas frequentes e problemas conhecidos](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
A resposta contém detalhes para a MSI atribuída pelo usuário criada, semelhante ao que é demonstrado a seguir. O valor de `id` do recurso atribuído à identidade atribuída pelo usuário é usado na etapa a seguir.

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

2. Atribua a identidade atribuída pelo usuário à sua VM usando [az vm identity assign](/cli/azure/vm#az-vm-identity-assign). Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. O `<MSI ID>` será a propriedade `id` do recurso da identidade atribuída pelo usuário, conforme criada na etapa anterior:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Remover uma identidade atribuída pelo usuário em uma VM do Azure

> [!NOTE]
> No momento, não há suporte para a remoção de todas as identidades atribuídas pelo usuário de uma máquina virtual, a menos que você tenha uma identidade atribuída pelo sistema.

Se sua VM tiver várias identidades atribuídas pelo usuário, você poderá remover todas, exceto a última, usando [az vm identity remove](/cli/azure/vm#az-vm-identity-remove). Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. O `<MSI NAME>` a propriedade `name` da identidade atribuída pelo usuário, que pode ser encontrada na seção de identidade da VM usando `az vm show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```
Se sua VM tiver identidades atribuídas pelo sistema e pelo usuário, você poderá remover todas as identidades atribuídas pelo usuário, alternando para usar somente as atribuídas pelo sistema. Use o seguinte comando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null 
```

## <a name="related-content"></a>Conteúdo relacionado
- [Visão geral da Identidade do Serviço Gerenciado](overview.md)
- Para os guias de início rápido completos sobre VM do Azure, consulte: 
  - [Crie máquinas virtuais Windows com o CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Crie máquinas virtuais Linux com o CLI](../../virtual-machines/linux/quick-create-cli.md) 

















