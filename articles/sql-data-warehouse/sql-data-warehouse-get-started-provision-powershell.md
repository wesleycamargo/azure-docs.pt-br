<properties
   pageTitle="Criar SQL Data Warehouse usando o Powershell | Microsoft Azure"
   description="Criar SQL Data Warehouse usando o Powershell"
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
   ms.date="06/07/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Criar um SQL Data Warehouse usando o Powershell

> [AZURE.SELECTOR]
- [Portal do Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## Pré-requisitos
Antes de começar, verifique se os seguintes pré-requisitos foram cumpridos:

- **Conta do Azure**: consulte [Avaliação Gratuita do Azure][] ou [Créditos do Azure no MSDN][] para criar uma conta.
- **Azure SQL Server V12**: consulte [Criar um servidor lógico do Banco de Dados SQL do Azure com o Portal do Azure][] ou [Criar um servidor lógico do Banco de Dados SQL do Azure com o PowerShell][].
- **Nome do grupo de recursos**: use o mesmo Grupo de Recursos que o Azure SQL Server V12, ou confira [grupos de recursos][] para criar um novo grupo de recursos.
- **PowerShell versão 1.0.3 ou superior**: você pode verificar a versão executando **Get-Module -ListAvailable -Name Azure**. A versão mais recente pode ser instalada a partir do [Microsoft Web Platform Installer][]. Para obter mais informações sobre como instalar a versão mais recente, consulte [Como instalar e configurar o Azure PowerShell][].

> [AZURE.NOTE] A criação de um novo SQL Data Warehouse pode resultar em um novo serviço faturável. Confira [Preços do SQL Data Warehouse][] para obter mais detalhes sobre preços.

## Como criar um banco de dados do SQL Data Warehouse.
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

- **RequestedServiceObjectiveName**: a quantidade de DWU solicitada, no formato “DWXXX”. DWU representa uma alocação de CPU e memória. Cada valor DWU representa um aumento linear nesses recursos. Os valores para os quais há suporte são: 100, 200, 300, 400, 500, 600, 1000, 1200, 1500, 2000.
- **DatabaseName**: o nome do SQL Data Warehouse que você está criando.
- **ServerName**: o nome do servidor que você está usando para a criação (deve ser V12).
- **ResourceGroupName**: o grupo de recursos que você está usando. Para encontrar os grupos de recursos na sua assinatura, use Get-AzureResource.
- **Edition**: você deve definir a edição como "DataWarehouse" para criar um SQL Data Warehouse.

Para obter mais detalhes sobre as opções do parâmetro, consulte [Criar Banco de Dados (Azure SQL Data Warehouse)][]. Para obter a referência dos comandos, consulte [New-AzureRmSqlDatabase][]

## Próximas etapas
Após o provisionamento do SQL Data Warehouse ter terminado, você pode tentar [carregar os dados de amostra][] ou verificar como [desenvolver][], [carregar][] ou [migrar][].

Se você estiver interessado em saber mais sobre como gerenciar o SQL Data Warehouse de forma programática, confira nosso artigo sobre como usar os [Cmdlets do Powershell e APIs REST][].

<!--Image references-->

<!--Article references-->

[migrar]: sql-data-warehouse-overview-migrate.md
[desenvolver]: sql-data-warehouse-overview-develop.md
[carregar]: sql-data-warehouse-load-with-bcp.md
[carregar os dados de amostra]: sql-data-warehouse-get-started-load-sample-databases.md
[Cmdlets do Powershell e APIs REST]: sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[Como instalar e configurar o Azure PowerShell]: ../powershell/powershell-install-configure.md
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Criar um servidor lógico do Banco de Dados SQL do Azure com o Portal do Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Criar um servidor lógico do Banco de Dados SQL do Azure com o PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[grupos de recursos]: ../azure-portal/resource-group-portal.md

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Criar Banco de Dados (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Preços do SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Avaliação Gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Créditos do Azure no MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0608_2016-->