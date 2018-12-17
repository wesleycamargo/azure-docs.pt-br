---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: aeefb63a283c473a98639ead1aa6640d88409125
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318123"
---
## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre na sua assinatura do Azure com o comando `Connect-AzureRmAccount` e siga as instruções na tela.

```powershell
Connect-AzureRmAccount
```

Se você não sabe qual localização deseja usar, você pode listar as localizações disponíveis. Exiba a lista de locais usando o seguinte exemplo de código e encontrar aquele que você deseja usar. Esse exemplo usa **eastus**. Armazene o local em uma variável e use a variável para que você possa alterá-la em um único local.

```powershell
Get-AzureRmLocation | select Location
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

```powershell
$resourceGroup = "myResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de armazenamento padrão de uso geral com replicação LRS usando [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). A seguir, obtenha o contexto da conta de armazenamento que define a conta de armazenamento que você deseja usar. Ao agir em uma conta de armazenamento, você pode referenciar o contexto em vez de passar repetidamente as credenciais. Use o exemplo a seguir para criar uma conta de armazenamento chamada *mystorageaccount* com armazenamento com redundância local (LRS) e criptografia de blob (habilitada por padrão).

```powershell
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `

$ctx = $storageAccount.Context
```
