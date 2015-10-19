<properties
   pageTitle="Introdução aos cmdlets no SQL Data Warehouse"
   description="Suspender e reiniciar o SQL Data Warehouse usando cmdlets do PowerShell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sidneyh"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="twounder;sidneyh;barbkess"/>

# Introdução aos cmdlets e APIs REST do Data Warehouse do Azure

O SQL Data Warehouse pode ser gerenciado usando APIs REST ou cmdlets do PowerShell do Azure.

Os comandos definidos para o **Banco de Dados SQL do Azure** também são usados para o **SQL Data Warehouse**. Para obter uma lista atual, consulte [Cmdlets do SQL do Azure](https://msdn.microsoft.com/library/azure/dn546726.aspx). Os cmdlets **Suspend-AzureSqlDatabase** e **Resume-AzureSqlDatabase** (abaixo) são adições projetadas para o SQL Data Warehouse.

Da mesma forma, as APIs REST para o **Banco de Dados do SQL Azure** também podem ser usadas para as instâncias do **SQL Data Warehouse**. Para obter a lista atual, consulte [Operações de bancos de dados SQL do Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx).

## Obter e executar os cmdlets do PowerShell do Azure

1. Para baixar o módulo PowerShell do Azure, execute o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). 
2. Para executar o módulo, na janela de início, digite **Microsoft Azure PowerShell**.
3. Se ainda não tiver adicionado sua conta ao computador, execute o cmdlet a seguir. (Para saber mais, consulte [Como instalar e configurar o Azure PowerShell]():

		Add-AzureAccount
3. Alterne o modo com este cmdlet:

		Switch-AzureMode AzureResourceManager

## Suspend-AzureSqlDatabase
### Exemplo 1: Pausar um banco de dados por nome em um servidor

Este exemplo pausa um banco de dados denominado "Database02" hospedado em um servidor denominado "Server01." O servidor está em um grupo de recursos do Azure denominado "ResourceGroup1".

    Suspend-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName
    "Server01" –DatabaseName "Database02"

### Exemplo 2: Pausar um objeto de banco de dados

Este exemplo recupera um banco de dados denominado “Database02” de um servidor chamado “Server01” contido em um grupo de recursos denominado “ResourceGroup1”. Ele canaliza do objeto recuperado para **Suspend-AzureSqlDatabase**. Como resultado, o banco de dados é pausado.

	$database = Get-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
	$resultDatabase = $database | Suspend-AzureSqlDatabase

## Resume-AzureSqlDatabase

### Exemplo 1: Retomar um banco de dados por nome em um servidor

Este exemplo retoma a operação de um banco de dados denominado "Database02" hospedado em um servidor denominado "Server01." O servidor está contido em um grupo de recursos denominado "ResourceGroup1".

	Resume-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"

### Exemplo 2: Retomando um objeto de banco de dados

Este exemplo recupera um banco de dados denominado “Database02” de um servidor chamado “Server01” que está contido em um grupo de recursos denominado “ResourceGroup1”. O objeto é canalizado para **Resume-AzureSqlDatabase**.

	$database = Get-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
	$resultDatabase = $database | Resume-AzureSqlDatabase

## Get-AzureSqlDatabaseRestorePoints

Esse cmdlet lista os pontos de restauração de backup para um banco de dados SQL do Azure. Os pontos de restauração são usados para restaurar o banco de dados. As propriedades do objeto retornado são como se segue.

Propriedade|Descrição
---|---
RestorePointType|DISCRETOS / CONTÍNUOS. Os pontos de restauração discretos descrevem os possíveis pontos no tempo para os quais um banco de dados SQL do Azure podem ser restaurados. Os pontos de restauração contínuos descrevem os possíveis pontos no tempo mais antigos para os quais um banco de dados SQL do Azure pode ser restaurado. O banco de dados pode ser restaurado para qualquer ponto no tempo após o ponto mais antigo.
EarliestRestoreDate|Tempo de restauração mais antigo (preenchido quando restorePointType = CONTÍNUO)
RestorePointCreationDate |Tempo do instantâneo de backup (preenchido quando restorePointType = DISCRETO)

### Exemplo 1: Recuperando pontos de restauração de um banco de dados por nome em um servidor
Este exemplo recupera os pontos de restauração para um banco de dados denominado “Database02” de um servidor chamado “Server01” contido em um grupo de recursos denominado “ResourceGroup1”.

	$restorePoints = Get-AzureSqlDatabaseRestorePoints –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"



### Exemplo 2: Retomando um objeto de banco de dados

Este exemplo recupera um banco de dados denominado “Database02” de um servidor chamado “Server01”, contido em um grupo de recursos denominado “ResourceGroup1”. O objeto de banco de dados é canalizado para **Get-AzureSqlDatabase** e o resultado são os pontos de restauração do banco de dados.

	$database = Get-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
	$restorePoints = $database | Get-AzureSqlDatabaseRestorePoints



> [AZURE.NOTE]Observe que se o servidor for foo.database.windows.net, use "foo" como o nome do servidor nos cmdlets do powershell.


## Próximas etapas
Para obter mais informações de referência, consulte [Visão geral de referência do SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Visão geral de referência do SQL Data Warehouse]: sql-data-warehouse-overview-reference.md
[How to install and configure Azure PowerShell]: ../articles/powershell-install-configure.md

<!--MSDN references-->


<!--Other Web references-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!---HONumber=Oct15_HO2-->