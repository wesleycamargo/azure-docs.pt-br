---
title: 'Serviço de Backup do Azure: Fazer backup e restaurar bancos de dados SQL em VMs do Azure usando o Backup do Azure e o PowerShell'
description: Fazer backup e restaurar bancos de dados SQL em VMs do Azure usando o Backup do Azure e o PowerShell.
services: backup
author: pvrk
manager: vijayts
keywords: Azure Backup; SQL;
ms.service: backup
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pullabhk
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 3a424335a1e7d7775f6be0980e7009669e354ea7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717912"
---
# <a name="back-up-and-restore-sql-databases-in-azure--vms-with-powershell"></a>Fazer backup e restaurar bancos de dados SQL em VMs do Azure com o PowerShell

Este artigo descreve como usar o Azure PowerShell para fazer backup e recuperar um banco de dados SQL dentro de uma VM do Azure usando [Backup do Azure](backup-overview.md) o cofre dos serviços de recuperação.

Este tutorial explica como:

> [!div class="checklist"]
> * Configurar o PowerShell e registrar o provedor de serviços de recuperação do Azure.
> * Crie um cofre dos Serviços de Recuperação.
> * Configure o backup para o banco de dados SQL dentro de uma VM do Azure.
> * Execute um trabalho de backup.
> * Restaure um backup de banco de dados SQL.
> * Monitorar o backup e restaurar trabalhos.

## <a name="before-you-start"></a>Antes de começar

* [Saiba mais](backup-azure-recovery-services-vault-overview.md) sobre cofres dos serviços de recuperação.
* Leia sobre os recursos do recurso para [fazendo backup de bancos de dados SQL nas VMs do Azure](backup-azure-sql-database.md#before-you-start).
* Examine a hierarquia de objetos do PowerShell dos serviços de recuperação.

### <a name="recovery-services-object-hierarchy"></a>Hierarquia de objetos dos Serviços de Recuperação

A hierarquia de objetos é resumida no diagrama a seguir.

![Hierarquia de objetos dos Serviços de Recuperação](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Examine os **Az.RecoveryServices** [referência de cmdlet](/powershell/module/az.recoveryservices) referência na biblioteca do Azure.

### <a name="set-up-and-install"></a>Configurar e instalar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Configure o PowerShell da seguinte maneira:

1. [Baixe a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps). A versão mínima necessária é 1.5.0.

2. Localize os cmdlets do PowerShell do Backup do Azure com este comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Revise os aliases e cmdlets para Backup do Azure e o cofre dos serviços de recuperação. Aqui está um exemplo de como você pode ver. Não é uma lista completa dos cmdlets.

    ![Listar cmdlets dos Serviços de Recuperação](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Entrar em sua conta do Azure com **Connect-AzAccount**.
5. Na página da web que aparece, você será solicitado a inserir suas credenciais de conta.

    * Como alternativa, você pode incluir suas credenciais de conta como um parâmetro na **Connect-AzAccount** cmdlet com **-credencial**.
    * Se você for um parceiro CSP trabalhando para um locatário, especifique o cliente como um locatário, usando seu nome de domínio primário tenantID ou locatário. Um exemplo é **Connect-AzAccount -Tenant** fabrikam.com.

6. Associe a assinatura que você deseja usar com a conta, pois uma conta pode ter várias assinaturas.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Se você estiver usando um Backup do Azure pela primeira vez, use o cmdlet **Register-AzResourceProvider** para registrar o provedor dos Serviços de Recuperação do Azure com sua assinatura.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verifique se os provedores foram registrados com êxito:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Na saída do comando, verifique **RegistrationState** alterações **registrado**. Se não, execute as **Register-AzResourceProvider** cmdlet novamente.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Siga estas etapas para criar um cofre dos Serviços de Recuperação:

O cofre dos Serviços de Recuperação é um recurso do Resource Manager e, portanto, você precisará colocá-lo em um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um grupo de recursos com o cmdlet **New-AzResourceGroup**. Ao criar um grupo de recursos, especifique o nome e o local.

1. Um cofre é colocado em um grupo de recursos. Se você não tiver um recurso existente do grupo, crie um novo com o [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). Neste exemplo, podemos criar um novo grupo de recursos na região Oeste dos EUA.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Use o [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet para criar o cofre. Especifique o mesmo local para o cofre usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Especifique o tipo de redundância para usar para o armazenamento de cofre.

    * Você pode usar [armazenamento com redundância local](../storage/common/storage-redundancy-lrs.md) ou [armazenamento com redundância geográfica](../storage/common/storage-redundancy-grs.md).
    * O exemplo a seguir define o **- BackupStorageRedundancy** opção para o[AzRecoveryServicesBackupProperties conjunto](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperties?view=azps-1.4.0) cmd para **testvault** definido como  **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Exibir os cofres em uma assinatura

Para exibir todos os cofres da assinatura, use [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

A saída é semelhante ao seguinte. O grupo de recursos associado e o local são fornecidos.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Definir o contexto do cofre

Store o objeto de cofre em uma variável e defina o contexto do cofre.

* Muitos cmdlets do Backup do Azure exigem o objeto de Cofre de serviços de recuperação como entrada, portanto, é conveniente armazenar o objeto de cofre em uma variável.
* O contexto do cofre é o tipo de dados protegido no cofre. Defini-lo com [AzRecoveryServicesVaultContext conjunto](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Depois que o contexto é definido, ele se aplica a todos os cmdlets subsequentes.

O exemplo a seguir define o contexto de cofre de **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Buscar a ID do cofre

Planejamos reprovando o contexto de cofre, configuração de acordo com as diretrizes do PowerShell do Azure. Em vez disso, você pode armazenar ou buscar a ID do cofre e passá-lo para comandos relevantes, da seguinte maneira:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Configurar uma política de backup

Uma política de backup Especifica a agenda de backups e por quanto tempo os pontos de recuperação de backup devem ser mantidos:

* Uma política de backup está associada a pelo menos uma política de retenção. Uma política de retenção define por quanto tempo um ponto de recuperação é mantido até ser excluído.
* Modo de exibição a retenção de política de backup padrão usando [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Exibir o agendamento de política de backup padrão usando [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Você usa o [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) para criar uma nova política de backup. Você insere os objetos de política de retenção e agendamento.

O exemplo a seguir armazena a política de agendamento e a política de retenção em variáveis. Em seguida, ele usa essas variáveis como parâmetros para uma nova política (**NewSQLPolicy**). **NewSQLPolicy** usa o diário "completo" backup, retém por 180 dias e usa um backup de log a cada 2 horas

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A saída é semelhante ao seguinte.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 9:00:00 PM      Daily                                    False                True
```

## <a name="enable-backup"></a>Habilitar o backup

### <a name="registering-the-sql-vm"></a>Registrar a VM do SQL

Para backups de VM do Azure e compartilhamentos de arquivos do Azure, o serviço de Backup pode se conectar a esses recursos do Azure Resource Manager e buscar os detalhes relevantes. Como o SQL é um aplicativo dentro de uma VM do Azure, o serviço de Backup precisa de permissão para acessar o aplicativo e buscar os detalhes necessários. Para fazer isso, você precisa *'Registrar'* a VM do Azure que contém o aplicativo de SQL com um cofre dos serviços de recuperação. Depois que você registrar uma VM do SQL com um cofre, você pode proteger os bancos de dados SQL somente nesse cofre. Use [Register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) cmdlet do PS para registrar a VM.

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

O comando retornará um 'contêiner de backup' deste recurso e o status será 'registrado'

> [!NOTE]
> Se o parâmetro force não for fornecido, o usuário é solicitado a confirmar com um texto 'você deseja desabilitar a proteção para este contêiner'. Por favor, ignore esse texto e dizer "Y" para confirmar. Esse é um problema conhecido e estamos trabalhando para remover o texto e o requisito para o parâmetro force

### <a name="fetching-sql-dbs"></a>Buscando os bancos de dados SQL

Depois que o registro for concluído, o serviço de Backup poderão listar todos os componentes do SQL disponíveis dentro da VM. Para exibir todos os componentes SQL ainda será feito backup para esse uso do cofre [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) cmdlet do PS

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

A saída mostrará todos os componentes do SQL sem proteção em todas as VMs do SQL registrado neste cofre com o tipo de Item e o nome do servidor. Você pode filtrar ainda mais a uma VM de SQL específica, passando o '-contêiner ' parâmetro ou use a combinação de 'Name' e 'ServerName', juntamente com ItemType sinalizador para chegar a um item exclusivo do SQL.

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>Configurar o backup

Agora que temos o BD SQL necessárias e a política com a qual ele precisa ser feito, podemos usar o [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) cmdlet para configurar o backup para este banco de dados SQL.

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

O comando aguarda até que a configurar o backup for concluído e retorna a saída a seguir.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Busca de novos bancos de dados SQL

Depois que o computador está registrado, o serviço de Backup buscará os detalhes de bancos de dados, em seguida, disponíveis. Se o usuário adiciona instâncias de bancos de dados SQL/SQL para a máquina registrada mais tarde, é necessário disparar manualmente o serviço de backup para executar uma nova consulta para obter todos os desprotegidos bancos de dados (incluindo aqueles adicionados recentemente) novamente. Use o [Initialize AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet na VM do SQL para executar uma consulta nova. O comando aguarda até que a operação seja concluída. Usar mais tarde a [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) cmdlet do PS para obter a lista mais recente desprotegidos dos componentes do SQL

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

Depois que os itens que podem ser protegidos relevantes são buscados, habilitar os backups conforme instruído na [acima da seção](#configuring-backup).
Se não quiser que um manualmente detectar os novos bancos de dados, eles podem optar por autoprotection, conforme explicado [abaixo](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Habilitar AutoProtection

Um usuário pode configurar o backup, de modo que todos os bancos de dados adicionados no futuro serão automaticamente protegidos com uma determinada política. Para habilitar autoprotection, use [Enable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) cmdlet do PS.

Uma vez que a instrução é fazer backup de todos os futuros bancos de dados, a operação é feita em um SQLInstance nível.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $targetPolicy -VaultId $targetvault.ID
```

Depois que a intenção de autoprotection é fornecida, a consulta na máquina para buscar recentemente adicionado bancos de dados ocorre como uma tarefa em segundo plano agendado a cada 8 horas.

## <a name="restore-sql-dbs"></a>Restaurar bancos de dados SQL

O Backup do Azure pode restaurar bancos de dados do SQL Server que são executados em VMs do Azure da seguinte maneira:

1. Restaure para uma determinada data ou hora (para o segundo) por meio de backups de log de transações. O Backup do Azure determina automaticamente o backup diferencial completo apropriado e a cadeia de backups de log que são necessários para restaurar com base no tempo selecionado.
2. Restaure um backup completo ou diferencial específico para restaurar para um ponto de recuperação específico.

Verificar pré-requisitos mencionados [aqui](restore-sql-database-azure-vm.md#prerequisites) antes de restaurar os bancos de dados SQL.

Buscar relevante primeiro backup de banco de dados SQL usando o [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) cmdlet do PS.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>O tempo de restauração relevantes de busca

Conforme descrito acima, o usuário pode restaurar o banco de dados do SQL backup para uma cópia completo/diferencial **ou** para um log point-in-time.

#### <a name="fetch-distinct-recovery-points"></a>Buscar os pontos de recuperação distintos

Use [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) para buscar os pontos de recuperação (completo/diferencial) distintos para um banco de dados do SQL de backup.

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = Get-Date.ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

A saída é semelhante ao exemplo a seguir

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

Use o 'RecoveryPointId' ou um filtro de matriz para buscar o ponto de recuperação relevante.

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>Buscar o ponto de recuperação point-in-time

Se o usuário deseja restaurar o banco de dados para um determinado point-in-time, use [Get-AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) cmdlet do PS. O cmdlet retorna uma lista de datas que representam as horas de início e término de uma cadeia contínua de logs ininterrupta para aquele item de backup do SQL. O point-in-time desejado deve ser dentro desse intervalo.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

A saída será semelhante ao exemplo a seguir.

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

A saída acima significa que o usuário pode restaurar para qualquer ponto no tempo entre a hora de início exibida e a hora de término. Os horários estão em UTC. Construa qualquer point-in-time no PS está dentro do intervalo mostrado acima.

> [!NOTE]
> Quando um log point-in-time selecionado para restauração, o usuário não precisa especificar o backup completo do qual o banco de dados é restaurado, ou seja, o ponto de partida. O serviço de Backup do Azure cuidará do plano de recuperação inteira, ou seja, que completa o backup para escolher o que backups de log para aplicar etc.

### <a name="determine-recovery-configuration"></a>Determinar a configuração de recuperação

No caso de restauração de banco de dados SQL, há suporte para os seguintes cenários de restauração.

1. Substituindo o banco de dados do SQL backup com os dados de outro ponto de recuperação - OriginalWorkloadRestore
2. Restaurando o banco de dados SQL como um novo banco de dados na mesma instância do SQL - AlternateWorkloadRestore
3. Restaurando o banco de dados SQL como um novo banco de dados em outra instância do SQL em outra VM do SQL - AlternateWorkloadRestore

Após buscar o ponto de recuperação relevante (distintos ou de log point-in-time), use [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) cmdlet do PS para buscar o objeto de configuração de recuperação, de acordo com o plano de recuperação desejado.

#### <a name="original-workload-restore"></a>Restauração de carga de trabalho original

Para substituir o banco de dados de backup com dados do ponto de recuperação, basta Especifica o sinalizador à direita e o ponto de recuperação relevantes conforme mostrado nos exemplos a seguir.

##### <a name="original-restore-with-distinct-recovery-point"></a>Restauração original com o ponto de recuperação distinto

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>Restauração original com o log point-in-time

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Restauração de carga de trabalho alternativo

> [!IMPORTANT]
> Backup de um backup de banco de dados SQL pode ser restaurado como um novo banco de dados para outro SQLInstance somente, em uma VM do Azure registrado nesse cofre.

Conforme descrito acima, se o destino SQLInstance reside dentro de outra VM do Azure, verifique se ele está [registrado neste cofre](#registering-the-sql-vm) e o SQLInstance relevante é exibido como um item que podem ser protegido.

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

Em seguida, basta passe o ponto de recuperação relevante, a instância SQL de destino com o sinalizador de direito como mostrado abaixo.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Restauração alternativa com o ponto de recuperação distinto

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>Restauração alternativa com o log point-in-time

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

O objeto de configuração de ponto final de recuperação obtido [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) cmdlet do PS tem todas as informações relevantes para a restauração e é conforme mostrado abaixo.

````powershell
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

Você pode editar os campos de nome, OverwriteWLIfpresent, NoRecoveryMode e targetPhysicalPath de banco de dados restaurados. Obter mais detalhes sobre os caminhos de arquivo de destino conforme mostrado abaixo.

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

Defina as propriedades relevantes do PS como valores de cadeia de caracteres, conforme mostrado abaixo.

````powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl

TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

> [!IMPORTANT]
> Certifique-se de que o objeto de configuração final de recuperação tem todos os valores necessários e adequados, pois a operação de restauração se baseará no objeto de configuração.

### <a name="restore-with-relevant-configuration"></a>Restaurar com configuração relevante

Depois que o objeto de configuração de recuperação relevante é obtido e verificado, usar o [AzRecoveryServicesBackupItem restauração](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) cmdlet do PS para iniciar o processo de restauração.

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

A operação de restauração retorna um trabalho a ser rastreado.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>Gerenciar backups do SQL

### <a name="on-demand-backup"></a>Backup sob demanda

Depois que o backup tiver sido habilitado para um banco de dados, o usuário também pode disparar um backup sob demanda para o banco de dados usando [AzRecoveryServicesBackupItem Backup](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) cmdlet do PS. O exemplo a seguir dispara um backup completo em um banco de dados SQL com compactação habilitada e o backup completo deve ser mantido por 60 dias.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

O comando de backup ad hoc retorna um trabalho a ser rastreado.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

Se a saída for perdida ou se você quiser obter a ID de trabalho relevantes, [obter a lista de trabalhos](#track-azure-backup-jobs) do Backup do Azure do serviço e, em seguida, controlar a ele e seus detalhes.

### <a name="change-policy-for-backup-items"></a>Alterar a política para itens de backup

Usuário pode modificar a política existente ou alterar a política do item de backup de Policy1 para política2. Para alternar as políticas para um item de backup, simplesmente buscar a diretiva relevante e fazer backup de item e usar o [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) comando com itens de backup como o parâmetro.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

O comando aguarda até que a configurar o backup for concluído e retorna a saída a seguir.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Registrar novamente as VMs do SQL

> [!WARNING]
> Certifique-se de ler este [documento](backup-sql-server-azure-troubleshoot.md#re-registration-failures) para compreender os sintomas de falha e faz com que antes de tentar a reinscrição

Para disparar o novo registro de VM do SQL, buscar o contêiner de backup relevante e passá-lo para o cmdlet de registro.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
````

### <a name="stop-protection"></a>Parar a proteção

#### <a name="retain-data"></a>Reter dados

Se o usuário deseja interromper a proteção, eles podem usar o [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) cmdlet do PS. Isso interromperá os backups agendados, mas os dados submetidos a backup até agora são mantidos para sempre.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Excluir dados de backup

Para remover completamente os dados de backup armazenados no cofre, basta adicionar '-Sinalizador/alternar dos RemoveRecoveryPoints para o ['disable' comando proteção](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>Desativar proteção automática

Se autoprotection foi configurado em um SQLInstance, o usuário pode desativá-lo usando o [Disable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) cmdlet do PS.

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>Cancelar o registro de VM do SQL

Se todos os bancos de dados de um SQL server [não são mais os dados protegidos e de backup não existem](#delete-backup-data), usuário pode cancelar o registro de VM do SQL neste cofre. Somente, em seguida, o usuário pode proteger bancos de dados para outro cofre. Use [Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) cmdlet do PS para cancelar o registro de VM do SQL.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>Acompanhar os trabalhos de Backup do Azure

É importante observar que o Backup do Azure apenas rastreia os trabalhos de usuário disparado no backup do SQL. Backups agendados (incluindo backups de log) não são visíveis no portal/powershell. No entanto, se houver agendada trabalhos falharem, uma [alerta de backup](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) é gerado e exibido no portal. [Usar o Azure Monitor](backup-azure-monitoring-use-azuremonitor.md) para acompanhar todos os trabalhos agendados e outras informações relevantes.

Os usuários podem controlar as operações do ad hoc/usuário disparado com a JobID que é retornado na [saída](#on-demand-backup) de trabalhos assíncronos, como backup. Use [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetails?view=azps-1.5.0) cmdlet do PS para acompanhar o trabalho e seus detalhes.

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

Para obter a lista de trabalhos de ad hoc e seus status do serviço de Backup do Azure, use [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) cmdlet do PS. O exemplo a seguir retorna todos os trabalhos SQL em andamento.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Para cancelar um trabalho em andamento, use o [Stop-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) cmdlet do PS.

## <a name="managing-sql-always-on-availability-groups"></a>Gerenciando grupos de disponibilidade AlwaysOn do SQL

Para SQL grupos de disponibilidade AlwaysOn, certifique-se [registrar todos os nós](#registering-the-sql-vm) do grupo de disponibilidade (AG). Depois que o registro é feito para todos os nós, um objeto de grupo de disponibilidade do SQL foi criado logicamente em itens que podem ser protegidos. Os bancos de dados sob o grupo de disponibilidade do SQL serão listados como 'SQLDatabase'. Os nós serão mostrados como instâncias autônomas e os bancos de dados padrão SQL sob eles serão listados como bancos de dados SQL.

Por exemplo, vamos supor que um grupo de disponibilidade do SQL tem dois nós: 'sql-server-0' e 'sql-server-1' e 1 grupo de disponibilidade de SQL DB. Depois de registrar esses dois nós, se usuário [lista os itens que podem ser protegidos](#fetching-sql-dbs), ele lista os componentes a seguir

1. Um objeto de grupo de disponibilidade do SQL - que podem ser protegido tipo como SQLAvailabilityGroup de item
2. Um grupo de disponibilidade do SQL DB - tipo de item que podem ser protegidos como SQLDatabase
3. tipo de item de proteção de SQL-server-0 - como SQLInstance
4. tipo de item de proteção de SQL-server-1 - como SQLInstance
5. Tipo de item de proteção de qualquer padrão de bancos de dados SQL (mestre, modelo, msdb) em sql-server-0 - como SQLDatabase
6. Tipo de item de proteção de qualquer padrão de bancos de dados SQL (mestre, modelo, msdb) em sql-server-1 - como SQLDatabase

SQL-server-0, 1 do sql server também será listado como "AzureVMAppContainer" quando [contêineres de backup são listados](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0).

Buscar apenas o banco de dados SQL relevante para [habilitar o backup](#configuring-backup) e o [backup ad hoc](#on-demand-backup) e [restaurar cmdlets do PS](#restore-sql-dbs) são idênticos.
