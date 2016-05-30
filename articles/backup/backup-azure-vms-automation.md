<properties
   pageTitle="Implantar e gerenciar backup para VMs do ARM usando o PowerShell | Microsoft Azure"
   description="Use o PowerShell para implantar e gerenciar backups no Azure para VMs do ARM"
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="05/09/2016"
   ms.author="markgal; trinadhk"/>

# Implantar e gerenciar o backup de VMs do ARM usando o PowerShell

> [AZURE.SELECTOR]
- [PowerShell do Resource Manager](backup-azure-vms-automation.md)
- [PowerShell Clássico](backup-azure-vms-classic-automation.md)

Este artigo mostra como usar o Azure PowerShell para fazer backup e recuperar uma VM (Máquina Virtual) do Azure de um cofre dos Serviços de Recuperação. Um cofre dos Serviços de Recuperação é um recurso do ARM (Azure Resource Manager) usado para proteger dados e ativos nos serviços de Backup do Azure e do Azure Site Recovery. Use um cofre dos Serviços de Recuperação ao trabalhar em uma implantação do ARM. Você pode usar um cofre dos Serviços de Recuperação para proteger as VMs implantadas no ASM (Azure Service Manager), bem como VMs do ARM.

>[AZURE.NOTE] O Azure tem dois modelos de implantação para criar e trabalhar com recursos: [Resource Manager e Clássico](../resource-manager-deployment-model.md). Este artigo destina-se a VMs criadas usando o modelo do Resource Manager.

Ele guiará você sobre como usar o PowerShell para proteger uma VM e como restaurar dados de um ponto de recuperação.

## Conceitos

Se você não estiver familiarizado com o serviço de Backup do Azure, para ter uma visão geral do serviço, confira [O que é o Backup do Azure?](backup-introduction-to-azure-backup.md) Antes de começar, é importante ter noções básicas sobre os pré-requisitos necessários para trabalhar com o Backup do Azure e as limitações da atual solução de backup de VM.

Para usar efetivamente o PowerShell, é necessário compreender a hierarquia de objetos e de onde começar.

![Hierarquia de objetos dos Serviços de Recuperação](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## Configuração e registro

Para começar:

1. [Baixe a versão mais recente do PowerShell](https://github.com/Azure/azure-powershell/releases) (a versão mínima necessária é: 1.0.0)

2. Localize os cmdlets do PowerShell do Backup do Azure disponíveis digitando o seguinte comando:

```
PS C:\WINDOWS\system32> Get-Command *azurermrecoveryservices*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.0.7      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.0.7      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.0.7      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.0.7      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.0.7      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.0.7      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.0.7      AzureRM.RecoveryServices
Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.0.0      AzureRM.RecoveryServices.Backup
```


As seguintes tarefas podem ser automatizadas com o PowerShell:

- Criar um cofre dos Serviços de Recuperação
- Fazer backup ou proteger VMs do Azure
- Disparar um trabalho de backup
- Monitorar um trabalho de backup
- Restaurar uma VM do Azure

## Criar um cofre dos Serviços de Recuperação

> [AZURE.TIP] Para clientes que usarão o Backup do Azure pela primeira vez, será necessário registrar o provedor do Serviço de Recuperação do Azure para ser usado com sua assinatura. Isso pode ser feito com a execução do seguinte comando: Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"

Você pode criar um novo cofre dos Serviços de Recuperação usando o cmdlet **New-AzureRmRecoveryServicesVault**. O cofre dos Serviços de Recuperação é um recurso do ARM e, portanto, você precisará colocá-lo em um Grupo de Recursos. Em um console do Azure PowerShell com privilégios elevados, execute os seguintes comandos:

```
PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
```

Para exibir uma lista de todos os cofres dos Serviços de Recuperação em uma assinatura, use o cmdlet **Get-AzureRmRecoveryServicesVault**.

### Definir redundância de armazenamento
Ao criar um cofre dos Serviços de Recuperação, especifique o tipo de redundância de armazenamento a ser usada, LRS (Armazenamento com Redundância Local) ou GRS (Armazenamento com Redundância Geográfica). O exemplo a seguir mostra que a opção BackupStorageRedundancy para o testVault está definida como GeoRedundant.

```
PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```


> [AZURE.TIP] Muitos cmdlets do Backup do Azure exigem o objeto do cofre dos Serviços de Recuperação como entrada. Por esse motivo, pode ser útil armazenar o objeto do cofre dos Serviços de Recuperação de backup em uma variável.


## Fazer backup das VMs do Azure

Antes de habilitar a proteção em uma VM, você deve definir o contexto do cofre. O contexto é aplicado a todos os cmdlets subsequentes.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name testvault | Set-AzureRmRecoveryServicesVaultContext
```

### Crie uma política de proteção

Quando você cria um novo cofre, ele vem com uma política padrão. Essa política dispara um trabalho de backup diariamente às 9:30h. O instantâneo de backup é mantido por 30 dias. Você pode usar a política padrão para proteger rapidamente sua VM e editá-la posteriormente com detalhes diferentes.

Para exibir a lista de políticas do cofre, use o cmdlet Get-AzureRmRecoveryServicesBackupProtectionPolicy:

```
PS C:\WINDOWS\system32> get-AzureRMRecoveryServicesBackupProtectionPolicy -WorkloadType AzureVM
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [AZURE.NOTE] O fuso horário do campo BackupTime no PowerShell é UTC. No entanto, quando o tempo de backup é mostrado no Portal do Azure, o horário é ajustado para seu fuso horário local.

Uma política de proteção de backup está associada a pelo menos uma política de retenção. A política de retenção define por quanto tempo um ponto de recuperação é mantido pelo Backup do Azure. Use Get-AzureRmRecoveryServicesBackupRetentionPolicyObject para exibir a política de retenção padrão. Da mesma forma, você pode obter a política de agendamento padrão usando Get-AzureRmRecoveryServicesBackupSchedulePolicyObject. Os objetos de política de retenção e agendamento são usados como entradas para o cmdlet New-AzureRmRecoveryServicesBackupProtectionPolicy.

Uma política de proteção de backup define quando e com que frequência será feito o backup de um item. O cmdlet New-AzureRmRecoveryServicesBackupProtectionPolicy cria um objeto do PowerShell que mantém as informações da política de backup. A política de backup é usada como entrada para o cmdlet Enable-AzureRmRecoveryServicesBackupProtection.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\>  $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\>  New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType AzureVM -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### Habilitar proteção

Habilitar a proteção envolve dois objetos, o item e a política. Ambos os objetos são necessários para habilitar a proteção no cofre. Depois de a política ter sido associada ao item, o fluxo de trabalho de backup será disparado no momento definido no agendamento da política.

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Para VMs baseadas no ASM

```
PS C:\>  $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\>  Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### Modificar uma política de proteção

Para alterar a política, modifique o objeto BackupSchedulePolicyObject ou BackupRetentionPolicy e a política usando Set-AzureRmRecoveryServicesBackupProtectionPolicy

O exemplo a seguir altera a contagem de retenção para 365.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRMRecoveryServicesBackupProtectionPolicy -Name NewPolicy
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy  $RetPol
```

## Executar um backup inicial

O agendamento de backup dispara um backup completo no backup inicial para o item. Os backups posteriores serão apenas uma cópia incremental. Se você desejar forçar que o backup inicial ocorra em um dado momento ou até mesmo imediatamente, use o cmdlet Backup-AzureRmRecoveryServicesBackupItem:

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "V2VM";
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM";
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item;
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Backup               InProgress            4/23/2016 5:00:30 PM            cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [AZURE.NOTE: o fuso horário dos campos StartTime e EndTime no PowerShell é UTC. No entanto, quando a hora é exibida no Portal do Azure, ela é ajustada para seu fuso horário local.

## Monitoramento de um trabalho de backup

A maioria das operações de longa duração no Backup do Azure são modeladas como um trabalho. Isso facilita acompanhar o andamento sem a necessidade de manter o portal do Azure aberto em todos os momentos.

Para obter o status mais recente de um trabalho em andamento, use o cmdlet Get-AzureRMRecoveryservicesBackupJob.

```
PS C:\ > $joblist = Get-AzureRMRecoveryservicesBackupJob –Status InProgress
PS C:\ > $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Em vez de sondar esses trabalhos para conclusão (o que é um código adicional desnecessário), é mais simples usar o cmdlet Wait-AzureRmRecoveryServicesBackupJob. Quando usado em um script, o cmdlet fará uma pausa na execução até que o trabalho seja concluído ou o valor de tempo limite especificado seja atingido.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## Restaurar uma VM do Azure

Para restaurar dados de backup, é necessário identificar o Item de backup e o ponto de recuperação que mantém os dados pontuais. Essas informações são fornecidas para o cmdlet Restore-AzureRMRecoveryServicesBackupItem para iniciar uma restauração dos dados do cofre para a conta do cliente.

### Selecione a VM

Para obter o objeto do PowerShell que identifica o item correto de backup, comece do contêiner no cofre e desça progressivamente na hierarquia de objetos. Para selecionar o contêiner que representa a VM, use o cmdlet Get-AzureRmRecoveryServicesBackupContainer e canalize-o para o cmdlet indique o cmdlet Get-AzureRmRecoveryServicesBackupItem.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType AzureVM –Status Registered -Name 'V2VM'
PS C:\> $backupitem=Get-AzureRmRecoveryServicesBackupItem –Container $namedContainer  –WorkloadType "AzureVM"
```

### Escolha um ponto de recuperação

Agora você pode listar todos os pontos de recuperação para o item de backup usando o cmdlet Get-AzureRMRecoveryServicesBackupRecoveryPoint e escolher o ponto de recuperação a ser restaurado. Normalmente, os usuários escolhem o ponto AppConsistent mais recente na lista.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRMRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM
                              /recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

A variável $rp é uma matriz de pontos de recuperação para o item de backup selecionado, classificados na ordem inversa de tempo - o último ponto de recuperação está no índice 0. Use a indexação de matriz padrão do PowerShell para selecionar o ponto de recuperação. Por exemplo: $rp[0] selecionará o último ponto de recuperação.

### Restaurando discos

Há uma diferença importante entre as operações de restauração feitas por meio do portal do Azure e por meio do Azure PowerShell. Com o PowerShell, a operação de restauração para na restauração de discos e nas informações de configuração do ponto de recuperação. Ele não cria uma máquina virtual.

> [AZURE.WARNING] O Restore-AzureRMRecoveryServicesBackupItem não cria uma VM, apenas restaura os discos para a conta de armazenamento especificada. Isso é diferente do que ocorre no Portal do Azure.

```
PS C:\> $restorejob = Restore-AzureRMRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName DestAccount
 -StorageAccountResourceGroupName DestRG
PS C:\> $restorejob
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Restore               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Você pode obter os detalhes da operação de restauração usando o cmdlet Get-AzureRmRecoveryServicesBackupJobDetails depois que o trabalho de Restauração for concluído. A propriedade JobDetails terá as informações necessárias para recompilar a VM.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails
```

## Registrar o Windows Server ou o DPM para um Cofre dos Serviços de Recuperação

Depois de criar o cofre dos Serviços de Recuperação, baixe o agente mais recente e as credenciais do cofre e armazene-os em um local conveniente como C:\\Downloads.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

No Windows Server ou servidor DPM, execute o cmdlet [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) para registrar o computador com o cofre.

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

<!---HONumber=AcomDC_0518_2016-->