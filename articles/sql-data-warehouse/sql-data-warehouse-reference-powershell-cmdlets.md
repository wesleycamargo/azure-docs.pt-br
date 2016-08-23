<properties
   pageTitle="Cmdlets do PowerShell para SQL Data Warehouse do Azure"
   description="Encontre os principais cmdlets do PowerShell para SQL Data Warehouse do Azure, inclusive sobre como pausar e retomar um banco de dados."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="sonyama;barbkess;mausher"/>

# Cmdlets do PowerShell e as APIs REST para SQL Data Warehouse

Diversas tarefas de administração do SQL Data Warehouse podem ser gerenciadas usando APIs REST ou cmdlets do Azure PowerShell. Abaixo estão alguns exemplos de como usar comandos do PowerShell para automatizar tarefas comuns no SQL Data Warehouse. Para obter alguns bons exemplos de REST, consulte o artigo [Gerenciar escalabilidade com REST][].

> [AZURE.NOTE]  Para usar o Azure PowerShell com o SQL Data Warehouse, você precisa do Azure PowerShell versão 1.0.3 ou superior. Você pode verificar a versão executando **Get-Module -ListAvailable -Name Azure**. A versão mais recente pode ser instalada pelo [Microsoft Web Platform Installer][]. Para obter mais informações sobre como instalar a versão mais recente, consulte [Como instalar e configurar o Azure PowerShell][].

## Introdução aos cmdlets do Azure PowerShell

1. Abra o Windows PowerShell.
2. No prompt do PowerShell, execute estes comandos para entrar no Azure Resource Manager e selecione sua assinatura.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## Pausar Exemplo do SQL Data Warehouse

Pause um banco de dados denominado "Database02" hospedado em um servidor denominado "Server01." O servidor está em um grupo de recursos do Azure denominado "ResourceGroup1".

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Uma variação, esse exemplo redireciona o objeto recuperado para [Suspend-AzureRmSqlDatabase][]. Como resultado, o banco de dados é pausado. O comando final mostra os resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## Iniciar Exemplo do SQL Data Warehouse

Retome a operação de um banco de dados denominado "Database02" hospedado em um servidor denominado "Server01." O servidor está contido em um grupo de recursos denominado "ResourceGroup1".

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Uma variação, este exemplo recupera um banco de dados denominado “Database02” de um servidor chamado “Server01” que está contido em um grupo de recursos denominado “ResourceGroup1”. Ele canaliza o objeto recuperado para [Resume-AzureRmSqlDatabase][].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [AZURE.NOTE] Observe que, se o servidor for foo.database.windows.net, use "foo" como o -ServerName nos cmdlets do PowerShell.

## Cmdlets do PowerShell Usados Frequentemente

Esses cmdlets do PowerShell são usados com frequência com o SQL Data Warehouse do Azure.

- [Get-AzureRmSqlDatabase][]
- [Get-AzureRmSqlDeletedDatabaseBackup][]
- [Get-AzureRmSqlDatabaseRestorePoints][]
- [New-AzureRmSqlDatabase][]
- [Remove-AzureRmSqlDatabase][]
- [Restore-AzureRmSqlDatabase][]
- [Resume-AzureRmSqlDatabase][]
- [Select-AzureRmSubscription][]
- [Set-AzureRmSqlDatabase][]
- [Suspend-AzureRmSqlDatabase][]

## Próximas etapas
Para obter mais exemplos do PowerShell, consulte:

- [Criar um SQL Data Warehouse usando o PowerShell][]
- [Restauração do banco de dados][]

Para obter uma lista de todas as tarefas que podem ser automatizadas com o PowerShell, consulte [Cmdlets do Banco de Dados SQL do Azure][]. Para obter uma lista de tarefas que podem ser automatizadas com REST, consulte [Operações para Banco de Dados SQL do Azure][].

<!--Image references-->

<!--Article references-->
[Como instalar e configurar o Azure PowerShell]: ./powershell-install-configure.md
[Criar um SQL Data Warehouse usando o PowerShell]: ./sql-data-warehouse-get-started-provision-powershell.md
[Restauração do banco de dados]: ./sql-data-warehouse-manage-database-restore-powershell.md
[Gerenciar escalabilidade com REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Cmdlets do Banco de Dados SQL do Azure]: https://msdn.microsoft.com/library/mt574084.aspx
[Operações para Banco de Dados SQL do Azure]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0810_2016-->