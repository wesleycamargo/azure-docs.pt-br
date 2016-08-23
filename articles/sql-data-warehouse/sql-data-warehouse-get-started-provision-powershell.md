<properties
   pageTitle="Criar SQL Data Warehouse usando o PowerShell | Microsoft Azure"
   description="Criar SQL Data Warehouse usando o PowerShell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Criar um SQL Data Warehouse usando o PowerShell

> [AZURE.SELECTOR]
- [Portal do Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Este artigo mostra como criar um SQL Data Warehouse usando o PowerShell.

## Pré-requisitos

Para começar, você precisará do seguinte:

- **Conta do Azure**: visite [Avaliação Gratuita do Azure][] ou [Créditos do Azure no MSDN][] para criar uma conta.
- **Servidor SQL do Azure**: veja [Criar um servidor lógico do Banco de Dados SQL do Azure com o Portal do Azure][] ou [Criar um servidor lógico do Banco de Dados SQL do Azure com o PowerShell][] para obter mais detalhes.
- **Grupo de recursos**: use o mesmo grupo de recursos do servidor SQL do Azure ou veja [como criar um grupo de recursos][].
- **PowerShell versão 1.0.3 ou superior**: você pode verificar a versão executando **Get-Module -ListAvailable -Name Azure**. A versão mais recente pode ser instalada pelo [Microsoft Web Platform Installer][]. Para saber mais sobre como instalar a versão mais recente, veja [Como instalar e configurar o Azure PowerShell][].

> [AZURE.NOTE] A criação de um novo SQL Data Warehouse pode resultar em um novo serviço faturável. Confira [Preços do SQL Data Warehouse][] para obter mais detalhes sobre preços.

## Criar um SQL Data Warehouse

1. Abra o Windows PowerShell.
2. Execute este cmdlet para fazer logon no Gerenciador de Recursos do Azure.

	```Powershell
	Login-AzureRmAccount
	```
	
3. Selecione a assinatura que você deseja usar para a sessão atual.

	```Powershell
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```

4.  Criar banco de dados. Este exemplo cria um novo banco de dados chamado "mynewsqldw", com um nível de objetivo de serviço "DW400", no servidor denominado "sqldwserver1", que está no grupo de recursos denominado "mywesteuroperesgp1".

	```Powershell
	New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
	```

Os parâmetros necessários para esse cmdlet são:

- **RequestedServiceObjectiveName**: a quantidade de [DWU][] solicitada. Os valores com suporte são: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 e DW6000.
- **DatabaseName**: o nome do SQL Data Warehouse que você está criando.
- **ServerName**: o nome do servidor que você está usando para a criação (deve ser V12).
- **ResourceGroupName**: o grupo de recursos que você está usando. Para encontrar os grupos de recursos na sua assinatura, use Get-AzureResource.
- **Edition**: você deve definir a edição como "DataWarehouse" para criar um SQL Data Warehouse.

Para obter mais detalhes sobre as opções do parâmetro, confira [Criar Banco de Dados (Azure SQL Data Warehouse)][]. Para obter a referência dos comandos, veja [New-AzureRmSqlDatabase][]

## Próximas etapas

Após o provisionamento do SQL Data Warehouse ter terminado, você pode tentar [carregar os dados de amostra][] ou verificar como [desenvolver][], [carregar][] ou [migrar][].

Se você estiver interessado em saber mais sobre como gerenciar o SQL Data Warehouse de forma programática, confira nosso artigo sobre como usar os [Cmdlets do PowerShell e APIs REST][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[migrar]: ./sql-data-warehouse-overview-migrate.md
[desenvolver]: ./sql-data-warehouse-overview-develop.md
[carregar]: ./sql-data-warehouse-load-with-bcp.md
[carregar os dados de amostra]: ./sql-data-warehouse-load-sample-databases.md
[Cmdlets do PowerShell e APIs REST]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[Como instalar e configurar o Azure PowerShell]: ../powershell/powershell-install-configure.md
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Criar um servidor lógico do Banco de Dados SQL do Azure com o Portal do Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Criar um servidor lógico do Banco de Dados SQL do Azure com o PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[como criar um grupo de recursos]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Criar Banco de Dados (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Preços do SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Avaliação Gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Créditos do Azure no MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0817_2016-->