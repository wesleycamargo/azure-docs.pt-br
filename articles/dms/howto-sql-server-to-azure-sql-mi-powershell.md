---
title: Migrar o SQL Server para uma Instância Gerenciada do Banco de Dados SQL do Azure com o Serviço de Migração de Banco de Dados e o PowerShell | Microsoft Docs
description: Saiba como migrar do SQL Server local para uma Instância Gerenciada do Banco de Dados SQL do Azure usando Azure PowerShell.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: ed0d65a0f00bd5ebc3227a249beec6bafd791347
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58335922"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-managed-instance-using-azure-powershell"></a>Migrar o SQL Server local para uma Instância Gerenciada do Banco de Dados SQL do Azure usando o Azure PowerShell
Neste artigo, você migra o banco de dados **Adventureworks2012** restaurado em uma instância local do SQL Server 2005 ou posterior para uma Instância Gerenciada do Banco de Dados SQL do Azure usando o Microsoft Azure PowerShell. Migre bancos de dados de uma instância local do SQL Server para uma Instância Gerenciada do Banco de Dados SQL do Azure usando o módulo `Az.DataMigration` no Microsoft Azure PowerShell.

Neste artigo, você aprenderá a:
> [!div class="checklist"]
> * Crie um grupo de recursos.
> * Criar uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Crie um projeto de migração em uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Executar a migração.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir essas etapas, você precisa:

- [SQL Server 2016 ou posterior](https://www.microsoft.com/sql-server/sql-server-downloads) (qualquer edição)
- Para habilitar o protocolo TCP/IP, que está desabilitado por padrão com a instalação do SQL Server Express. Habilite o protocolo TCP/IP seguindo o artigo [Habilitar ou desabilitar um protocolo de rede de servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Uma instância do Banco de Dados SQL do Azure. Crie uma instância do Banco de Dados SQL do Azure seguindo os detalhes no artigo [Criar um banco de dados SQL do Azure no portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Assistente de Migração de Dados](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
- Para criar uma VNET usando o modelo de implantação do Azure Resource Manager, que fornece o Serviço de Migração de Banco de Dados do Azure com conectividade site a site para os servidores de origem locais usando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Para concluir a avaliação da sua migração de esquema e banco de dados local usando o Assistente de migração de dados conforme descrito no artigo [realizando uma avaliação de migração do SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
- Para baixar e instalar o módulo Az.DataMigration da Galeria do PowerShell usando [cmdlet Install-Module do PowerShell](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)
- Para garantir que as credenciais usadas para conectar a instância do SQL Server de origem tenham a permissão [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Para garantir que as credenciais usadas para conectar A instância do Azure SQL DB de destino tenham a permissão CONTROL DATABASE nos bancos de dados do Banco de Dados SQL do Azure de destino.
- Uma assinatura do Azure. Se não tiver uma, crie uma conta[gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Fazer logon em sua assinatura do Microsoft Azure
Use as instruções do artigo [Fazer logon com o Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps) para fazer logon em sua assinatura do Azure usando o PowerShell.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Crie um grupo de recursos antes de criar uma máquina virtual.

Criar um grupo de recursos usando o [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) comando. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na região *EastUS*.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-instance-of-the-azure-database-migration-service"></a>Criar uma instância do Serviço de Migração de Banco de Dados do Azure 
Crie uma nova instância do Serviço de Migração de Banco de Dados do Azure usando o cmdlet `New-AzDataMigrationService`. Esse cmdlet espera os seguintes parâmetros obrigatórios:
- *Nome do Grupo de Recursos do Azure*. Você pode usar [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) comando para criar o grupo de recursos do Azure como mostrado anteriormente e forneça seu nome como um parâmetro.
- *Nome do serviço*. Cadeia de caracteres que corresponde ao nome de serviço exclusivo desejado para o Serviço de Migração de Banco de Dados do Azure 
- *Local*. Especifica o local do serviço. Especifique um local de data center do Azure, como Oeste dos EUA ou Sudeste Asiático
- *Sku*. Esse parâmetro corresponde ao nome do SKU do DMS. Atualmente, os nomes de SKU com suporte são *Basic_1vCore*, *Basic_2vCores* e *GeneralPurpose_4vCores*
- *Identificador de Sub-rede Virtual*. Você pode usar o cmdlet [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) para criar uma sub-rede. 

O exemplo a seguir cria um serviço nomeado *MyDMS* no grupo de recursos *MyDMSResourceGroup* localizado na região *Leste dos EUA* com uma rede virtual nomeada *MyVNET* e sub-rede virtual chamada *MySubnet*.

```powershell
 $vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Criar um projeto de migração
Depois de criar uma instância do Serviço de Migração de Banco de Dados do Azure, crie um projeto de migração. Um projeto do Serviço de Migração de Banco de Dados do Azure exige informações de conexão para as instâncias de origem e de destino, bem como uma lista de bancos de dados que você deseja migrar como parte do projeto.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Criar um objeto de Informações de Conexão de Banco de Dados para as conexões de origem e destino
Crie um objeto de Informações de Conexão de Banco de Dados usando o cmdlet `New-AzDmsConnInfo`. Esse cmdlet espera os seguintes parâmetros:
- *ServerType*. O tipo de conexão de banco de dados solicitado, por exemplo, SQL, Oracle ou MySQL. Use o SQL para SQL Server e SQL do Azure.
- *DataSource*. O nome ou IP de uma instância do SQL Server ou banco de dados SQL do Azure.
- *AuthType*. O tipo de autenticação da conexão, que pode ser SqlAuthentication ou WindowsAuthentication.
- O parâmetro *TrustServerCertificate* define um valor que indica se o canal é criptografado durante o bypass percorrendo a cadeia de certificados para validar a relação de confiança. O valor pode ser verdadeiro ou falso.

O exemplo a seguir cria o objeto Connection Info para SQL Server de origem chamado MySourceSQLServer usando autenticação sql: 

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

O próximo exemplo mostra a criação do Connection Info para um servidor de Instância Gerenciada do Banco de Dados SQL do Azure chamado ‘targetmanagedinstance.database.windows.net’ usando autenticação sql:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fornecer bancos de dados para o projeto de migração
Crie uma lista de objetos `AzDataMigrationDatabaseInfo` que especifica os bancos de dados como parte do projeto da Migração de Banco de Dados do Azure que podem ser fornecidos como parâmetro para a criação do projeto. O Cmdlet `New-AzDataMigrationDatabaseInfo` pode ser usado para criar AzDataMigrationDatabaseInfo. 

O exemplo a seguir cria o `AzDataMigrationDatabaseInfo` projeto para o banco de dados **AdventureWorks** e adiciona-o à lista a ser fornecida como parâmetro para a criação do projeto.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Criar um objeto de projeto
Por fim, crie o projeto de Migração de Banco de Dados do Azure chamado *MyDMSProject* localizado no *Leste dos EUA* usando `New-AzDataMigrationProject` e adicionando as conexões de origem e destino criadas anteriormente e a lista de bancos de dados a serem migrados.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Criar e iniciar uma tarefa de migração
Por fim, crie e inicie a tarefa de Migração de Banco de Dados do Azure. A tarefa de Migração de Banco de Dados do Azure exige informações de credenciais da conexão para a origem e o destino e a lista de tabelas de banco de dados a serem migrados, além das informações já fornecidas com o projeto criado como um pré-requisito. 

### <a name="create-credential-parameters-for-source-and-target"></a>Criar parâmetros de credenciais para a origem e o destino
As credenciais de segurança da conexão podem ser criadas como o objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0). 

O exemplo a seguir mostra a criação de objetos *PSCredential* para fornecer para as conexões de origem e destino fornecendo senhas como variáveis de cadeia de caracteres *$sourcePassword* e *$ targetPassword*. 

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-backup-fileshare-object"></a>Criar objeto fileshare de backup
Agora crie o objeto de compartilhamento de arquivos que representa o compartilhamento de rede SMB local que o serviço de migração de banco de dados do Azure pode levar a fonte de backups de banco de dados usando o cmdlet New-AzDmsFileShare.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Criar objeto de banco de dados selecionado
A próxima etapa é selecionar os bancos de dados de origem e de destino usando o cmdlet New-AzDmsSelectedDB, conforme mostrado no exemplo a seguir:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

### <a name="sas-uri-for-azure-storage-container"></a>URI de SAS para Contêiner do Armazenamento do Microsoft Azure
Crie uma variável contendo o URI de SAS que fornece ao Serviço de Migração de Banco de Dados do Azure o acesso ao contêiner da conta de armazenamento para o qual o serviço faz upload dos arquivos de backup.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="select-logins"></a>Selecionar logons
Crie uma lista de logons a serem migrados, conforme mostrado no exemplo abaixo:  Observe que, atualmente, o DMS dá suporte apenas para migração de logons do SQL. 

```powershell
$selectedLogins = @("user1", "user2")
```

### <a name="select-agent-jobs"></a>Selecionar trabalhos do agente
Crie uma lista de trabalhos do agente a serem migradas, conforme mostrado no exemplo abaixo:

>[!NOTE]
>Atualmente, o DMS dá a trabalhos apenas com as etapas de trabalho do subsistema T-SQL.

```powershell
$selectedAgentJobs = @("agentJob1", "agentJob2")
```

### <a name="create-and-start-a-migration-task"></a>Criar e iniciar uma tarefa de migração

Use o cmdlet `New-AzDataMigrationTask` para criar e iniciar uma tarefa de migração. Esse cmdlet espera os seguintes parâmetros:
- *TaskType*. O tipo de tarefa de migração a ser criada para o SQL Server para o tipo de migração da Instância Gerenciada do Banco de Dados SQL do Azure *MigrateSqlServerSqlDbMi* é esperado. 
- *Nome do Grupo de Recursos*. Nome do grupo de recursos do Azure no qual a tarefa será criada.
- *ServiceName*. Instância do Serviço de Migração de Banco de Dados do Azure na qual a tarefa será criada.
- *ProjectName*. Nome do projeto do Serviço de Migração de Banco de Dados do Azure no qual criar a tarefa. 
- *TaskName*. Nome da tarefa a ser criada. 
- *SourceConnection*. Objeto AzDmsConnInfo que representa a conexão do SQL Server de origem.
- *TargetConnection*. Objeto AzDmsConnInfo que representa a conexão de banco de dados instância gerenciada do SQL de destino.
- *SourceCred*. Objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) para se conectar ao servidor de origem.
- *TargetCred*. Objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) para se conectar ao servidor de destino.
- *SelectedDatabase*. Objeto AzDataMigrationSelectedDB que representa o mapeamento de banco de dados de origem e destino.
- *BackupFileShare*. Objeto FileShare que representa o compartilhamento de rede local para o qual o Serviço de Migração de Banco de Dados do Azure pode fazer backups de banco de dados de origem.
- *BackupBlobSasUri*. O URI de SAS que fornece ao Serviço de Migração de Banco de Dados do Azure acesso ao contêiner da conta de armazenamento para o qual o serviço faz upload dos arquivos de backup. Saiba como obter o URI de SAS para o contêiner de blob.
- *SelectedLogins*. Lista de logons selecionados para migrar.
- *SelectedAgentJobs*. Lista de trabalhos do agente selecionados para migrar.

O seguinte exemplo cria e inicia uma tarefa de migração chamada myDMSTask:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

## <a name="monitor-the-migration"></a>Monitorar a migração
Monitore a tarefa de migração em execução consultando a propriedade de estado da tarefa, conforme mostrado no seguinte exemplo:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>Excluir a instância do DMS
Quando a migração é concluída, você pode excluir a instância do DMS do Azure:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-steps"></a>Próximas etapas
- Verificar as diretrizes de migração no [Guia de Migração de Banco de Dados da Microsoft](https://datamigration.microsoft.com/).
