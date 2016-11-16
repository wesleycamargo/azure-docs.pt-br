---
title: Criar SQL Data Warehouse usando o PowerShell | Microsoft Docs
description: Criar SQL Data Warehouse usando o PowerShell
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3d13d4a0dd1d6e0b7361a57e167b06f0b717bfb4


---
# <a name="create-sql-data-warehouse-using-powershell"></a>Criar um SQL Data Warehouse usando o PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

Este artigo mostra como criar um SQL Data Warehouse usando o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos
Para começar, você precisa do seguinte:

* **Conta do Azure**: visite [Avaliação gratuita do Azure][Avaliação gratuita do Azure] ou [Créditos do Azure no MSDN][Créditos do Azure no MSDN] para criar uma conta.
* **Azure SQL Server**: consulte [criar um servidor lógico do banco de dados SQL com o Azure Portal][criar um servidor lógico do banco de dados SQL com o Azure Portal] ou [criar um servidor lógico do banco de dados SQL com o PowerShell][criar um servidor lógico do banco de dados SQL com o PowerShell] para obter mais detalhes.
* **Grupo de recursos**: use o mesmo grupo de recursos do Azure SQL Server ou veja [como criar um grupo de recursos][como criar um grupo de recursos].
* **PowerShell versão 1.0.3 ou superior**: você pode verificar a versão executando **Get-Module -ListAvailable -Name Azure**.  A versão mais recente pode ser instalada pelo [Microsoft Web Platform Installer][Microsoft Web Platform Installer].  Para obter mais informações sobre como instalar a versão mais recente, consulte [Como instalar e configurar o Azure PowerShell][Como instalar e configurar o Azure PowerShell].

> [!NOTE]
> A criação de um SQL Data Warehouse pode resultar em um novo serviço faturável.  Confira [Preços do SQL Data Warehouse][Preços do SQL Data Warehouse] para obter mais detalhes sobre preços.
> 
> 

## <a name="create-a-sql-data-warehouse"></a>Criar um SQL Data Warehouse
1. Abra o Windows PowerShell.
2. Execute este cmdlet para fazer logon no Gerenciador de Recursos do Azure.
   
    ```Powershell
    Login-AzureRmAccount
    ```
3. Selecione a assinatura que você deseja usar para a sessão atual.
   
    ```Powershell
    Get-AzureRmSubscription    -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```
4. Criar banco de dados. Este exemplo cria um banco de dados denominado "mynewsqldw", com um nível de objetivo de serviço "DW400", no servidor denominado "sqldwserver1", que está no grupo de recursos denominado "mywesteuroperesgp1".
   
   ```Powershell
   New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
   ```

Os parâmetros requeridos são:

* **RequestedServiceObjectiveName**: a quantidade de [DWU][DWU] solicitada.  Os valores com suporte são: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 e DW6000.
* **DatabaseName**: o nome do SQL Data Warehouse que você está criando.
* **ServerName**: o nome do servidor que você está usando para a criação (deve ser V12).
* **ResourceGroupName**: o grupo de recursos que você está usando.  Para encontrar os grupos de recursos na sua assinatura, use Get-AzureResource.
* **Edition**: deve ser "DataWarehouse" para criar um SQL Data Warehouse.

Os parâmetros opcionais são:

* **CollationName**: o agrupamento padrão, se não especificado, é SQL_Latin1_General_CP1_CI_AS.  O agrupamento não pode ser alterado em um banco de dados.
* **MaxSizeBytes**: o tamanho máximo padrão de um banco de dados é de 10 GB.

Para obter mais detalhes sobre as opções do parâmetro, veja [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] e [Create Database (Azure SQL Data Warehouse)][Create Database (Azure SQL Data Warehouse)].

## <a name="next-steps"></a>Próximas etapas
Após o provisionamento do SQL Data Warehouse, você poderá tentar [carregar dados de exemplo][carregar dados de exemplo] ou conferir como [desenvolvê-los][desenvolvê-los], [carregá-los][carregá-los] ou [migrá-los][migrá-los].

Se você estiver interessado em saber mais sobre como gerenciar o SQL Data Warehouse de forma programática, confira nosso artigo sobre como usar os [Cmdlets do PowerShell e APIs REST][Cmdlets do PowerShell e APIs REST].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[migrar]: ./sql-data-warehouse-overview-migrate.md
[desenvolvê-los]: ./sql-data-warehouse-overview-develop.md
[carregar]: ./sql-data-warehouse-load-with-bcp.md
[carregando dados de exemplo]: ./sql-data-warehouse-load-sample-databases.md
[Cmdlets do PowerShell e APIs REST]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[regras de firewall]: ../sql-database-configure-firewall-settings.md

[Como instalar e configurar o PowerShell do Azure]: ../powershell-install-configure.md
[como criar um SQL Data Warehouse no Portal do Azure]: ./sql-data-warehouse-get-started-provision.md
[Criar um servidor lógico do Banco de Dados SQL do Azure com o Portal do Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Criar um servidor lógico do Banco de Dados SQL do Azure com o PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[como criar um grupo de recursos]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Criar Banco de Dados (SQL Data Warehouse do Azure)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Preços do SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Avaliação Gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Créditos do Azure no MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Nov16_HO2-->


