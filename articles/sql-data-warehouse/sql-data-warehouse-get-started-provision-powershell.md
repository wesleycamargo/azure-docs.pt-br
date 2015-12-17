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
   ms.date="10/20/2015"
   ms.author="lodipalm"/>

# Criar um SQL Data Warehouse usando o Powershell

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

> [AZURE.NOTE]Para usar o Microsoft Azure PowerShell com o SQL Data Warehouse, você precisará da versão 0.9.4 ou superior. Você pode verificar a versão executando (Get-Module Azure).Version no Powershell.

## Obter e executar os cmdlets do PowerShell do Azure
Se você não ainda tiver configurado o Powershell, você pode:

1. Para baixar o módulo PowerShell do Azure, execute o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409).
2. Para executar o módulo, na janela de início, digite **Microsoft Azure PowerShell**.
3. Se ainda não tiver adicionado sua conta ao computador, execute o cmdlet a seguir. (Para obter mais informações, consulte [Como instalar e configurar o PowerShell do Azure][]):

            Add-AzureAccount

4. Você também precisará executar o PowerShell no modo ARM. Você pode alternar para esse modo executando o seguinte comando:

            switch-azuremode AzureResourceManager

## Criando o SQL Data Warehouse
Depois de garantir que o Powershell foi configurado com êxito em sua conta, você pode executar o seguinte para implantar um novo SQL Data Warehouse:

        New-AzureSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"

Os parâmetros necessários para esse cmdlet são os seguintes:

 + **RequestedServiceObjectiveName**: a quantidade de DWU solicitada, no formato “DWXXX”, os valores com suporte no momento são: 100, 200, 300, 400, 500, 600, 1000, 1200, 1500 e 2000.
 + **DatabaseName**: o nome do SQL Data Warehouse que você está criando.
 + **ServerName**: o nome do servidor que você está usando para a criação (deve ser V12).
 + **ResourceGroupName**: o grupo de recursos que você está usando. Para encontrar os grupos de recursos na sua assinatura, use Get-AzureResource.
 + **Edition**: você deve definir a edição como “DataWarehouse” para criar um SQL Data Warehouse. 

## Próximas etapas
Após o provisionamento do SQL Data Warehouse, você pode [carregar dados de amostra][] ou conferir como [desenvolver][], [carregar][] ou [migrar][].

Se estiver interessado em obter mais informações sobre como gerenciar o SQL Data Warehouse de forma programática, confira nossa documentação do [Powershell][] ou da [API REST][].



<!--Image references-->

<!--Article references-->
[migrar]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-overview-migrate/
[desenvolver]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-overview-develop/
[carregar]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-overview-load/
[carregar dados de amostra]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[Powershell]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-reference-powershell-cmdlets/
[API REST]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[firewall rules]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-database-configure-firewall-settings/
[Como instalar e configurar o PowerShell do Azure]: powershell-install-configure.md

<!----HONumber=AcomDC_1210_2015-->