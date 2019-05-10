---
title: Script do Azure PowerShell – Criar uma conta da API do Gremlin do Azure Cosmos DB
description: Exemplo de script do Azure PowerShell – Criar uma conta de API do Gremlin do Azure Cosmos DB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.subservice: cosmosdb-graph
ms.devlang: PowerShell
ms.topic: sample
ms.date: 05/29/2018
ms.reviewer: sngun
ms.openlocfilehash: 3aee8c6b4983d2c82b8a9eef8f9f0c974e162854
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079511"
---
# <a name="azure-cosmos-db-create-a-gremlin-api-account-using-powershell"></a>O Azure Cosmos DB: Criar uma conta da API do Gremlin usando o PowerShell

Este exemplo de script do PowerShell cria uma conta de API do Gremlin do Azure Cosmos DB. 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../../powershell_scripts/cosmosdb/create-and-configure-graph-database/create-and-configure-graph-database.ps1 "Create an Azure Cosmos DB account")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Após a execução do script de exemplo, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Cria um servidor lógico que hospeda um banco de dados ou pool elástico. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de scripts do PowerShell do Banco de Dados Cosmos do Azure podem ser encontrados nos [Scripts do PowerShell do Banco de Dados Cosmos do Azure](../../powershell-samples.md).
