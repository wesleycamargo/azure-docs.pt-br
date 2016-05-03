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
   ms.date="04/20/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Criar um SQL Data Warehouse usando o Powershell

> [AZURE.SELECTOR]
- [Portal do Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

### Pré-requisitos
Antes de começar, verifique se os seguintes pré-requisitos foram cumpridos:

- Um servidor do SQL Azure V12 para hospedar o banco de dados
- Saber o nome do grupo de recursos do SQL Server

Para obter mais detalhes sobre os pré-requisitos acima, confira **Configurar e criar um servidor** no artigo [Como criar um SQL Data Warehouse do Portal do Azure][].

> [AZURE.NOTE]  Para usar o Azure Powershell com o SQL Data Warehouse, você precisará instalar a versão 1.0.3 ou superior do Azure PowerShell. Você pode verificar a versão executando **Get-Module -ListAvailable -Name Azure**. A versão mais recente pode ser instalada do [Microsoft Web Platform Installer][]. Para saber mais sobre como instalar a versão mais recente, confira [Como instalar e configurar o Azure PowerShell][].

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

4.  Criar banco de dados. Este exemplo cria um novo banco de dados chamado "mynewsqldw", com um nível de objetivo de serviço "DW400", no servidor denominado "sqldwserver1", que está no grupo de recursos denominado "mywesteuroperesgp1". **OBSERVAÇÃO: a criação de um novo banco de dados do SQL Data Warehouse pode resultar em novas cobranças. Confira [Preços do SQL Data Warehouse][] para obter mais detalhes sobre preços.**

	```Powershell
	New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
	```

Os parâmetros necessários para esse cmdlet são:

- **RequestedServiceObjectiveName**: a quantidade de DWU solicitada, no formato “DWXXX”. DWU representa uma alocação de CPU e memória. Cada valor DWU representa um aumento linear nesses recursos. Os valores para os quais há suporte são: 100, 200, 300, 400, 500, 600, 1000, 1200, 1500, 2000.
- **DatabaseName**: o nome do SQL Data Warehouse que você está criando.
- **ServerName**: o nome do servidor que você está usando para a criação (deve ser V12).
- **ResourceGroupName**: o grupo de recursos que você está usando. Para encontrar os grupos de recursos na sua assinatura, use Get-AzureResource.
- **Edition**: você deve definir a edição como "DataWarehouse" para criar um SQL Data Warehouse.

Para obter mai detalhes sobre as opções do parâmetro, confira [Criar Banco de Dados (Azure SQL Data Warehouse)][]. Para obter a referência aos comandos, confira [New-AzureSqlDatabase][].

## Próximas etapas
Após o provisionamento do SQL Data Warehouse, convém tentar [carregar dados de amostra][] ou conferir como [desenvolver][], [carregar][] ou [migrar][].

Se você estiver interessado em saber mais sobre como gerenciar o SQL Data Warehouse de forma programática, confira nosso artigo sobre como usar os [Cmdlets do Powershell e APIs REST][].

<!--Image references-->

<!--Article references-->
[migrar]: sql-data-warehouse-overview-migrate.md
[desenvolver]: sql-data-warehouse-overview-develop.md
[carregar]: sql-data-warehouse-load-with-bcp.md
[carregar dados de amostra]: sql-data-warehouse-get-started-manually-load-samples.md
[Cmdlets do Powershell e APIs REST]: sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: sql-database-configure-firewall-settings.md
[Como instalar e configurar o Azure PowerShell]: powershell-install-configure.md
[Como criar um SQL Data Warehouse do Portal do Azure]: sql-data-warehouse-get-started-provision.md

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Criar Banco de Dados (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Preços do SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
 

<!---HONumber=AcomDC_0427_2016-->