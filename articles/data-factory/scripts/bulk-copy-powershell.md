---
title: 'Script do PowerShell: copiar os dados em massa usando o Azure Data Factory | Microsoft Docs'
description: Esse script do PowerShell mostra como usar o Azure Data Factory para copiar dados em massa de um armazenamento de dados de origem para um armazenamento de dados de destino.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: 49102d573dd93b1481d1ef97ef3e603fc2f3ad46
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Script do PowerShell – copiar várias tabelas em massa usando o Azure Data Factory

Esse script do PowerShell de exemplo copia dados de diversas tabelas em um banco de dados SQL do Azure para um SQL Data Warehouse do Azure.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

Consulte [tutorial: cópia em massa](../tutorial-bulk-copy.md#prerequisites) para conhecer os pré-requisitos para executar esse exemplo.

## <a name="sample-script"></a>Script de exemplo

> [!IMPORTANT]
> Esse script cria arquivos JSON que definem as entidades do Data Factory (serviço vinculado, conjunto de dados e pipeline) no disco rígido na pasta c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure SQL Data Warehouse")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Depois de executar o exemplo de script, use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
Para remover o data factory do grupo de recursos, execute o seguinte comando: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: 

| Comando | Observações |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | Criar uma fábrica de dados. |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | Cria um serviço vinculado no data factory. Um serviço vinculado vincula um armazenamento de dados ou uma computação a um data factory. |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | Cria um conjunto de dados no data factory. Um conjunto de dados representa a entrada/saída para uma atividade em um pipeline. | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | Cria um pipeline no data factory. Um pipeline contém uma ou mais atividades que executam uma determinada operação. Nesse pipeline, a atividade de cópia copia dados de um local para outro no Armazenamento de Blobs do Azure. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | Cria uma execução do pipeline. Em outras palavras, executa o pipeline. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Obtém os detalhes sobre a execução da atividade (execução da atividade) no pipeline. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de scripts do Azure Data Factory PowerShell podem ser encontrados nos [Scripts do Azure Data Factory PowerShell](../samples-powershell.md).