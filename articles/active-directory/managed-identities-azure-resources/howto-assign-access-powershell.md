---
title: Como atribuir um acesso de identidade gerenciada a um recurso do Azure usando o PowerShell
description: Instruções passo a passo para atribuir uma identidade gerenciada em um recurso, acessar outro recurso, usando o PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: daveba
ms.openlocfilehash: 72e05af92e88dc04f470d8be9a65347672777556
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427654"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Atribuir um acesso de identidade gerenciada a um recurso usando o PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Após configurar um recurso do Azure com uma identidade gerenciada, será possível conceder o acesso de identidade gerenciada a outro recurso, assim como qualquer entidade de segurança. Este exemplo mostra como conceder acesso de uma identidade gerenciada da máquina virtual do Azure para uma conta de armazenamento do Azure usando o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [a versão mais recente do Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) se ainda não o fez.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Usar o RBAC para atribuir um acesso de identidade gerenciada a outro recurso

Após habilitar a identidade gerenciada em um recurso do Azure, [como uma VM do Azure](qs-configure-powershell-windows-vm.md):

1. Entrar no Azure usando o cmdlet `Connect-AzureRmAccount`. Use uma conta que está associada à assinatura do Azure na qual você configurou a identidade gerenciada:

   ```powershell
   Connect-AzureRmAccount
   ```
2. Neste exemplo, fornecemos um acesso da VM do Azure para uma conta de armazenamento. Primeiro, usamos [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm) para obter a entidade de serviço da VM chamada `myVM`, que foi criada quando habilitamos a identidade gerenciada. Em seguida, usamos [New-AzureRmRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment) para conceder à VM acesso de **Leitor** para uma conta de armazenamento chamada `myStorageAcct`:

    ```powershell
    $spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Próximas etapas

- [Identidade gerenciada para visão geral dos recursos do Azure](overview.md)
- Para habilitar a identidade gerenciada em uma máquina virtual do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando o PowerShell](qs-configure-powershell-windows-vm.md).
