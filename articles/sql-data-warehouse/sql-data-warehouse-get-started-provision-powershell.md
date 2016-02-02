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
   ms.date="01/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Criar um SQL Data Warehouse usando o Powershell

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## Obter e executar os cmdlets do PowerShell do Azure

> [AZURE.NOTE]  Para usar o Microsoft Azure Powershell com o SQL Data Warehouse, você deve baixar e instalar a versão mais recente do Azure PowerShell com cmdlets ARM. Você pode verificar a versão executando `Get-Module -ListAvailable -Name Azure`. Este artigo baseia-se no Microsoft Azure PowerShell versão 1.0.3.

Se você ainda não tiver configurado o PowerShell, precisará baixá-lo e configurá-lo.

1. Para baixar o módulo PowerShell do Azure, execute o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409).
2. Para executar o módulo, na janela de início, digite **Microsoft Azure PowerShell**.
3. Execute este cmdlet para fazer logon no Gerenciador de Recursos do Azure. Para saber mais, consulte [Como instalar e configurar o Azure PowerShell][]\:

	```
	Login-AzureRmAccount
	```

4. Selecione a assinatura que você deseja usar para a sessão atual.

	```
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```
   
## Como criar um banco de dados do SQL Data Warehouse.
Para implantar um SQL Data Warehouse, use o cmdlet New-AzureRmSQLDatabase. Antes de executar o comando, verifique se você cumpriu os pré-requisitos a seguir.

### Pré-requisitos

- Um servidor do SQL Azure V12 para hospedar o banco de dados
- Saber o nome do grupo de recursos do SQL Server.

### Comando de implantação

Esse comando implantará um novo banco de dados no SQL Data Warehouse.

```
New-AzureRmSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"
```

O exemplo implanta um novo banco de dados chamado "mynewsqldw1", com o nível de objetivo de serviço "DW400" no servidor denominado "sqldwserver1", que está no grupo de recursos denominado "mywesteuroperesgp1".

```
New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw1" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
```

Os parâmetros necessários para esse cmdlet são os seguintes:

 + **RequestedServiceObjectiveName**: a quantidade de DWU solicitada, no formato “DWXXX”, os valores com suporte no momento são: 100, 200, 300, 400, 500, 600, 1000, 1200, 1500 e 2000.
 + **DatabaseName**: o nome do SQL Data Warehouse que você está criando.
 + **ServerName**: o nome do servidor que você está usando para a criação (deve ser V12).
 + **ResourceGroupName**: o grupo de recursos que você está usando. Para encontrar os grupos de recursos na sua assinatura, use Get-AzureResource.
 + **Edition**: você deve definir a edição como "DataWarehouse" para criar um SQL Data Warehouse. 

Para obter a referência aos comandos, veja [New-AzureSqlDatabase](https://msdn.microsoft.com/library/mt619339.aspx).

Para obter as opções do parâmetro, confira [Criar Banco de Dados (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt204021.aspx).

## Próximas etapas
Após o provisionamento do SQL Data Warehouse, você poderá [carregar dados de exemplo][] ou conferir como [desenvolver][], carregar ou [migrar][].

Se estiver interessado em obter mais informações sobre como gerenciar o SQL Data Warehouse de forma programática, confira nossa documentação do [Powershell][] ou da [API REST][].



<!--Image references-->

<!--Article references-->
[migrar]: ./sql-data-warehouse-overview-migrate.md
[desenvolver]: ./sql-data-warehouse-overview-develop/.md
[carregar dados de exemplo]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Powershell]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[API REST]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[firewall rules]: ../sql-database/sql-database-configure-firewall-settings.md
[Como instalar e configurar o Azure PowerShell]: ./powershell-install-configure.md

<!---HONumber=AcomDC_0128_2016-->