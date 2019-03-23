---
title: Criar uma conta de armazenamento de blob de bloco - armazenamento do Azure | Microsoft Docs
description: Mostra como criar uma conta de armazenamento de blob de blocos do Azure com características de desempenho premium.
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
author: twooley
ms.author: twooley
ms.subservice: blobs
ms.openlocfilehash: 16ecade7a8d0049e098bb06bd14828b19934f5f5
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373039"
---
# <a name="create-a-block-blob-storage-account"></a>Criar uma conta de armazenamento de blob de bloco

O tipo de conta de armazenamento de blob de bloco permite criar blobs de blocos com características de desempenho premium. Esse tipo de conta de armazenamento é otimizado para cargas de trabalho com as taxas de transações alta ou que exigem tempos de acesso muito rápidos. Este artigo mostra como criar uma conta de armazenamento de blob de bloco usando o portal do Azure, a CLI do Azure ou Azure PowerShell.

Para obter mais informações sobre contas de armazenamento de blobs de bloco, consulte [visão geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="create-account-in-the-azure-portal"></a>Criar conta no portal do Azure

Para criar uma conta de armazenamento de blob de bloco no portal do Azure, siga estas etapas:

1. No portal do Azure, selecione **todos os serviços** > o **armazenamento** categoria > **contas de armazenamento**.

1. Sob **contas de armazenamento**, selecione **Add**.

1. No **assinatura** , selecione a assinatura na qual criar a conta de armazenamento.

1. No **grupo de recursos** campo, selecione um grupo de recursos existente ou selecione **criar novo**e insira um nome para o novo grupo de recursos.

1. No **nome da conta de armazenamento** , insira um nome para a conta. Observe as seguintes diretrizes:

   - O nome deve ser exclusivo no Azure.
   - O nome deve ter entre três e 24 caracteres de comprimento.
   - O nome pode incluir apenas números e letras minúsculas.

1. No **local** campo, selecione um local para a conta de armazenamento ou usar o local padrão.

1. Para o restante das configurações, configure o seguinte:

   |Campo     |Valor  |
   |---------|---------|
   |**Desempenho**    |  Selecione **Premium**.   |
   |**Tipo de conta**    | Selecione **BlockBlobStorage**.      |
   |**Replicação**    |  Deixe a configuração padrão de **armazenamento localmente redundante (LRS)**.      |

   ![Mostra o portal da interface do usuário para criar uma conta de armazenamento de blob de bloco](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Selecione **revisar + criar** para examinar as configurações de conta de armazenamento.

1. Selecione **Criar**.

### <a name="create-account-using-azure-powershell"></a>Criar conta usando o Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Abra uma sessão do Windows PowerShell com privilégios elevados (Executar como administrador).

1. Execute o seguinte comando para verificar se a versão mais recente do `Az` módulo PowerShell está instalado.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Abra um novo console do PowerShell e entre com sua conta do Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Se necessário, crie um novo grupo de recursos. Substitua os valores em cotações e execute o comando a seguir.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Crie a conta de armazenamento de blob de bloco. Substitua os valores em cotações e execute o comando a seguir.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="create-account-using-azure-cli"></a>Criar conta usando a CLI do Azure

Para criar uma conta de blob de bloco usando a CLI do Azure, você deve primeiro instalar v CLI do Azure. 2.0.46 ou uma versão posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Entre em sua assinatura do Azure.

   ```azurecli
   az login
   ```

1. Se necessário, crie um novo grupo de recursos. Substitua os valores entre colchetes (incluindo os colchetes) e execute o comando a seguir.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Crie a conta de armazenamento de blob de bloco. Substitua os valores entre colchetes (incluindo os colchetes) e execute o comando a seguir.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Para saber mais sobre os grupos de recursos, confira [Visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
