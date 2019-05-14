---
title: Criar uma conta de armazenamento – Armazenamento do Azure
description: Este artigo de instruções, você aprenderá a criar uma conta de armazenamento usando o portal do Azure, Azure PowerShell ou a CLI do Azure. Uma conta de armazenamento do Azure fornece um namespace exclusivo no Microsoft Azure para armazenar e acessar os objetos de dados que você cria no Armazenamento do Azure.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: article
ms.date: 05/06/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 8375f4c54dc436ecf0694ec5f629c81d3591594d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65234151"
---
# <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de armazenamento do Azure contém todos os seus objetos de dados do Armazenamento do Azure: blobs, arquivos, filas, tabelas e discos. A conta de armazenamento fornece um namespace exclusivo para seus dados de armazenamento do Azure que é acessíveis de qualquer lugar no mundo via HTTP ou HTTPS. Dados em sua conta de armazenamento do Azure são durável e altamente disponível, seguro e escalonável.

Este artigo de instruções, você aprenderá a criar uma conta de armazenamento usando o [portal do Azure](https://portal.azure.com/), [do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), [CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest), ou um [do Azure Resource Manager modelo](../../azure-resource-manager/resource-group-overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Nenhuma.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Este artigo de instruções requer o módulo do PowerShell do Azure Az versão 0,7 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão atual. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você pode entrar no Azure e executar comandos da CLI do Azure de uma das duas formas a seguir:

- Você pode executar comandos CLI de dentro do portal do Azure, no Azure Cloud Shell.
- Você pode instalar a CLI e execute os comandos da CLI localmente.

### <a name="use-azure-cloud-shell"></a>Usar o Azure Cloud Shell

O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. A CLI do Azure é pré-instalado e configurada para usar com sua conta. Clique o **Cloud Shell** botão no menu na seção de canto superior direito do portal do Azure:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

O botão inicia um shell interativo que você pode usar para executar as etapas descritas neste artigo de instruções:

[![Captura de tela mostrando a janela do Cloud Shell no portal](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

Você também pode instalar e usar a CLI do Azure localmente. Este artigo de instruções requer que você está executando a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

Nenhuma.

---

## <a name="sign-in-to-azure"></a>Entrar no Azure

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Entre no [Portal do Azure](https://portal.azure.com).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Entre em sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela para fazer a autenticação.

```powershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para iniciar o Azure Cloud Shell, entrar para o [portal do Azure](https://portal.azure.com).

Para fazer logon em sua instalação local da CLI, execute as [login az](/cli/azure/reference-index#az-login) comando:

```cli
az login
```

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

N/D

---

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Agora você está pronto para criar sua conta de armazenamento.

Cada conta de armazenamento deve pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contêiner lógico para agrupar seus serviços do Azure. Quando você cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou usar um grupo de recursos existente. Este artigo mostra como criar um novo grupo de recursos.

Uma conta de armazenamento de **uso geral v2** fornece acesso a todos os serviços de Armazenamento do Azure: blobs, arquivos, filas, tabelas e discos. As etapas descritas aqui criam uma conta de armazenamento de uso geral v2, mas as etapas para criar qualquer tipo de conta de armazenamento são semelhantes.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Primeiro, crie um grupo de recursos com o PowerShell usando o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Caso não tenha certeza de qual região especificar para o parâmetro `-Location`, recupere uma lista de regiões com suporte para sua assinatura com o comando [Get-AzLocation](/powershell/module/az.resources/get-azlocation):

```powershell
Get-AzLocation | select Location
$location = "westus"
```

Em seguida, crie uma conta de armazenamento de uso geral v2 com armazenamento com redundância geográfica de acesso de leitura (RA-GRS) usando o [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) comando. Lembre-se de que o nome da sua conta de armazenamento deve ser exclusivo no Azure, portanto, substitua o valor de espaço reservado entre colchetes com seu próprio valor exclusivo:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2 
```

Para criar uma conta de armazenamento de uso geral v2 com uma opção de replicação diferente, substitua o valor desejado na tabela a seguir para o **SkuName** parâmetro.

|Opção de replicação  |Parâmetro SkuName  |
|---------|---------|
|Armazenamento com redundância local (LRS)     |LRS_Padrão         |
|ZRS (armazenamento com redundância de zona)     |Standard_ZRS         |
|GRS (armazenamento com redundância geográfica)     |Standard_GRS         |
|Armazenamento com redundância geográfica com acesso de leitura (GRS)     |Standard_RAGRS         |

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, crie criar um novo grupo de recursos com a CLI do Azure usando o comando [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create \
    --name storage-resource-group \
    --location westus
```

Se não tiver certeza de qual região especificar para o parâmetro `--location`, recupere uma lista de regiões com suporte para a assinatura com o comando [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Em seguida, crie uma conta de armazenamento de uso geral v2 com armazenamento com redundância geográfica de acesso de leitura usando o [criar conta de armazenamento az](/cli/azure/storage/account#az_storage_account_create) comando. Lembre-se de que o nome da sua conta de armazenamento deve ser exclusivo no Azure, portanto, substitua o valor de espaço reservado entre colchetes com seu próprio valor exclusivo:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

Para criar uma conta de armazenamento de uso geral v2 com uma opção de replicação diferente, substitua o valor desejado na tabela a seguir para o **sku** parâmetro.

|Opção de replicação  |Parâmetro sku  |
|---------|---------|
|Armazenamento com redundância local (LRS)     |LRS_Padrão         |
|ZRS (armazenamento com redundância de zona)     |Standard_ZRS         |
|GRS (armazenamento com redundância geográfica)     |Standard_GRS         |
|Armazenamento com redundância geográfica com acesso de leitura (GRS)     |Standard_RAGRS         |

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

Você pode usar o Azure PowerShell ou a CLI do Azure para implantar um modelo do Resource Manager para criar uma conta de armazenamento. O modelo usado neste artigo de instruções é de [modelos de início rápido do Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Para executar os scripts, selecione **Experimentar** para abrir o Azure Cloud Shell. Para colar o script, clique com o botão direito do mouse no shell e, em seguida, selecione **Colar**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-file "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Para saber como criar modelos, veja:

- [Documentação do Azure Resource Manager](/azure/azure-resource-manager/).
- [Referência de modelo da conta de armazenamento](/azure/templates/microsoft.storage/allversions).
- [Exemplos de modelo de conta de armazenamento adicional](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Para saber mais sobre as opções de replicação disponíveis , veja [Opções de replicação de armazenamento](storage-redundancy.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar todos os recursos criados por este artigo de instruções, você pode excluir o grupo de recursos. A exclusão do grupo de recursos também exclui a conta de armazenamento associada e todos os outros recursos associados ao grupo de recursos.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Para remover um grupo de recursos usando o portal do Azure:

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha **Grupo de Recursos** para exibir a lista dos seus grupos de recursos.
2. Localize o grupo de recursos a ser excluído e clique com o botão direito do mouse no botão **Mais** (**...**) do lado direito da lista.
3. Selecione **Excluir grupo de recursos** e confirme.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name storage-resource-group
```

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o Azure PowerShell ou a CLI do Azure.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Próximas etapas

Este artigo de instruções, você criou uma conta de armazenamento padrão de uso geral v2. Para saber como carregar e baixar blobs para e de sua conta de armazenamento, continue a um dos inícios rápidos do armazenamento de Blob.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Trabalhar com blobs usando o portal do Azure](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Trabalhar com blobs usando o PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Trabalhar com blobs usando a CLI do Azure](../blobs/storage-quickstart-blobs-cli.md)

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

> [!div class="nextstepaction"]
> [Trabalhar com blobs usando o portal do Azure](../blobs/storage-quickstart-blobs-portal.md)

---
