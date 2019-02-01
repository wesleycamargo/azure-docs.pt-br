---
title: 'Início Rápido: Aumentar a computação no SQL Data Warehouse do Azure – PowerShell | Microsoft Docs'
description: Dimensionar a computação no SQL Data Warehouse do Azure no PowerShell. Escale horizontalmente a computação para melhorar o desempenho ou reduza a escala da computação para economizar custos.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6df0ff292c21ceb99bc30c7cd8cab007a27a0fcb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469440"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-powershell"></a>Início Rápido: Dimensionar a computação no SQL Data Warehouse do Azure no PowerShell

Dimensionar a computação no SQL Data Warehouse do Azure no PowerShell. [Escale horizontalmente a computação](sql-data-warehouse-manage-compute-overview.md) para melhorar o desempenho ou reduza a escala da computação para economizar custos.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Este tutorial requer o módulo do Azure PowerShell, versão 5.1.1 ou posterior. Execute `Get-Module -ListAvailable AzureRM` descobrir a versão que você tem atualmente. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="before-you-begin"></a>Antes de começar

Este início rápido pressupõe que você já tenha um data warehouse do SQL que possa ser dimensionado. Se precisar, use [Criar e conectar – portal](create-data-warehouse-portal.md) para criar um data warehouse chamado **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon em sua assinatura do Azure usando o comando [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) e siga as instruções na tela.

```powershell
Connect-AzureRmAccount
```

Para ver qual assinatura que você está usando, execute [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Se você precisar usar uma assinatura diferente do padrão, execute [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext).

```powershell
Set-AzureRmContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Pesquisar informações de Data Warehouse

Localize o nome do banco de dados, o nome do servidor e o grupo de recursos para o Data Warehouse que você planeja pausar e continuar.

Siga estas etapas para localizar informações de local de seu Data Warehouse.

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Clique em **SQL data warehouses** na página esquerda do portal do Azure.
3. Selecione **mySampleDataWarehouse** na página **SQL data warehouses**. Isso abre o Data Warehouse.

    ![Grupo de recursos e o nome de servidor](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Anote o nome do Data Warehouse que será usado como o nome do banco de dados. Lembre-se de que um data warehouse é um tipo de banco de dados. Anote também o nome do servidor e o grupo de recursos. Você os usará nos comandos pausar e continuar.
5. Se o servidor for foo.database.windows.net, use somente a primeira parte como o nome do servidor nos cmdlets do PowerShell. Na imagem anterior, o nome completo do servidor é newserver-20171113.database.windows.net. Usamos **newserver-20180430** como o nome do servidor no cmdlet do PowerShell.

## <a name="scale-compute"></a>Computação de escala

No SQL Data Warehouse, você pode aumentar ou diminuir os recursos de computação ao ajustar as unidades de data warehouse. O [Criar e conectar – portal](create-data-warehouse-portal.md) criou o **mySampleDataWarehouse** e o inicializou com 400 DWUs. As seguintes etapas ajustam as DWUs do **mySampleDataWarehouse**.

Para alterar as unidades de data warehouse, use o cmdlet [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) do PowerShell. O exemplo a seguir define as unidades de data warehouse do banco de dados **mySampleDataWarehouse** como DW300, que é hospedado no Grupo de recursos **myResourceGroup** no servidor **mynewserver-20180430**.

```Powershell
Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
```

## <a name="check-data-warehouse-state"></a>Verifique o estado do data warehouse

Para ver o estado atual do data warehouse, use o cmdlet [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) do PowerShell. Ele obtém o estado do banco de dados **mySampleDataWarehouse** no ResourceGroup **myResourceGroup** e no servidor **mynewserver-20180430.database.windows.net**.

```powershell
$database = Get-AzureRmSqlDatabase -ResourceGroupName myResourceGroup -ServerName mynewserver-20171113 -DatabaseName mySampleDataWarehouse
$database
```

O resultado será em algo parecido com isto:

```powershell
ResourceGroupName             : myResourceGroup
ServerName                    : mynewserver-20171113
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mynewserver-20171113/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

Você pode ver o **Status** do banco de dados na saída. Nesse caso, é possível ver que esse banco de dados está online.  Ao executar esse comando, você deverá receber um valor de Status Online, Pausando, Retomando, Dimensionando ou Em pausa.

Para ver o status, use o seguinte comando:

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>Próximas etapas
Agora, você aprendeu como dimensionar a computação para seu data warehouse. Para saber mais sobre o SQL Data Warehouse do Azure, prossiga para o tutorial de carregamento de dados.

> [!div class="nextstepaction"]
>[Carregar dados no SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
