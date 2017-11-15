---
title: "Executar operações de armazenamento de Tabelas do Azure com o PowerShell | Microsoft Docs"
description: "Executar operações de armazenamento de Tabelas do Azure com o PowerShell"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/02/2017
ms.author: robinsh
ms.openlocfilehash: 0174b6fe02008a1c22a165b077c694af7e8618ab
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2017
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Executar operações de armazenamento de Tabelas do Azure com o Azure PowerShell 

>[!NOTE]
>A API de Tabela do Azure Cosmos DB fornece recursos premium para o armazenamento de tabelas, como distribuição global turnkey, leituras e gravações de baixa latência, indexação secundária automática e taxa de transferência dedicada. Na maioria dos casos, os comandos do PowerShell deste artigo funcionam para a API de Tabela do Azure Cosmos DB e para o armazenamento de Tabelas do Azure. No entanto, este artigo é específico ao armazenamento de Tabelas do Azure. Se estiver usando a API de Tabela do Azure Cosmos DB, consulte [Operações da API de Tabela do Azure Cosmos DB com o Azure PowerShell](table-powershell.md).
>

O armazenamento de Tabelas do Azure é um armazenamento de dados NoSQL que pode ser usado para armazenar e consultar grandes conjuntos de dados estruturados e não relacionais. Os principais componentes do serviço são tabelas, entidades e propriedades. Uma tabela é uma coleção de entidades. Uma entidade é um conjunto de propriedades. Cada entidade pode ter até 252 propriedades, que são todas pares de nome-valor. Este artigo pressupõe que você já esteja familiarizado com os conceitos do Serviço de Armazenamento de Tabelas do Azure. Para obter informações detalhadas, confira [Understanding the Table Service Data Model](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) (Noções básicas do modelo de dados do serviço Tabela) e [Introdução ao Armazenamento de Tabelas do Azure usando o .NET](table-storage-how-to-use-dotnet.md).

Este artigo de instruções abrange operações comuns do armazenamento de Tabelas do Azure. Você aprenderá como: 

> [!div class="checklist"]
> * Criar uma tabela
> * Recuperar uma tabela
> * Adicionar entidades de tabela
> * Consultar uma tabela
> * Excluir entidades de tabela
> * Excluir uma tabela

Este artigo de instruções mostra como criar uma nova conta de armazenamento em um novo grupo de recursos, de modo que você possa removê-la facilmente quando terminar. Se preferir usar uma conta de armazenamento existente, faça isso.

Estes exemplos exigem a versão 4.4.0 ou posterior do módulo do Azure PowerShell. Em uma janela do PowerShell, execute `Get-Module -ListAvailable AzureRM` para localizar a versão. Se nada for exibido ou você precisar fazer atualização, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). 

Depois que o Azure PowerShell for instalado ou atualizado, você deverá instalar o módulo **AzureRmStorageTable**, que contém os comandos para gerenciar as entidades. Para instalar esse módulo, execute o PowerShell como administrador e use o comando **Install-Module**.

```powershell
Install-Module AzureRmStorageTable
```

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça logon na sua assinatura do Azure com o comando `Login-AzureRmAccount` e siga as instruções na tela.

```powershell
Login-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Recuperar a lista de locais

Se você não sabe qual localização deseja usar, você pode listar as localizações disponíveis. Depois que a lista for exibida, encontre o que deseja usar. Esses exemplos usam **eastus**. Armazene esse valor na variável **location** para uso futuro.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup). 

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Armazene o nome do grupo de recursos em uma variável para uso futuro. Neste exemplo, um grupo de recursos chamado *pshtablesrg* é criado na região *eastus*.

```powershell
$resourceGroup = "pshtablesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Criar Conta de Armazenamento

Crie uma conta de armazenamento padrão de uso geral com o armazenamento localmente redundante (LRS) usando [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Obtenha o contexto da conta de armazenamento que define a conta de armazenamento a ser usada. Ao agir em uma conta de armazenamento, você pode referenciar o contexto em vez de fornecer repetidamente as credenciais.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Criar uma nova tabela

Para criar uma tabela, use o cmdlet [New-AzureStorageTable](/powershell/module/azure.storage/New-AzureStorageTable). Neste exemplo, a tabela é chamada `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzureStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Recuperar uma lista de tabelas na conta de armazenamento

Recupere uma lista de tabelas na conta de armazenamento usando [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzureStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Recuperar uma referência a uma tabela específica

Para executar operações em uma tabela, você precisa de uma referência à tabela específica. Obtenha uma referência usando [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable). 

```powershell
$storageTable = Get-AzureStorageTable –Name $tableName –Context $ctx
```

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Excluir uma tabela

Para excluir uma tabela, use [Remove-AzureStorageTable](/powershell/module/azure.storage/Remove-AzureStorageTable). Esse cmdlet remove a tabela, incluindo todos os seus dados.

```powershell
Remove-AzureStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzureStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você criou um novo grupo de recursos e uma conta de armazenamento no início destas instruções, remova todos os ativos criados neste exercício removendo o grupo de recursos. Esse comando exclui todos os recursos contidos no grupo, bem como o próprio grupo de recursos.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo de instruções, você aprendeu sobre operações comuns do armazenamento de Tabelas do Azure com o PowerShell, incluindo como: 

> [!div class="checklist"]
> * Criar uma tabela
> * Recuperar uma tabela
> * Adicionar entidades de tabela
> * Consultar uma tabela
> * Excluir entidades de tabela
> * Excluir uma tabela

Para obter mais informações, consulte os seguintes artigos

* [Cmdlets do PowerShell do Armazenamento](/powershell/module/azurerm.storage#storage)

* [Trabalhando com Tabelas do Armazenamento do Azure no PowerShell](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/)

* [O Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.