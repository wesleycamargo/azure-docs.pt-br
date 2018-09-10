---
title: Criar um agente de Trabalho Elástico do Banco de Dados SQL do Azure usando o PowerShell | Microsoft Docs
description: Saiba como criar e gerenciar um agente de Trabalho Elástico usando o PowerShell.
services: sql-database
author: johnpaulkee
manager: craigg
ms.service: sql-database
ms.topic: tutorial
ms.date: 06/14/2018
ms.author: joke
ms.openlocfilehash: 2cc8db0ce849e0f0d376824665aac7dbc2af29db
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035210"
---
# <a name="create-an-elastic-job-agent-using-powershell"></a>Criar um agente de Trabalho Elástico usando o PowerShell

Os [trabalhos elásticos](elastic-jobs-overview.md) habilitam a execução de um ou mais scripts T-SQL (Transact-SQL) em paralelo entre vários bancos de dados.

Neste tutorial, você aprenderá as etapas necessárias para executar uma consulta em vários bancos de dados:

> [!div class="checklist"]
> * Criar um agente de Trabalho Elástico
> * Criar credenciais de trabalho para que os trabalhos possam executar scripts em seus destinos
> * Definir os destinos (servidores, pools elásticos, bancos de dados, mapas de fragmento) nos quais você deseja executar o trabalho
> * Criar credenciais no escopo do banco de dados nos bancos de dados de destino para que o agente se conecte e execute trabalhos
> * Criar um trabalho
> * Adicionar etapas de trabalho a um trabalho
> * Iniciar a execução de um trabalho
> * Monitorar um trabalho

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Instalar o módulo **AzureRM.Sql** 4.8.1-preview mais recente para obter os cmdlets de Trabalho Elástico. Execute os comandos do PowerShell a seguir com acesso administrativo.

```powershell
# Installs the latest PackageManagement powershell package which PowershellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# Installs the latest PowershellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Restart your powershell session with administrative access

# Places AzureRM.Sql preview cmdlets side by side with existing AzureRM.Sql version
Install-Module -Name AzureRM.Sql -AllowPrerelease -RequiredVersion 4.8.1-preview -Force

# Import the AzureRM.Sql 4.8.1 module
Import-Module AzureRM.Sql -RequiredVersion 4.8.1

# Confirm if module successfully imported - if the imported version is 4.8.1, then continue
Get-Module AzureRM.Sql
```

## <a name="create-required-resources"></a>Criar recursos necessários

A criação de um agente de Trabalho Elástico requer um banco de dados (S0 ou superior) para uso como o [banco de dados de trabalhos](elastic-jobs-overview.md#job-database). 

*O script a seguir cria um novo grupo de recursos, servidor e banco de dados para uso como o banco de dados de trabalhos. O script abaixo também cria um segundo servidor com dois bancos de dados em branco nos quais os trabalhos serão executados.*

Os Trabalhos Elásticos não têm nenhum requisito de nomenclatura específico. Você pode usar as convenções de nomenclatura que desejar, desde que estejam em conformidade com os [requisitos do Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

```powershell
# Sign in to your Azure account
Connect-AzureRmAccount

# Create a resource group
Write-Output "Creating a resource group..."
$ResourceGroupName = Read-Host "Please enter a resource group name"
$Location = Read-Host "Please enter an Azure Region"
$Rg = New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
$Rg

# Create a server
Write-Output "Creating a server..."
$AgentServerName = Read-Host "Please enter an agent server name"
$AgentServerName = $AgentServerName + "-" + [guid]::NewGuid()
$AdminLogin = Read-Host "Please enter the server admin name"
$AdminPassword = Read-Host "Please enter the server admin password"
$AdminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$AdminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AdminLogin, $AdminPasswordSecure
$AgentServer = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $AgentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$AgentServer | New-AzureRmSqlServerFirewallRule -AllowAllAzureIPs
$AgentServer

# Create the job database
Write-Output "Creating a blank SQL database to be used as the Job Database..."
$JobDatabaseName = "JobDatabase"
$JobDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $JobDatabaseName -RequestedServiceObjectiveName "S0"
$JobDatabase
```

```powershell
# Create a target server and some sample databases - uses the same admin credential as the agent server just for simplicity
Write-Output "Creating target server..."
$TargetServerName = Read-Host "Please enter a target server name"
$TargetServerName = $TargetServerName + "-" + [guid]::NewGuid()
$TargetServer = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $TargetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set target server firewall rules to allow all Azure IPs
$TargetServer | New-AzureRmSqlServerFirewallRule -AllowAllAzureIPs
$TargetServer | New-AzureRmSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$TargetServer

# Create some sample databases to execute jobs against...
$Db1 = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb1"
$Db1
$Db2 = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb2"
$Db2
```

## <a name="enable-the-elastic-jobs-preview-for-your-subscription"></a>Habilitar a versão prévia dos Trabalhos Elásticos para a assinatura

Para usar os Trabalhos Elásticos, registre o recurso em sua assinatura do Azure executando o seguinte comando (isso só precisa ser executado uma vez em cada assinatura em que você deseja usar os Trabalhos Elásticos):

```powershell
Register-AzureRmProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

## <a name="create-the-elastic-job-agent"></a>Criar o agente de Trabalho Elástico

Um agente de Trabalho Elástico é um recurso do Azure para criar, executar e gerenciar trabalhos. O agente executa trabalhos com base em uma agenda ou como um único trabalho.

O cmdlet **New-AzureRmSqlElasticJobAgent** requer um banco de dados SQL do Azure existente, ou seja, os parâmetros *ResourceGroupName*, *ServerName* e  *DatabaseName* precisam apontar para recursos existentes.

```powershell
Write-Output "Creating job agent..."
$AgentName = Read-Host "Please enter a name for your new Elastic Job agent"
$JobAgent = $JobDatabase | New-AzureRmSqlElasticJobAgent -Name $AgentName
$JobAgent
```

## <a name="create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets"></a>Criar credenciais de trabalho para que os trabalhos possam executar scripts em seus destinos

Os trabalhos usam credenciais no escopo do banco de dados para se conectar aos bancos de dados de destino especificados pelo grupo de destino após a execução. Essas credenciais no escopo do banco de dados também são usadas para se conectar ao banco de dados mestre e enumerar todos os bancos de dados em um servidor ou um pool elástico, quando um destes é usado como o tipo de membro do grupo de destino.

As credenciais no escopo do banco de dados devem ser criadas no banco de dados de trabalhos.  
Todos os bancos de dados de destino devem ter um logon com permissões suficientes para que o trabalho seja concluído com êxito.

![Credenciais de trabalhos elásticos](media/elastic-jobs-overview/job-credentials.png)

Além das credenciais na imagem, observe a adição dos comandos *GRANT* no script a seguir. Essas permissões são necessárias para o script que escolhemos como exemplo para o trabalho. O exemplo cria uma nova tabela nos bancos de dados de destino; cada banco de dados de destino precisa ter as permissões apropriadas para ser executado com êxito.

Para criar as credenciais de trabalho necessárias (no banco de dados de trabalho), execute o seguinte script:

```powershell
# In the master database (target server)
# - Create the master user login
# - Create the master user from master user login
# - Create the job user login
$Params = @{
  'Database' = 'master'
  'ServerInstance' =  $TargetServer.ServerName + '.database.windows.net'
  'Username' = $AdminLogin
  'Password' = $AdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN masteruser WITH PASSWORD='password!123'"
}
Invoke-SqlCmd @Params
$Params.Query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @Params
$Params.Query = "CREATE LOGIN jobuser WITH PASSWORD='password!123'"
Invoke-SqlCmd @Params

# For each of the target databases
# - Create the jobuser from jobuser login
# - Make sure they have the right permissions for successful script execution
$TargetDatabases = @( $Db1.DatabaseName, $Db2.DatabaseName )
$CreateJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$GrantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$GrantCreateScript = "GRANT CREATE TABLE TO jobuser"

$TargetDatabases | % {
  $Params.Database = $_

  $Params.Query = $CreateJobUserScript
  Invoke-SqlCmd @Params

  $Params.Query = $GrantAlterSchemaScript
  Invoke-SqlCmd @Params

  $Params.Query = $GrantCreateScript
  Invoke-SqlCmd @Params
}

# Create job credential in Job database for master user
Write-Output "Creating job credentials..."
$LoginPasswordSecure = (ConvertTo-SecureString -String "password!123" -AsPlainText -Force)

$MasterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $LoginPasswordSecure
$MasterCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "masteruser" -Credential $MasterCred

$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $LoginPasswordSecure
$JobCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "jobuser" -Credential $JobCred
```

## <a name="define-the-target-databases-you-want-to-run-the-job-against"></a>Definir os bancos de dados de destino nos quais os trabalhos serão executados

Um [grupo de destino](elastic-jobs-overview.md#target-group) define o conjunto de um ou mais bancos de dados em que uma etapa de trabalho será executada. 

O seguinte trecho de código cria dois grupos de destino: *ServerGroup* e *ServerGroupExcludingDb2*. *ServerGroup* tem como alvo todos os bancos de dados existentes no momento da execução e *ServerGroupExcludingDb2* tem como alvo todos os bancos de dados no servidor, exceto *TargetDb2*:

```powershell
Write-Output "Creating test target groups..."
# Create ServerGroup target group
$ServerGroup = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name 'ServerGroup'
$ServerGroup | Add-AzureRmSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName

# Create ServerGroup with an exclusion of Db2
$ServerGroupExcludingDb2 = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$ServerGroupExcludingDb2 | Add-AzureRmSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName
$ServerGroupExcludingDb2 | Add-AzureRmSqlElasticJobTarget -ServerName $TargetServerName -Database $Db2.DatabaseName -Exclude
```

## <a name="create-a-job"></a>Criar um trabalho

```powershell
Write-Output "Creating a new job"
$JobName = "Job1"
$Job = $JobAgent | New-AzureRmSqlElasticJob -Name $JobName -RunOnce
$Job
```

## <a name="create-a-job-step"></a>Criar uma etapa de trabalho

Este exemplo define duas etapas de trabalho para que o trabalho seja executado. A primeira etapa de trabalho (*etapa 1*) cria uma nova tabela (*Step1Table*) em cada banco de dados no grupo de destino *ServerGroup*. A segunda etapa de trabalho (*etapa 2*) cria uma nova tabela (*Step2Table*) em cada banco de dados, exceto para *TargetDb2*, pois o grupo de destino [definido anteriormente](#define-the-target-databases-you-want-to-run-the-job-against) está especificado para excluí-lo.

```powershell
Write-Output "Creating job steps"
$SqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$SqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$Job | Add-AzureRmSqlElasticJobStep -Name "step1" -TargetGroupName $ServerGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText1
$Job | Add-AzureRmSqlElasticJobStep -Name "step2" -TargetGroupName $ServerGroupExcludingDb2.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText2
```


## <a name="run-the-job"></a>Executar o trabalho

Para iniciar o trabalho imediatamente, execute o seguinte comando:

```powershell
Write-Output "Start a new execution of the job..."
$JobExecution = $Job | Start-AzureRmSqlElasticJob
$JobExecution
```

Após a conclusão bem-sucedida, você deverá ver duas tabelas novas no TargetDb1 e apenas uma tabela nova no TargetDb2:


   ![verificação de novas tabelas no SSMS](media/elastic-jobs-overview/job-execution-verification.png)




## <a name="monitor-status-of-job-executions"></a>Monitorar status de execuções de trabalho

Os seguintes trechos de código obtêm os detalhes de execução do trabalho:

```powershell
# Get the latest 10 executions run
$JobAgent | Get-AzureRmSqlElasticJobExecution -Count 10

# Get the job step execution details
$JobExecution | Get-AzureRmSqlElasticJobStepExecution

# Get the job target execution details
$JobExecution | Get-AzureRmSqlElasticJobTargetExecution -Count 2
```

## <a name="schedule-the-job-to-run-later"></a>Agendar o trabalho para execução posterior

Para agendar um trabalho para execução em um momento específico, execute o seguinte comando:

```powershell
# Run every hour starting from now
$Job | Set-AzureRmSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

## <a name="clean-up-resources"></a>Limpar recursos

Exclua os recursos do Azure criados neste tutorial excluindo o grupo de recursos.

> [!TIP]
> Se você planeja continuar a usar esses trabalhos, não limpe os recursos criados neste artigo. Caso contrário, use as etapas a seguir para excluir todos os recursos criados neste artigo.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $ResourceGroupName
```


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você executou um script Transact-SQL em um conjunto de bancos de dados.  Você aprendeu a realizar as seguintes tarefas:

> [!div class="checklist"]
> * Criar um agente de Trabalho Elástico
> * Criar credenciais de trabalho para que os trabalhos possam executar scripts em seus destinos
> * Definir os destinos (servidores, pools elásticos, bancos de dados, mapas de fragmento) nos quais você deseja executar o trabalho
> * Criar credenciais no escopo do banco de dados nos bancos de dados de destino para que o agente se conecte e execute trabalhos
> * Criar um trabalho
> * Adicionar uma etapa de trabalho ao trabalho
> * Iniciar a execução do trabalho
> * Monitorar trabalho

> [!div class="nextstepaction"]
>[Gerenciar Trabalhos Elásticos usando Transact-SQL](elastic-jobs-tsql.md)