---
title: "Nova configuração do Banco de Dados SQL com o PowerShell | Microsoft Docs"
description: "Saiba como criar um banco de dados SQL com o PowerShell. As tarefas comuns de configuração de banco de dados podem ser gerenciadas por meio de cmdlets do PowerShell."
keywords: "criar um novo banco de dados sql, configuração do banco de dados"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: 7d99869b-cec5-4583-8c1c-4c663f4afd4d
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 12/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 3ba16154857f8e7b59a1013b736d6131a4161185
ms.openlocfilehash: d00b7b543f105fd944e91f6ed27e6613366c6716


---

# <a name="get-started-with-azure-sql-database-servers-databases-and-firewall-rules-by-using-azure-powershell"></a>Introdução aos servidores do Banco de Dados SQL, bancos de dados e regras de firewall usando o Azure PowerShell

Neste tutorial de introdução, você aprenderá a usar o PowerShell para:

* Criar um novo grupo de recursos do Azure
* Criar um servidor lógico do SQL do Azure
* Exibir propriedades do SQL Server do Azure
* Criar uma regra de firewall no nível de servidor
* Criar o banco de dados de exemplo do AdventureWorksLT como um banco de dados único
* Exibir as propriedades do banco de dados de exemplo do AdventureWorksLT
* Criar um banco de dados em branco

Neste tutorial, você irá:

* Conectar-se ao servidor lógico e seu banco de dados mestre
* Exibir propriedades do banco de dados mestre
* Conectar-se ao banco de dados de exemplo
* Exibir propriedades do banco de dados de usuário

Ao concluir este tutorial, você terá um banco de dados de exemplo e um banco de dados em branco em execução em um grupo de recursos do Azure e conectado a um servidor lógico. Você também terá uma regra de firewall de nível de servidor configurada para habilitar a entidade de segurança de nível de servidor fazer logon no servidor de um endereço IP especificado (ou o intervalo de endereços IP). 

**Tempo estimado:** este tutorial levará cerca de 30 minutos (supondo que você já atendeu aos pré-requisitos).


> [!TIP]
> Você pode executar as mesmas tarefas em um tutorial de introdução usando o [portal do Azure](sql-database-get-started.md).
>


## <a name="prerequisites"></a>Pré-requisitos

* Você precisa de uma conta do Azure. Você pode [abrir uma conta gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F) ou [Ativar os benefícios de assinante do Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* Você deve estar conectado usando uma conta que seja membro da função de proprietário ou colaborador da assinatura. Para obter mais informações sobre o controle de acesso baseado em função (RBAC), confira [Introdução ao gerenciamento de acesso no Portal do Azure](../active-directory/role-based-access-control-what-is.md).

* Você precisa do arquivo .bacpac do banco de dados de exemplo do AdventureWorksLT no armazenamento de blobs do Azure

### <a name="download-the-adventureworkslt-sample-database-bacpac-file-and-save-it-in-azure-blob-storage"></a>Baixe o arquivo .bacpac do banco de dados de exemplo do AdventureWorksLT e salve-o no armazenamento de blobs do Azure

Este tutorial cria um novo banco de dados AdventureWorksLT importando um arquivo .bacpac do Armazenamento do Azure. A primeira etapa é obter uma cópia do AdventureWorksLT.bacpac e carregá-la no armazenamento de blobs.
As etapas a seguir deixam o banco de dados de exemplo pronto para a importação:

1. [Baixe o AdventureWorksLT.bacpac](https://sqldbbacpacs.blob.core.windows.net/bacpacs/AdventureWorksLT.bacpac) e salve-o com uma extensão de arquivo .bacpac.
2. [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) - você pode criar a conta de armazenamento com as configurações padrão.
3. Crie um novo **Contêiner** navegando para a conta de armazenamento, selecione **Blobs**, em seguida, clique em **+Contêiner**.
4. Carregue o arquivo .bacpac no contêiner de blobs em sua conta de armazenamento. Você pode usar o botão **Carregar** na parte superior da página do contêiner ou [usar o AzCopy](../storage/storage-use-azcopy.md#blob-upload). 
5. Depois de salvar o AdventureWorksLT.bacpac, é necessário a URL e chave da conta de armazenamento para importar posteriormente o trecho de código neste tutorial. 
   * Selecione o arquivo bacpac e copie a URL. Ele será semelhante a https://{nome-conta-armazenamento}.blob.core.windows.net/{nome-contêiner}/AdventureWorksLT.bacpac. Na página da conta de armazenamento, clique em **Chaves de acesso**e copie **key1**.


[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]


## <a name="create-a-new-logical-sql-server-using-azure-powershell"></a>Criar um novo servidor SQL lógico usando o Azure PowerShell

Você precisa de um grupo de recursos para conter o servidor, portanto, a primeira etapa é criar um novo grupo de recursos e o servidor ([New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroup), [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserver)) ou obter referências para os existentes ([Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/get-azurermresourcegroup), [Get-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqlserver)).
Os trechos a seguir criarão um grupo de recursos e o SQL Server Azure, se ainda não existirem:

Para obter uma lista de locais do Azure válidos e formato da cadeia de caracteres, consulte a seção [Trechos do código auxiliar](#helper-snippets) abaixo.
```
# Create new, or get existing resource group
############################################

$resourceGroupName = "{resource-group-name}"
$resourceGroupLocation = "{resource-group-location}"

$myResourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ea SilentlyContinue

if(!$myResourceGroup)
{
   Write-Output "Creating resource group: $resourceGroupName"
   $myResourceGroup = New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else
{
   Write-Output "Resource group $resourceGroupName already exists:"
}
$myResourceGroup



# Create a new, or get existing server
######################################

$serverName = "{server-name}"
$serverVersion = "12.0"
$serverLocation = $resourceGroupLocation
$serverResourceGroupName = $resourceGroupName

$serverAdmin = "{server-admin}"
$serverAdminPassword = "{server-admin-password}"

$securePassword = ConvertTo-SecureString –String $serverAdminPassword –AsPlainText -Force
$serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue
if(!$myServer)
{
   Write-Output "Creating SQL server: $serverName"
   $myServer = New-AzureRmSqlServer -ResourceGroupName $serverResourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
}
else
{
   Write-Output "SQL server $serverName already exists:"
}
$myServer

```


## <a name="view-the-logical-sql-server-properties-using-azure-powershell"></a>Exibir as propriedades do servidor SQL lógico usando o Azure PowerShell

```
#$serverResourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 

Write-Host "Server name: " $myServer.ServerName
Write-Host "Fully qualified server name: $serverName.database.windows.net"
Write-Host "Server location: " $myServer.Location
Write-Host "Server version: " $myServer.ServerVersion
Write-Host "Server administrator login: " $myServer.SqlAdministratorLogin
```


## <a name="create-a-server-level-firewall-rule-using-azure-powershell"></a>Criar uma regra de firewall no nível do servidor usando o Azure PowerShell

Você precisa saber o endereço IP público para definir a regra de firewall. Você pode obter seu endereço IP usando um navegador de sua escolha (pergunte “qual é meu endereço IP”). Para obter detalhes, consulte [regras de firewall](sql-database-firewall-configure.md).

O seguinte usa os cmdlets [Get-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqlserverfirewallrule) e [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserverfirewallrule) para obter uma referência ou criar uma nova regra. Para este trecho, se a regra já existir, ele apenas obtém uma referência e não atualiza os endereços IP inicial e final. Você sempre pode modificar a cláusula **else** para usar o [Set-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverfirewallrule) para a funcionalidade de criação ou atualização.

> [!NOTE] 
> Você pode abrir o firewall do Banco de dados SQL no servidor para um único endereço IP ou um intervalo inteiro de endereços. Abrir o firewall permite que os administradores do SQL e os usuários façam logon em qualquer banco de dados no servidor ao qual eles têm credenciais válidas.
>

```
#$serverName = "{server-name}"
#$serverResourceGroupName = "{resource-group-name}"
$serverFirewallRuleName = "{server-firewall-rule-name}"
$serverFirewallStartIp = "{server-firewall-rule-startIp}"
$serverFirewallEndIp = "{server-firewall-rule-endIp}"

$myFirewallRule = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName"
   $myFirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName -StartIpAddress $serverFirewallStartIp -EndIpAddress $serverFirewallEndIp
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName already exists:"
}
$myFirewallRule
```


## <a name="connect-to-sql-server-using-azure-powershell"></a>Conectar o SQL Server usando o Azure PowerShell

Iremos executar uma consulta rápida no banco de dados mestre para verificar se podemos conectar o servidor. O trecho a seguir usa o [Provedor do .NET Framework para SQL Server (System.Data.SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) para conectar e consultar o banco de dados mestre do servidor. Ele cria uma cadeia de conexão com base nas variáveis usadas nos trechos anteriores. Substitua os espaços reservados pelo administrador do SQL Server e senha usados para criar o servidor nas etapas anteriores.


```
#$serverName = "{server-name}"
#$serverAdmin = "{server-admin}"
#$serverAdminPassword = "{server-admin-password}"
$databaseName = "master"

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=$serverAdmin;Password=$serverAdminPassword" + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"



$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()


$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()
```


## <a name="create-new-adventureworkslt-sample-database-using-azure-powershell"></a>Criar um novo banco de dados de exemplo do AdventureWorksLT usando o Azure PowerShell

O trecho a seguir importa um bacpac do banco de dados de exemplo do AdventureWorksLT usando o cmdlet [New-AzureRmSqlDatabaseImport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabaseimport). O bacpac está localizado no armazenamento de blobs do Azure. Depois de executar o cmdlet de importação, você pode monitorar o progresso da operação de importação usando o cmdlet [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqldatabaseimportexportstatus).
$storageUri é a propriedade de URL do arquivo bacpac carregado anteriormente no portal e deve ser semelhante a: https://{conta-armazenamento}.blob.core.windows.net/{contêiner}/AdventureWorksLT.bacpac

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$databaseName = "AdventureWorksLT"
$databaseEdition = "Basic"
$databaseServiceLevel = "Basic"

$storageKeyType = "StorageAccessKey"
$storageUri = "{storage-uri}" # URL of bacpac file you uploaded to your storage account
$storageKey = "{storage-key}" # key1 in the Access keys setting of your storage account

$importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $resourceGroupName –ServerName $serverName –DatabaseName $databaseName –StorageKeytype $storageKeyType –StorageKey $storageKey -StorageUri $storageUri –AdministratorLogin $serverAdmin –AdministratorLoginPassword $securePassword –Edition $databaseEdition –ServiceObjectiveName $databaseServiceLevel -DatabaseMaxSizeBytes 5000000


Do {
     $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
     Write-host "Importing database..." $importStatus.StatusMessage
     Start-Sleep -Seconds 30
     $importStatus.Status
   }
   until ($importStatus.Status -eq "Succeeded")
$importStatus
```



## <a name="view-database-properties-using-azure-powershell"></a>Exibir as propriedades do banco de dados no Azure PowerShell

Depois de criar o banco de dados, exiba algumas de suas propriedades.

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
#$databaseName = "{database-name}"

$myDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName


Write-Host "Database name: " $myDatabase.DatabaseName
Write-Host "Server name: " $myDatabase.ServerName
Write-Host "Creation date: " $myDatabase.CreationDate
Write-Host "Database edition: " $myDatabase.Edition
Write-Host "Database performance level: " $myDatabase.CurrentServiceObjectiveName
Write-Host "Database status: " $myDatabase.Status
```

## <a name="connect-and-query-the-sample-database-using-azure-powershell"></a>Conectar e consultar o banco de dados de exemplo usando o Azure PowerShell

Iremos executar uma consulta rápida no banco de dados AdventureWorksLT para verificar se podemos conectar. O trecho a seguir usa o [Provedor do .NET Framework para SQL Server (System.Data.SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) para conectar e consultar o banco de dados. Ele cria uma cadeia de conexão com base nas variáveis usadas nos trechos anteriores. Substitua o espaço reservado pela senha de administrador do SQL Server.

```
#$serverName = {server-name}
#$serverAdmin = "{server-admin}"
#$serverAdminPassword = "{server-admin-password}"
#$databaseName = {database-name}

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=$serverAdmin;Password=$serverAdminPassword" + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"


$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()


$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()
```

## <a name="create-a-new-blank-database-using-azure-powershell"></a>Criar um novo banco de dados em branco usando o Azure PowerShell

```
#$resourceGroupName = {resource-group-name}
#$serverName = {server-name}

$blankDatabaseName = "blankdb"
$blankDatabaseEdition = "Basic"
$blankDatabaseServiceLevel = "Basic"


$myBlankDatabase = New-AzureRmSqlDatabase -DatabaseName $blankDatabaseName -ServerName $serverName -ResourceGroupName $resourceGroupName -Edition $blankDatabaseEdition -RequestedServiceObjectiveName $blankDatabaseServiceLevel
$myBlankDatabase
```


## <a name="complete-azure-powershell-script-to-create-a-server-firewall-rule-and-database"></a>Concluir o script do Azure PowerShell para criar um servidor, regra de firewall e banco de dados



```
# Sign in to Azure and set the subscription to work with
########################################################

$SubscriptionId = "{subscription-id}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

# User variables
################

$myResourceGroupName = "{resource-group-name}"
$myResourceGroupLocation = "{resource-group-location}"

$myServerName = "{server-name}"
$myServerVersion = "12.0"
$myServerLocation = $myResourceGroupLocation
$myServerResourceGroupName = $myResourceGroupName
$myServerAdmin = "{server-admin}"
$myServerAdminPassword = "{server-admin-password}" 

$myServerFirewallRuleName = "{server-firewall-rule-name}"
$myServerFirewallStartIp = "{start-ip}"
$myServerFirewallEndIp = "{end-ip}"

$myDatabaseName = "AdventureWorksLT"
$myDatabaseEdition = "Basic"
$myDatabaseServiceLevel = "Basic"

$myStorageKeyType = "StorageAccessKey"
# Get these values from your Azure storage account:
$myStorageUri = "{http://your-storage-account.blob.core.windows.net/your-container/AdventureWorksLT.bacpac}"
$myStorageKey = "{your-storage-key}"


# Create new, or get existing resource group
############################################

$resourceGroupName = $myResourceGroupName
$resourceGroupLocation = $myResourceGroupLocation

$myResourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ea SilentlyContinue

if(!$myResourceGroup)
{
   Write-host "Creating resource group: $resourceGroupName"
   $myResourceGroup = New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else
{
   Write-host "Resource group $resourceGroupName already exists:"
}
$myResourceGroup


# Create a new, or get existing server
######################################

$serverName = $myServerName
$serverVersion = $myServerVersion
$serverLocation = $myServerLocation
$serverResourceGroupName = $myServerResourceGroupName

$serverAdmin = $myServerAdmin
$serverAdminPassword = $myServerAdminPassword

$securePassword = ConvertTo-SecureString –String $serverAdminPassword –AsPlainText -Force
$serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue
if(!$myServer)
{
   Write-host "Creating SQL server: $serverName"
   $myServer = New-AzureRmSqlServer -ResourceGroupName $serverResourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
}
else
{
   Write-host "SQL server $serverName already exists:"
}
$myServer


# View server properties
##########################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 

Write-Host "Server name: " $myServer.ServerName
Write-Host "Fully qualified server name: $serverName.database.windows.net"
Write-Host "Server location: " $myServer.Location
Write-Host "Server version: " $myServer.ServerVersion
Write-Host "Server administrator login: " $myServer.SqlAdministratorLogin


# Create or update server firewall rule
#######################################

$serverFirewallRuleName = $myServerFirewallRuleName
$serverFirewallStartIp = $myServerFirewallStartIp
$serverFirewallEndIp = $myServerFirewallEndIp

$myFirewallRule = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName"
   $myFirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName -StartIpAddress $serverFirewallStartIp -EndIpAddress $serverFirewallEndIp
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName already exists:"
}
$myFirewallRule


# Connect to the server and master database
###########################################
$databaseName = "master"

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=" + $myServer.SqlAdministratorLogin + ";Password=" + $myServerAdminPassword + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"


$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()

$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()


# Create the AdventureWorksLT database from a bacpac
####################################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName

$databaseName = $myDatabaseName
$databaseEdition = $myDatabaseEdition
$databaseServiceLevel = $myDatabaseServiceLevel

$storageKeyType = $myStorageKeyType
$storageUri = $myStorageUri
$storageKey = $myStorageKey

$importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $resourceGroupName –ServerName $serverName –DatabaseName $databaseName –StorageKeytype $storageKeyType –StorageKey $storageKey -StorageUri $storageUri –AdministratorLogin $serverAdmin –AdministratorLoginPassword $securePassword –Edition $databaseEdition –ServiceObjectiveName $databaseServiceLevel -DatabaseMaxSizeBytes 5000000

Do {
     $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
     Write-host "Importing database..." $importStatus.StatusMessage
     Start-Sleep -Seconds 30
     $importStatus.Status
   }
   until ($importStatus.Status -eq "Succeeded")
$importStatus


# View database properties
##########################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$databaseName = $myDatabaseName

$myDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

Write-Host "Database name: " $myDatabase.DatabaseName
Write-Host "Server name: " $myDatabase.ServerName
Write-Host "Creation date: " $myDatabase.CreationDate
Write-Host "Database edition: " $myDatabase.Edition
Write-Host "Database performance level: " $myDatabase.CurrentServiceObjectiveName
Write-Host "Database status: " $myDatabase.Status


# Query the database
####################

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=" + $myServer.SqlAdministratorLogin + ";Password=" + $myServerAdminPassword + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"

$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()

$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()


# Create a blank database
#########################

$blankDatabaseName = "blankdb"
$blankDatabaseEdition = "Basic"
$blankDatabaseServiceLevel = "Basic"


$myBlankDatabase = New-AzureRmSqlDatabase -DatabaseName $blankDatabaseName -ServerName $serverName -ResourceGroupName $resourceGroupName -Edition $blankDatabaseEdition -RequestedServiceObjectiveName $blankDatabaseServiceLevel
$myBlankDatabase
```



> [!TIP]
> Você pode economizar algum dinheiro enquanto estiver aprendendo excluindo bancos de dados que você não está usando. Para os bancos de dados de edição Básica, você pode restaurá-los dentro de sete dias. No entanto, não exclua um servidor. Se você fizer isso, não poderá recuperar o servidor ou qualquer um de seus bancos de dados excluídos.

## <a name="helper-snippets"></a>Trechos de código auxiliares

```
# Get a list of Azure regions where you can create SQL resources

$sqlRegions = (Get-AzureRmLocation | Where-Object { $_.Providers -eq "Microsoft.Sql" })
foreach ($region in $sqlRegions)
{
   $region.Location
}

# Clean up
# Delete a resource group (and all contained resources)
Remove-AzureRmResourceGroup -Name {resource-group-name}
```

> [!TIP]
> Você pode economizar algum dinheiro enquanto estiver aprendendo excluindo bancos de dados que você não está usando. Para bancos de dados edição Básica, você poderá restaurá-los dentro de sete dias. No entanto, não exclua um servidor. Se você fizer isso, não poderá recuperar o servidor ou qualquer um de seus bancos de dados excluídos.
>

## <a name="next-steps"></a>Próximas etapas
Agora que você concluiu este primeiro tutorial de introdução e criou um banco de dados com alguns dados de exemplo, há vários tutoriais adicionais que talvez queira explorar que compilam o que você aprendeu neste tutorial. 

* Se quiser começar a explorar a segurança do Banco de Dados SQL do Azure, confira [Introdução à segurança](sql-database-get-started-security.md).
* Se você conhece o Excel, saiba como [Conectar um banco de dados SQL no Azure com o Excel](sql-database-connect-excel.md).
* Se você estiver pronto para começar a codificar, escolha a linguagem de programação em [Bibliotecas de conexão para Banco de Dados SQL e SQL Server](sql-database-libraries.md).
* Se você quiser mover seus bancos de dados SQL Server locais para o Azure, confira [Migrando um banco de dados para o Banco de Dados SQL](sql-database-cloud-migrate.md).
* Se você quiser carregar alguns dados em uma nova tabela de um arquivo CSV usando a ferramenta da linha de comando BCP, consulte [Carregando dados no Banco de Dados SQL a partir de um arquivo CSV usando o BCP](sql-database-load-from-csv-with-bcp.md).
* Se você deseja iniciar a criação de tabelas e outros objetos, confira o tópico "Para criar uma tabela" em [Criando uma tabela](https://msdn.microsoft.com/library/ms365315.aspx).

## <a name="additional-resources"></a>Recursos adicionais
[O que é o Banco de Dados SQL?](sql-database-technical-overview.md)


<!--HONumber=Dec16_HO3-->


