---
title: Criar uma conta de armazenamento da versão prévia do Armazenamento do Azure Data Lake Gen2 | Microsoft Docs
description: Aprenda rapidamente criar uma nova conta de armazenamento com acesso à versão prévia do Armazenamento do Data Lake Gen2 usando o portal do Azure, o Azure PowerShell ou a CLI do Azure
services: storage
author: jamesbak
ms.component: data-lake-storage-gen2
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 06/27/2018
ms.author: jamesbak
ms.openlocfilehash: 41f25496d089a5c6487176afee01f7cb06330794
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281550"
---
# <a name="quickstart-create-a-storage-account-for-analytics"></a>Início Rápido: Criar uma conta de armazenamento para análise

As contas de armazenamento com a versão prévia do Azure Data Lake Gen2 habilitada [dão suporte a um serviço de namespace hierárquico](introduction.md) que fornece um sistema de arquivos baseado em diretórios nativo sob medida para funcionar com o Sistema de Arquivos Distribuído Hadoop (HDFS). O acesso aos dados do Armazenamento do Data Lake Gen2 pelo HDFS está disponível por meio do [driver ABFS](abfs-driver.md).

Para habilitar os recursos do Armazenamento do Data Lake Gen2 em sua conta de armazenamento, [preencha a pesquisa de versão prévia para solicitar acesso](https://aka.ms/adlsgen2signup). Depois de aprovado, você poderá criar uma nova conta de armazenamento com o Data Lake Gen2 habilitado. Este guia de início rápido demonstra como criar uma conta com o [portal do Azure](https://portal.azure.com/), o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) ou a [CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

> [!NOTE]
> A interface do usuário de criação de conta no portal do Azure é atualizada depois da aprovação para criar uma conta de Armazenamento do Data Lake Gen2. Da mesma forma, os argumentos do PowerShell e da CLI relativos ao Armazenamento do Data Lake Gen2 só funcionam quando aprovados para versão prévia.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

|           | Pré-requisito |
|-----------|--------------|
|Portal     | Nenhum         |
|PowerShell | Este início rápido requer o módulo Azure PowerShell versão **5.0.4-preview** ou posterior. Execute `Get-Module -ListAvailable AzureRM` para localizar a versão atual. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). |
|CLI        | Você pode fazer logon no Azure e executar comandos da CLI do Azure de uma das duas formas a seguir: <ul><li>Você pode executar comandos da CLI de dentro do portal do Azure, no Azure Cloud Shell </li><li>Você pode instalar a CLI e executar comandos da CLI localmente</li></ul>|

Ao trabalhar na linha de comando, você tem a opção de executar o Azure Cloud Shell ou instalar a CLI localmente.

### <a name="use-azure-cloud-shell"></a>Usar o Azure Cloud Shell

O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Clique no botão **Cloud Shell** no menu no canto superior direito do portal do Azure:

[![Cloud Shell](./media/quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

O botão inicia um shell interativo que você pode usar para executar as etapas neste guia de início rápido:

[![Captura de tela mostrando a janela do Cloud Shell no portal](./media/quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

Você também pode instalar e usar a CLI do Azure localmente. Este início rápido exige que você esteja executando a versão 2.0.38 ou posterior da CLI do Azure. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>Criar uma conta de armazenamento com o Azure Data Lake Storage Gen2 habilitado

Antes de criar uma conta, primeiro crie um grupo de recursos que atue como um contêiner lógico para contas de armazenamento ou outros recursos do Azure que você criar. Caso queira limpar os recursos criados por este guia de início rápido, você pode simplesmente excluir o grupo de recursos. A exclusão do grupo de recursos também exclui a conta de armazenamento associada e todos os outros recursos associados ao grupo de recursos. Para saber mais sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> Você deve criar novas contas de armazenamento com o tipo **StorageV2 (uso geral V2)** para tirar proveito dos recursos de Armazenamento do Data Lake Gen2.  

Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de Armazenamento do Azure](../common/storage-account-overview.md).

Ao nomear sua conta de armazenamento, lembre-se dessas regras:

- Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas.
- O nome da sua conta de armazenamento deve ser exclusivo no Azure. Duas contas de armazenamento não podem ter o mesmo nome.

## <a name="create-an-account-using-the-azure-portal"></a>Criar uma conta usando o portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um grupo de recursos no portal do Azure, siga estas etapas:

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha **Grupo de Recursos**.
2. Clique no botão **Adicionar** para adicionar um novo grupo de recursos.
3. Insira um nome para o novo grupo de recursos.
4. Selecione a assinatura na qual criar o novo grupo de recursos.
5. Escolha o local para o grupo de recursos.
6. Selecione o botão **Criar**.  

![Captura de tela mostrando a criação do grupo de recursos no portal do Azure](./media/quickstart-create-account/create-resource-group.png)

### <a name="create-a-general-purpose-v2-storage-account"></a>Criar uma conta de armazenamento de uso geral v2

Para criar uma conta de armazenamento de uso geral v2 no portal do Azure, siga estas etapas:

> [!NOTE]
> O namespace hierárquico está disponível atualmente em todas as regiões públicas, mas em nenhuma das regiões privadas. No momento, ele não está disponível em nuvens soberanas.

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha **Todos os serviços**. Em seguida, role para baixo até **Armazenamento** e escolha **Contas de armazenamento**. Na janela **Contas de Armazenamento** que aparece, escolha **Adicionar**.
2. Insira um nome para a conta de armazenamento.
3. Deixe **Modelo de implantação** definido com o valor padrão.
4. Definir o campo **Tipo de conta** para **StorageV2 (uso geral v2)**.
5. Defina **Local** como **Oeste dos EUA 2**
6. Deixe o campo **Replicação** definido como **Armazenamento com redundância local (LRS)**.
7. Deixe esses campos definidos com seus padrões: **Replicação**. **Desempenho**, **Camada de acesso**.
8. Escolha a assinatura na qual você deseja criar a conta de armazenamento.
9. Na seção **Grupo de recursos**, selecione **Usar existente**, depois escolha o grupo de recursos criado na seção anterior.
10. Mantenha o valor padrão em **Redes Virtuais**
11. Na seção **Armazenamento do Data Lake Gen2 (versão prévia)**, defina **namespace hierárquico** como **Habilitado**.
12. Clique em **Criar** para criar a conta de armazenamento.

![Captura de tela mostrando a criação da conta de armazenamento no portal do Azure](./media/quickstart-create-account/azure-data-lake-storage-account-create.png)

Sua conta de armazenamento é criada por meio do portal.

### <a name="clean-up-resources"></a>Limpar recursos

Para remover um grupo de recursos usando o portal do Azure:

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha **Grupo de Recursos** para exibir a lista dos seus grupos de recursos.
2. Localize o grupo de recursos a ser excluído e clique com o botão direito do mouse no botão **Mais** (**...**) do lado direito da lista.
3. Selecione **Excluir grupo de recursos** e confirme.

## <a name="create-an-account-using-powershell"></a>Criar uma conta usando o PowerShell

Faça logon na sua assinatura do Azure com o comando `Login-AzureRmAccount` e siga as instruções na tela para fazer a autenticação.

```powershell
Login-AzureRmAccount
```

### <a name="upgrade-your-powershell-module"></a>Atualizar seu módulo do powershell

Para interagir com o Data Lake Storage Gen2 por meio do PowerShell, você terá que atualizar seu módulo para a versão prévia.

Para fazer isso, abra o um PowerShell elevado e insira o comando a seguir: `Install-Module AzureRM.Storage –Repository PSGallery -RequiredVersion 5.0.4-preview –AllowPrerelease –AllowClobber –Force `

Depois, reinicie o shell.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um novo grupo de recursos com o PowerShell, use o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup): 

> [!NOTE]
> > O namespace hierárquico está disponível atualmente em todas as regiões públicas. No momento, ele não está disponível em nuvens soberanas.

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
$location = "westus2"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-general-purpose-v2-storage-account"></a>Criar uma conta de armazenamento de uso geral v2

Para criar uma conta de armazenamento de uso geral v2 a partir do PowerShell com armazenamento com redundância local (LRS), use o comando [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount):

```powershell
Get-AzureRmLocation | select Location 
$location = "westus2"

New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 
  -EnableHierarchicalNamespace $True
```

### <a name="clean-up-resources"></a>Limpar recursos

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup): 

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>Criar uma conta usando a CLI do Azure

Para iniciar o Azure Cloud Shell, faça logon no [portal do Azure](https://portal.azure.com).

Para fazer logon em sua instalação local da CLI, execute o comando de logon:

```cli
az login
```

### <a name="upgrade-your-cli-module"></a>Atualizar seu módulo da CLI

Para interagir com o Data Lake Storage Gen2 por meio da CLI, você precisará adicionar a extensão ao seu shell.

Para fazer isso: insira o seguinte comando usando o Cloud Shell ou um shell local: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um novo grupo de recursos com a CLI do Azure, use o comando [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create \
    --name storage-quickstart-resource-group \
    --location westus2
```

> [!NOTE]
> > O namespace hierárquico está disponível atualmente em todas as regiões públicas, mas em nenhuma das regiões privadas. No momento, ele não está disponível em nuvens soberanas.

### <a name="create-a-general-purpose-v2-storage-account"></a>Criar uma conta de armazenamento de uso geral v2

Para criar uma conta de armazenamento de uso geral v2 a partir da CLI do Azure com armazenamento com redundância local, use o comando [az storage account create](/cli/azure/storage/account#az_storage_account_create).

```azurecli-interactive
az storage account create \
    --name storagequickstart \
    --resource-group storage-quickstart-resource-group \
    --location westus2 \
    --sku Standard_LRS \
    --kind StorageV2 \
    --hierarchical-namespace true
```

### <a name="clean-up-resources"></a>Limpar recursos

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou uma conta de armazenamento com recursos do Data Lake Storage Gen2. Para saber como carregar e baixar blobs para e da sua conta de armazenamento, prossiga para o guia de início rápido do Armazenamento de blobs.

* [Mover dados de e para o Armazenamento de Blobs do Azure usando o AzCopy](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azcopy)