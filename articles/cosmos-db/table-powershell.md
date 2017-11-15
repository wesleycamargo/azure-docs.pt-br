---
title: "Executar operações da API de Tabela do Azure Cosmos DB com o PowerShell | Microsoft Docs"
description: "Como executar operações da API de Tabela do Azure Cosmos DB com o PowerShell"
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
ms.openlocfilehash: 35d05b7003d731610df816c8470acc9133a4a6de
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2017
---
# <a name="perform-azure-cosmos-db-table-api-operations-with-azure-powershell"></a>Executar operações da API de Tabela do Azure Cosmos DB com o Azure PowerShell 

>[!NOTE]
>A API de Tabela do Azure Cosmos DB fornece recursos premium para o armazenamento de tabelas, como distribuição global turnkey, leituras e gravações de baixa latência, indexação secundária automática e taxa de transferência dedicada. Na maioria dos casos, os comandos do PowerShell deste artigo funcionam para a API de Tabela do Azure Cosmos DB e para o armazenamento de Tabelas do Azure. No entanto, este artigo é específico à API de Tabela do Azure Cosmos DB. Se estiver usando o armazenamento de Tabelas do Azure, consulte [Executar operações de armazenamento de Tabelas do Azure com o Azure PowerShell](table-storage-how-to-use-powershell.md).
>

A API de Tabela do Azure Cosmos DB permite armazenar e consultar grandes conjuntos de dados estruturados e não relacionais. Os principais componentes do serviço são tabelas, entidades e propriedades. Uma tabela é uma coleção de entidades. Uma entidade é um conjunto de propriedades. Cada entidade pode ter até 252 propriedades, que são todas pares de nome-valor. Este artigo pressupõe que você já esteja familiarizado com os conceitos da API de Tabela do Azure Cosmos DB. Para obter informações detalhadas, consulte [Introdução à API de Tabela do Azure Cosmos DB](table-introduction.md) e [Criar um aplicativo .NET usando a API de Tabela](create-table-dotnet.md).

Este artigo de instruções abrange operações comuns da API de Tabela. Você aprenderá como: 

> [!div class="checklist"]
> * Criar uma tabela
> * Recuperar uma tabela
> * Adicionar entidades de tabela
> * Consultar uma tabela
> * Excluir entidades de tabela

## <a name="prerequisites"></a>Pré-requisitos

Estes exemplos exigem a versão 4.4.0 ou posterior do módulo do Azure PowerShell. Em uma janela do PowerShell, execute `Get-Module -ListAvailable AzureRM` para localizar a versão. Se nada for exibido ou você precisar fazer atualização, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). 

Depois que o Azure PowerShell for instalado ou atualizado, você deverá instalar o módulo **AzureRmStorageTable**, que contém os comandos para gerenciar as entidades. Para instalar esse módulo, execute o PowerShell como administrador e use o comando **Install-Module**.

```powershell
Install-Module AzureRmStorageTable
```

Embora a API de Tabela do Azure Cosmos DB esteja em versão prévia, você também precisa instalar seus assemblies localmente para usar esses cmdlets do PowerShell. Para obter instruções sobre como fazer isso, consulte [Módulo do PowerShell de Tabelas de Armazenamento da RM do Azure para Tabelas do Azure Cosmos DB](https://blogs.technet.microsoft.com/paulomarques/2017/05/23/azure-rm-storage-tables-powershell-module-now-includes-support-for-cosmos-db-tables/).

Para testar os exercícios a seguir, você precisa de uma conta de banco de dados do Azure Cosmos DB. Caso ainda não tenha uma, crie uma nova conta do Azure Cosmos DB usando o [portal do Azure](https://portal.azure.com). Para obter ajuda sobre como criar uma nova conta de banco de dados, consulte [Azure Cosmos DB: criar uma conta de banco de dados](create-table-dotnet.md#create-a-database-account).

Obtenha o nome da conta do banco de dados e o grupo de recursos no portal; você precisa desses valores para colocá-los no script para acessar as tabelas. 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça logon na sua assinatura do Azure com o comando `Login-AzureRmAccount` e siga as instruções na tela.

```powershell
Login-AzureRmAccount
```

## <a name="create-a-table-or-reference-a-table"></a>Criar uma tabela ou referenciar uma tabela

Para criar uma tabela ou obter uma referência a uma tabela, use **Get-AzureStorageTableTable**. Se você chamar esse cmdlet com o nome de uma tabela que não existe, ele criará uma nova tabela com esse nome e retornará uma referência à nova tabela. Se a tabela existir, ele retornará uma referência à tabela existente.

```powershell
# set the name of the resource group in which your Cosmos DB Account resides.
$resourceGroup = "contosocosmosrg"
# if you want to make sure the resource group is valid, try this command
Get-AzureRmResourceGroup -Name $resourceGroup

# set the Cosmos DB account name 
$cosmosDBAccountName = "contosocosmostbl" 

# set the table name 
$tableName = "contosotable1"

# Get a reference to a table. This creates the table if it doesn't exist.
$storageTable = Get-AzureStorageTableTable `
  -resourceGroup $resourceGroup `
  -tableName $tableName `
  -cosmosDbAccount $cosmosDBAccountName 
```

Não é possível listar as tabelas na conta do Azure Cosmos DB usando o PowerShell, mas é possível entrar no portal e ver a tabela. Agora vamos dar uma olhada em como gerenciar as entidades na tabela.

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Excluir uma tabela 

O PowerShell não dá suporte à exclusão de tabelas por meio do Azure Cosmos DB. Para excluir uma tabela, acesse o [portal do Azure](https://azure.portal.com), localize a conta do Azure Cosmos DB que você está usando e, em seguida, encontre e exclua a tabela. 

## <a name="clean-up-resources"></a>Limpar recursos

Se você criou um novo grupo de recursos e uma nova conta do Azure Cosmos DB nesse grupo, remova todos os ativos criados neste exercício removendo o grupo de recursos. Esse comando exclui todos os recursos contidos no grupo, bem como o próprio grupo de recursos.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo de instruções, você aprendeu sobre operações comuns da API de Tabela com o PowerShell, incluindo como: 

> [!div class="checklist"]
> * Criar uma tabela
> * Recuperar uma tabela
> * Adicionar entidades de tabela
> * Consultar uma tabela
> * Excluir entidades de tabela

Para obter mais informações, consulte os seguintes artigos:

* [Trabalhando com Tabelas do Armazenamento do Azure no PowerShell](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/)

* [O Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.