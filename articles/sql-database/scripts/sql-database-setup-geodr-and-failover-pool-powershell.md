---
title: "Script do Azure PowerShell - Configuração de replicação geográfica de um banco de dados SQL em pool | Microsoft Docs"
description: "Exemplo de Script do Azure PowerShell - Configure a Replicação Geográfica Ativa para um banco de dados SQL do Azure em pool usando o PowerShell"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: PowerShell
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/07/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: ccb5b0545dd8b0eea47b17a06e18e4ae63024ea3
ms.lasthandoff: 03/30/2017

---

# <a name="configure-active-geo-replication-for-a-pooled-azure-sql-database-using-powershell"></a>Configurar a Replicação Geográfica Ativa para um banco de dados SQL do Azure usando o PowerShell

Esse exemplo de script do PowerShell configura a Replicação Geográfica Ativa para um banco de dados em um pool elástico e faz o failover desse banco de dados para a réplica secundária.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-scripts"></a>Exemplos de Scripts

[!code-powershell[main](../../../powershell_scripts/sql-database/setup-geodr-and-failover-pool/setup-geodr-and-failover-pool.ps1 "Configurar a Replicação Geográfica Ativa para o pool Elástico")]

## <a name="clean-up-deployment"></a>Limpar implantação

Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) | Cria um servidor lógico que hospeda um banco de dados ou pool elástico. |
| [New-AzureRmSqlElasticPool](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlelasticpool) | Cria um pool elástico dentro de um servidor lógico. |
| [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) | Cria um banco de dados em um servidor lógico como um banco de dados individual ou em pool. |
| [Set-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/set-azurermsqldatabase) | Atualiza as propriedades do banco de dados ou move um banco de dados para dentro, para fora entre pools elásticos. |
| [New-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabasesecondary)| Cria um banco de dados secundário para um banco de dados existente e inicia a replicação de dados. |
| [Get-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/get-azurermsqldatabase)| Obtém um ou mais bancos de dados. |
| [Set-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/set-azurermsqldatabasesecondary)| Alterna um banco de dados secundário para primário a fim de iniciar o failover.|
| [Get-AzureRmSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/get-azurermsqldatabasereplicationlink) | Obtém os links de replicação geográfica entre um Banco de Dados SQL do Azure e um grupo de recursos ou do SQL Server. |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Os exemplos de script do PowerShell do Banco de Dados SQL adicionais podem ser encontrados nos [scripts do PowerShell do Banco de Dados SQL do Azure](../sql-database-powershell-samples.md).

