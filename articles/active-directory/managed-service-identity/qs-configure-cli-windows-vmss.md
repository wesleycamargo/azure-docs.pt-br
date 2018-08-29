---
title: Como configurar identidades atribuídas pelo sistema e pelo usuário em um VMSS do Azure usando a CLI do Azure
description: Instruções passo a passo para configurar as identidades atribuídas pelo sistema e pelo usuário em um VMSS do Azure, usando a CLI do Azure.
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
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: 225fd7800f05514e989ec0153b5de22e63b62bde
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42146093"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Configurar uma MSI (Identidade de Serviço Gerenciada) do conjunto de dimensionamento de máquinas virtuais usando CLI do Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá como executar as seguintes operações de Identidade de Serviço Gerenciado em um Conjunto de Escala de Máquina Virtual do Azure (VMSS), usando a CLI do Azure:
- Habilitar e desabilitar a identidade atribuída ao sistema em um VMSS do Azure
- Adicionar e remover uma identidade atribuída pelo sistema em uma VMSS do Azure


## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a [seção de visão geral](overview.md). **Verifique se examinou a [diferença entre uma identidade atribuída pelo sistema e uma atribuída pelo usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações de gerenciamento deste artigo, a conta precisará das seguintes atribuições de função:
    - [Colaborador da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para criar um conjunto de dimensionamento de máquinas virtuais e habilitar e remover identidade gerenciada atribuída ao usuário e/ou sistema de um conjunto de dimensionamento de máquinas virtuais.
    - Função de [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) para criar uma identidade atribuída ao usuário.
    - Função de [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para atribuir e remover uma identidade atribuída ao usuário de e para um conjunto de dimensionamento de máquinas virtuais.
- Para executar os exemplos de script da CLI, você tem três opções:
    - Usar o [Azure Cloud Shell](../../cloud-shell/overview.md) no Portal do Azure (confira a próxima seção).
    - Usar o Azure Cloud Shell inserido por meio do botão "Experimentar", localizado no canto superior direito de cada bloco de código.
    - [Instale a última versão da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli), se preferir usar um console de CLI local. 
      
      > [!NOTE]
      > Os comandos foram atualizados para refletir a última versão da [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Identidade atribuída pelo sistema

Nesta seção, você aprenderá como habilitar e desabilitar a identidade atribuída ao sistema para um VMSS do Azure usando a CLI do Azure.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Habilitar identidade atribuída ao sistema durante a criação de um conjunto de dimensionamento da máquina virtual do Azure

Para criar uma escala de máquina virtual definida com a identidade atribuída ao sistema ativada:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/reference-index#az-login). Use uma conta associada à assinatura do Azure, na qual você deseja implantar o conjunto de dimensionamento de máquinas virtuais:

   ```azurecli-interactive
   az login
   ```

2. Crie um [grupo de recursos](../../azure-resource-manager/resource-group-overview.md#terminology) para confinamento e implantação do conjunto de dimensionamento de máquinas virtuais e os recursos relacionados, usando [az group create](/cli/azure/group/#az-group-create). Se você já tiver um grupo de recursos e quiser usá-lo, ignore esta etapa:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Crie um conjunto de dimensionamento de máquinas virtuais usando [az vmss create](/cli/azure/vmss/#az-vmss-create) . O exemplo a seguir cria um conjunto de dimensionamento de máquina virtual chamado *myVMSS* com uma identidade designada pelo sistema, conforme solicitado pelo parâmetro `--assign-identity`. Os parâmetros `--admin-username` e `--admin-password` especificam o nome de usuário e a senha do usuário administrativo para a entrada na máquina virtual. Atualize esses valores como adequado ao seu ambiente: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Ativar identidade atribuída ao sistema em um conjunto de dimensionamento de máquina virtual do Azure existente

Se você precisar ativar a identidade atribuída ao sistema em um conjunto de dimensionamento de máquina virtual do Azure existente:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/reference-index#az-login). Use uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.

   ```azurecli-interactive
   az login
   ```

2. Use o comando [ az vmss identity assign ](/cli/azure/vmss/identity/#az-vmss-identity-assign) para ativar uma identidade designada pelo sistema para uma VM existente:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Desabilitar a identidade do sistema atribuído de um conjunto de escala de máquina virtual do Azure

Caso tenha um conjunto de dimensionamento de máquinas virtuais que não precise mais da identidade atribuída ao sistema, mas ainda precise de identidades atribuídas ao usuário, use o seguinte comando:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Caso tenha um conjunto de dimensionamento de máquinas virtuais que não precise mais da identidade atribuída ao sistema e não tenha identidades atribuídas ao usuário, use o seguinte comando:

> [!NOTE]
> O valor `none` diferencia maiúsculas de minúsculas. Deve estar em letras minúsculas. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

Para remover a extensão do MSI VM, use [identidade de vmss az remover](/cli/azure/vmss/identity/#az-vmss-remove-identity) comando para remover a identidade do sistema atribuído de um VMSS:

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

## <a name="user-assigned-identity"></a>Identidade atribuída pelo usuário

Nesta seção, você aprenderá como habilitar e remover uma identidade atribuída pelo usuário usando a CLI do Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Atribuir uma identidade atribuída pelo usuário durante a criação de um conjunto de dimensionamento de máquinas virtuais

Esta seção orienta você na criação de um VMSS e na atribuição de uma identidade atribuída pelo usuário ao VMSS. Se você já tem um VMSS que deseja usar, pule esta seção e prossiga para a próxima.

1. Se você já tiver um grupo de recursos e quiser usá-lo, ignore esta etapa. Crie um [ grupo de recursos ](~/articles/azure-resource-manager/resource-group-overview.md#terminology) para contenção e implementação de sua identidade designada pelo usuário, usando [ az group create ](/cli/azure/group/#az-group-create). Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<LOCATION>` pelos seus próprios valores. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Crie uma identidade atribuída pelo usuário usando [ az identity create ](/cli/azure/identity#az-identity-create).  O parâmetro `-g` especifica o grupo de recursos onde a identidade definida pelo usuário é criada e o parâmetro `-n` especifica seu nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   A resposta contém detalhes para a identidade atribuída pelo usuário criada, semelhante ao seguinte. O valor de `id` do recurso atribuído à identidade atribuída pelo usuário é usado na etapa a seguir.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Crie um usando VMSS [vmss az criar](/cli/azure/vmss/#az-vmss-create). O exemplo a seguir cria um VMSS associado à nova identidade atribuída pelo usuário, conforme especificado pelo parâmetro `--assign-identity`. Substitua os valores dos parâmetros `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` e `<USER ASSIGNED IDENTITY>` pelos seus próprios valores. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-virtual-machine-scale-set"></a>Atribuir uma identidade atribuída pelo usuário a um conjunto de dimensionamento de máquinas virtuais existente

1. Crie uma identidade atribuída pelo usuário usando [ az identity create ](/cli/azure/identity#az-identity-create).  O parâmetro `-g` especifica o grupo de recursos onde a identidade definida pelo usuário é criada e o parâmetro `-n` especifica seu nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

    > [!IMPORTANT]
    > No momento, não há suporte para a criação de identidades atribuídas pelo usuário com caracteres especiais (isto é, sublinhado) no nome. Use caracteres alfanuméricos. Procure novamente por atualizações.  Para mais informações, consulte [Perguntas frequentes e problemas conhecidos](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
A resposta contém detalhes para a identidade atribuída pelo usuário criada, semelhante ao seguinte.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Atribua a identidade atribuída pelo usuário ao seu VMSS usando [ az vmss identity assign ](/cli/azure/vmss/identity#az-vm-assign-identity). Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VMSS NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY>` é a propriedade de recurso `name` da identidade atribuída pelo usuário, conforme criado na etapa anterior:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Remover uma identidade atribuída ao usuário de um conjunto de dimensionamento de máquinas virtuais

Para remover uma identidade atribuída ao usuário de um conjunto de dimensionamento de máquinas virtuais, use [az vmss identity remove](/cli/azure/vmss/identity#az-vmss-identity-remove). Se essa for a única identidade atribuída pelo usuário atribuída ao conjunto de dimensionamento de máquinas virtuais, `UserAssigned` será removido do valor do tipo de identidade.  Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VMSS NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY>` será a propriedade `name` da identidade atribuída pelo usuário, que pode ser localizada na seção de identidade do conjunto de dimensionamento de máquinas virtuais usando `az vmss identity show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY>
```

Se o seu conjunto de dimensionamento de máquinas virtuais não tiver uma identidade atribuída ao sistema e você quiser remover todas as identidades atribuídas ao usuário desse conjunto, use o seguinte comando:

> [!NOTE]
> O valor `none` diferencia maiúsculas de minúsculas. Deve estar em letras minúsculas.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Se o conjunto de dimensionamento de máquinas virtuais tiver identidades atribuídas ao sistema e ao usuário, será possível remover todas as identidades atribuídas ao usuário optando por usar somente as atribuídas ao sistema. Use o seguinte comando:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da Identidade do Serviço Gerenciado](overview.md)
- Para Início Rápido da criação do conjunto de dimensionamento de máquinas virtuais do Azure completo, consulte: 

  - [Criar um conjunto de dimensionamento de máquinas virtuais com CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















