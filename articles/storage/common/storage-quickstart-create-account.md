---
title: 'Início Rápido: Criar uma conta de armazenamento – Armazenamento do Azure'
description: Neste início rápido, você aprende a criar uma conta de armazenamento usando o portal do Azure, o Azure PowerShell ou a CLI do Azure. Uma conta de armazenamento do Azure fornece um namespace exclusivo no Microsoft Azure para armazenar e acessar os objetos de dados que você cria no Armazenamento do Azure.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 09/18/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: a695e333f48ed0bbf1ad5656c20964232feff4d7
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990120"
---
# <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Neste início rápido, você aprende a criar uma conta de armazenamento usando o [portal do Azure](https://portal.azure.com/), o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) ou a [CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).  

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

# <a name="portaltabportal"></a>[Portal](#tab/portal)

Nenhuma.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Este início rápido requer o módulo Azure PowerShell versão 3.6 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para localizar a versão atual. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você pode fazer logon no Azure e executar comandos da CLI do Azure de uma das duas formas a seguir:

- Você pode executar comandos da CLI de dentro do portal do Azure, no Azure Cloud Shell 
- Você pode instalar a CLI e executar comandos da CLI localmente  

### <a name="use-azure-cloud-shell"></a>Usar o Azure Cloud Shell

O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Clique no botão **Cloud Shell** no menu no canto superior direito do portal do Azure:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

O botão inicia um shell interativo que você pode usar para executar as etapas neste guia de início rápido:

[![Captura de tela mostrando a janela do Cloud Shell no portal](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

Você também pode instalar e usar a CLI do Azure localmente. Este guia de início rápido exige que você esteja executando a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

---

## <a name="log-in-to-azure"></a>Fazer logon no Azure

# <a name="portaltabportal"></a>[Portal](#tab/portal)

Faça logon no [Portal do Azure](https://portal.azure.com).

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Faça logon na sua assinatura do Azure com o comando `Connect-AzureRmAccount` e siga as instruções na tela para fazer a autenticação.

```powershell
Connect-AzureRmAccount
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para iniciar o Azure Cloud Shell, faça logon no [portal do Azure](https://portal.azure.com).

Para fazer logon em sua instalação local da CLI, execute o comando de logon:

```cli
az login
```

---

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Agora você está pronto para criar sua conta de armazenamento.

Cada conta de armazenamento deve pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contêiner lógico para agrupar seus serviços do Azure. Quando você cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou usar um grupo de recursos existente. Este início rápido mostra como criar um novo grupo de recursos. 

Uma conta de armazenamento de **uso geral v2** fornece acesso a todos os serviços de Armazenamento do Azure: blobs, arquivos, filas, tabelas e discos. O guia de início rápido cria uma conta de armazenamento de uso geral v2, mas as etapas para criar qualquer tipo de conta de armazenamento são semelhantes.   

# <a name="portaltabportal"></a>[Portal](#tab/portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Primeiro, crie um novo grupo de recursos com o PowerShell usando o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup): 

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

Se não tiver certeza de qual região especificar para o parâmetro `-Location`, é possível recuperar uma lista de regiões com suporte para sua assinatura com o comando [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation):

```powershell
Get-AzureRmLocation | select Location 
$location = "westus"
```

Em seguida, crie uma conta de armazenamento v2 de uso geral com LRS (armazenamento localmente redundante). Use o comando [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount): 

```powershell
New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 
```

Para criar uma conta de armazenamento de uso geral v2 com armazenamento com redundância de zona (ZRS) (versão prévia), armazenamento com redundância geográfica (GRS) ou armazenamento com redundância geográfica com acesso de leitura (RA-GRS), substitua o valor desejado na tabela a seguir pelo parâmetro **SkuName**. 

|Opção de replicação  |Parâmetro SkuName  |
|---------|---------|
|Armazenamento com redundância local (LRS)     |Standard_LRS         |
|Armazenamento com redundância de zona (ZRS)     |Standard_ZRS         |
|Armazenamento com redundância geográfica (GRS)     |Standard_GRS         |
|Armazenamento com redundância geográfica com acesso de leitura (GRS)     |Standard_RAGRS         |

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, crie criar um novo grupo de recursos com a CLI do Azure usando o comando [az group create](/cli/azure/group#az_group_create). 

```azurecli-interactive
az group create \
    --name storage-quickstart-resource-group \
    --location westus
```

Se não tiver certeza de qual região especificar para o parâmetro `--location`, recupere uma lista de regiões com suporte para a assinatura com o comando [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Em seguida, crie uma conta de armazenamento v2 de uso geral com armazenamento localmente redundante. Use o comando [az storage account create](/cli/azure/storage/account#az_storage_account_create):

```azurecli-interactive
az storage account create \
    --name storagequickstart \
    --resource-group storage-quickstart-resource-group \
    --location westus \
    --sku Standard_LRS \
    --kind StorageV2
```

Para criar uma conta de armazenamento de uso geral v2 com armazenamento com redundância de zona (ZRS Versão prévia), armazenamento com redundância geográfica (GRS) ou armazenamento com redundância geográfica com acesso de leitura (RA-GRS), substitua o valor desejado na tabela a seguir pelo parâmetro **sku**. 

|Opção de replicação  |Parâmetro sku  |
|---------|---------|
|Armazenamento com redundância local (LRS)     |Standard_LRS         |
|Armazenamento com redundância de zona (ZRS)     |Standard_ZRS         |
|Armazenamento com redundância geográfica (GRS)     |Standard_GRS         |
|Armazenamento com redundância geográfica com acesso de leitura (GRS)     |Standard_RAGRS         |

---

Para saber mais sobre as opções de replicação disponíveis , veja [Opções de replicação de armazenamento](storage-redundancy.md).

## <a name="clean-up-resources"></a>Limpar recursos

Caso queira limpar os recursos criados por este guia de início rápido, você pode simplesmente excluir o grupo de recursos. A exclusão do grupo de recursos também exclui a conta de armazenamento associada e todos os outros recursos associados ao grupo de recursos.

# <a name="portaltabportal"></a>[Portal](#tab/portal)

Para remover um grupo de recursos usando o portal do Azure:

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha **Grupo de Recursos** para exibir a lista dos seus grupos de recursos.
2. Localize o grupo de recursos a ser excluído e clique com o botão direito do mouse no botão **Mais** (**...**) do lado direito da lista.
3. Selecione **Excluir grupo de recursos** e confirme.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup): 

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

---

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma conta de armazenamento padrão de uso geral. Para saber como carregar e baixar blobs para e da sua conta de armazenamento, prossiga para o guia de início rápido do Armazenamento de blobs.

# <a name="portaltabportal"></a>[Portal](#tab/portal)

> [!div class="nextstepaction"]
> [Trabalhar com blobs usando o portal do Azure](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

> [!div class="nextstepaction"]
> [Trabalhar com blobs usando o PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Trabalhar com o armazenamento de blobs usando a CLI do Azure](../blobs/storage-quickstart-blobs-cli.md)

---
