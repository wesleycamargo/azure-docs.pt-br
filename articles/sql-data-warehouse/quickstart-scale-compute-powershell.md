---
title: "Início Rápido: escalar horizontalmente a computação no SQL Data Warehouse do Azure – PowerShell | Microsoft Docs"
description: "Tarefas do PowerShell para escalar horizontalmente os recursos de computação por meio do ajuste das unidades de data warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/31/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: a3a435d6bdb0d35c96349540d5e9f9b5be61bd9b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-powershell"></a>Início Rápido: dimensionar a computação no SQL Data Warehouse do Azure no PowerShell

Dimensionar a computação no SQL Data Warehouse do Azure no PowerShell. [Escale horizontalmente a computação](sql-data-warehouse-manage-compute-overview.md) para melhorar o desempenho ou reduza a escala da computação para economizar custos. 

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Este tutorial requer o módulo do Azure PowerShell, versão 5.1.1 ou posterior. Execute `Get-Module -ListAvailable AzureRM` descobrir a versão que você tem atualmente. Se você precisa instalar ou atualizar, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps.md). 

## <a name="before-you-begin"></a>Antes de começar

Este início rápido pressupõe que você já tenha um data warehouse do SQL que possa ser dimensionado. Se precisar, use [Criar e conectar – portal](create-data-warehouse-portal.md) para criar um data warehouse chamado **mySampleDataWarehouse**. 

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon em sua assinatura do Azure usando o comando [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) e siga as instruções na tela.

```powershell
Add-AzureRmAccount
```

Para ver qual assinatura que você está usando, execute [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Se você precisar usar uma assinatura diferente do padrão, execute [AzureRmSubscription selecione](/powershell/module/azurerm.profile/select-azurermsubscription).

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Pesquisar informações de Data Warehouse

Localize o nome do banco de dados, o nome do servidor e o grupo de recursos para o Data Warehouse que você planeja pausar e continuar. 

Siga estas etapas para localizar informações de local de seu Data Warehouse.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Clique em **Bancos de Dados SQL** na página esquerda do portal do Azure.
3. Selecione **mySampleDataWarehouse** da página **Bancos de Dados SQL**. Isso abre o Data Warehouse. 

    ![Grupo de recursos e o nome de servidor](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Anote o nome do Data Warehouse que será usado como o nome do banco de dados. Anote também o nome do servidor e o grupo de recursos. Você os usará nos comandos pausar e continuar.
5. Se o servidor for foo.database.windows.net, use somente a primeira parte como o nome do servidor nos cmdlets do PowerShell. Na imagem anterior, o nome completo do servidor é newserver-20171113.database.windows.net. Nós usamos **newserver-20171113** como o nome do servidor no cmdlet do PowerShell.

## <a name="scale-compute"></a>Computação de escala

No SQL Data Warehouse, você pode aumentar ou diminuir os recursos de computação ao ajustar as unidades de data warehouse. O [Criar e conectar – portal](create-data-warehouse-portal.md) criou o **mySampleDataWarehouse** e o inicializou com 400 DWUs. As seguintes etapas ajustam as DWUs do **mySampleDataWarehouse**.

Para alterar as unidades de data warehouse, use o cmdlet [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) do PowerShell. O exemplo a seguir define as unidades de data warehouse do banco de dados **mySampleDataWarehouse** como DW300, o qual é hospedado no Grupo de recursos **myResourceGroup** no servidor **mynewserver-20171113**.

```Powershell
Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
```

## <a name="check-database-state"></a>Verificar estado do banco de dados

Para ver o estado atual do data warehouse, use o cmdlet [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) do PowerShell. Ele obtém o estado do banco de dados **mySampleDataWarehouse** no ResourceGroup **myResourceGroup** e no servidor **mynewserver-20171113.database.windows.net**.

```powershell
Get-AzureRmSqlDatabase -ResourceGroupName myResourceGroup -ServerName mynewserver-20171113 -DatabaseName mySampleDataWarehouse
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

Então, você pode clicar para ver o **Status** do banco de dados. Nesse caso, é possível ver que esse banco de dados está online.  Ao executar esse comando, você deverá receber um valor de Status Online, Pausando, Retomando, Dimensionando ou Em pausa.

## <a name="next-steps"></a>Próximas etapas
Agora, você aprendeu como dimensionar a computação para seu data warehouse. Para saber mais sobre o SQL Data Warehouse do Azure, prossiga para o tutorial de carregamento de dados.

> [!div class="nextstepaction"]
>[Carregar dados no SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
