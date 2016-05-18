<properties
   pageTitle="Cmdlets do PowerShell para SQL Data Warehouse do Azure"
   description="Encontre os principais cmdlets do PowerShell para SQL Data Warehouse do Azure, inclusive sobre como pausar e retomar um banco de dados."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyama"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/02/2016"
   ms.author="sonyama;barbkess;mausher"/>

# Cmdlets do PowerShell e as APIs REST para SQL Data Warehouse

O SQL Data Warehouse pode ser gerenciado usando APIs REST ou cmdlets do PowerShell do Azure.

A maioria dos comandos definidos para o **Banco de Dados SQL do Azure** também é usada para o **SQL Data Warehouse**. Para obter uma lista atual, consulte [Cmdlets do SQL do Azure](https://msdn.microsoft.com/library/mt574084.aspx). Os cmdlets [Suspend-AzureRmSqlDatabase][] e [Resume-AzureRmSqlDatabase][] são adições projetadas para o SQL Data Warehouse.

Da mesma forma, as APIs REST para o **Banco de Dados do SQL Azure** também podem ser usadas para as instâncias do **SQL Data Warehouse**. Para obter a lista atual, consulte [Operações de bancos de dados SQL do Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx).

## Introdução aos cmdlets do Azure PowerShell

1. Para baixar o módulo PowerShell do Azure, execute o [Microsoft Web Platform Installer](http://aka.ms/webpi-azps). Para obter mais informações sobre esse instalador, confira [Como instalar e configurar o Azure PowerShell][].
2. Para iniciar o PowerShell, clique em **Iniciar** e digite **Windows PowerShell**.
3. No prompt do PowerShell, execute estes comandos para entrar no Azure Resource Manager e selecione sua assinatura.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```


## Cmdlets do PowerShell usados com frequência

Esses cmdlets do PowerShell são usados com frequência com o Azure SQL Data Warehouse:


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


## Exemplos para o SQL Data Warehouse

Esses exemplos destinam-se a recursos que se aplicam somente ao SQL Data Warehouse.

### [Suspend-AzureRmSqlDatabase][]

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

### [Resume-AzureRmSqlDatabase][]

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


## Próximas etapas
Para obter mais informações de referência, consulte [Visão geral de referência do SQL Data Warehouse][]. Para obter mais exemplos do PowerShell, consulte:
- [Criar um SQL Data Warehouse usando o PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
- [Restaurar do instantâneo](sql-data-warehouse-backup-and-restore-from-snapshot.md)
- [Restauração geográfica do instantâneo](sql-data-warehouse-backup-and-restore-from-geo-restore-snapshot.md)

<!--Image references-->

<!--Article references-->
[Visão geral de referência do SQL Data Warehouse]: sql-data-warehouse-overview-reference.md
[Como instalar e configurar o Azure PowerShell]: ../articles/powershell-install-configure.md

<!--MSDN references-->
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureRmSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619337.aspx



<!--Other Web references-->

<!---HONumber=AcomDC_0420_2016-->