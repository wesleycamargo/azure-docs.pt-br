<properties
   pageTitle="Tarefas para escalar horizontalmente os recursos de computação no SQL Data Warehouse do Azure | Microsoft Azure"
   description="Consulte como pausar (suspender) ou iniciar (retomar) os recursos de computação para um banco de dados do SQL Data Warehouse do Azure e como escalar horizontalmente ou escalar de volta as configurações de DWU para o SLO (objeto de nível de serviço). Essas tarefas usam os cmdlets do Azure PowerShell."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/21/2016"
   ms.author="barbkess;sonyama"/>

# Tarefas para escalar horizontalmente ou escalar de volta os recursos de computação no SQL Data Warehouse do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)


Escale horizontalmente de forma elástica os recursos de computação e memória para atender às demandas em transformação de sua carga de trabalho e economizar custos dimensionando os recursos de volta durante os horários fora de pico.

Esta coleção de tarefas usa cmdlets do PowerShell para:

- Pausar recursos de computação
- Retomar recursos de computação
- Alterar recursos de computação ajustando as DWUs

## Antes de começar

### Instale a versão mais recente do Azure PowerShell

> [AZURE.NOTE]  Para usar o Azure PowerShell com o SQL Data Warehouse, você precisa instalar a versão 1.0.3 ou superior do Azure PowerShell. Para verificar a versão atual, execute o comando **Get-Module -ListAvailable -Name Azure**. Você pode instalar a versão mais recente do [Microsoft Web Platform Installer][]. Para saber mais, confira [Como instalar e configurar o Azure PowerShell][].

### Introdução aos cmdlets do Azure PowerShell

Introdução:

1. Abra o PowerShell do Azure. 
2. No prompt do PowerShell, execute estes comandos para entrar no Azure Resource Manager e selecione sua assinatura.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## Tarefa nº 1: pausar computação

[AZURE.INCLUDE [Descrição de pausa do SQL Data Warehouse](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar um banco de dados, use o cmdlet [Suspend-AzureRmSqlDatabase][]. O exemplo a seguir pausa um banco de dados chamado Database02 hospedado em um servidor chamado Server01. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1.

> [AZURE.NOTE] Observe que, se o servidor for foo.database.windows.net, use "foo" como o -ServerName nos cmdlets do PowerShell.

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Uma variação, o próximo exemplo recupera o banco de dados para o objeto $database. Ele então redireciona o objeto para [Suspend-AzureRmSqlDatabase][]. Os resultados são armazenados no objeto resultDatabase. O comando final mostra os resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## Tarefa nº 2: retomar a computação

[AZURE.INCLUDE [Descrição de retomada do SQL Data Warehouse](../../includes/sql-data-warehouse-resume-description.md)]

Para iniciar um banco de dados, use o cmdlet [Resume-AzureRmSqlDatabase][]. O exemplo a seguir inicia um banco de dados chamado Database02 hospedado em um servidor chamado Server01. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Uma variação, o próximo exemplo recupera o banco de dados para o objeto $database. Ele então redireciona o objeto [Resume-AzureRmSqlDatabase][] e armazena os resultados em $resultDatabase. O comando final mostra os resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```


## Tarefa nº 3: escalar as DWUs

[AZURE.INCLUDE [Descrição de DWUs de escala do SQL Data Warehouse](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para alterar as DWUs, use o cmdlet do PowerShell [Set-AzureRmSqlDatabase][]. O exemplo a seguir define o objetivo de nível de serviço como DW1000 para o banco de dados MySQLDW, que está hospedado no servidor MyServer.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

## Próximas etapas

Para outras tarefas de gerenciamento, consulte [Visão geral de gerenciamento][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Visão geral de gerenciamento]: ./sql-data-warehouse-overview-manage.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0427_2016-->