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
   ms.date="05/24/2016"
   ms.author="markgal; trinadhk"/>

# Implantar e gerenciar o backup de VMs do ARM usando o PowerShell

> [AZURE.SELECTOR]
- [ARM](backup-azure-vms-automation.md)
- [Clássico](backup-azure-vms-classic-automation.md)

Este artigo mostra como usar os cmdlets do Azure PowerShell para fazer backup e recuperar uma VM (máquina virtual) do Azure de um cofre dos Serviços de Recuperação. Um cofre dos Serviços de Recuperação é um recurso do ARM (Azure Resource Manager) usado para proteger dados e ativos nos serviços de Backup do Azure e do Azure Site Recovery. Use um cofre dos Serviços de Recuperação ao trabalhar em uma implantação do ARM. Você pode usar um cofre dos Serviços de Recuperação para proteger as VMs implantadas no ASM (Azure Service Manager), bem como VMs do ARM.

>[AZURE.NOTE] O Azure tem dois modelos de implantação para criar e trabalhar com recursos: [Resource Manager e Clássico](../resource-manager-deployment-model.md). Este artigo destina-se a VMs criadas usando o modelo do Resource Manager.

Ele guiará você sobre como usar o PowerShell para proteger uma VM e como restaurar dados de um ponto de recuperação.

## Conceitos

Se você não estiver familiarizado com o serviço de Backup do Azure, para ter uma visão geral do serviço, confira [O que é o Backup do Azure?](backup-introduction-to-azure-backup.md) Antes de começar, é importante ter noções básicas sobre os pré-requisitos necessários para trabalhar com o Backup do Azure e as limitações da atual solução de backup de VM.

Para usar efetivamente o PowerShell, é necessário compreender a hierarquia de objetos e de onde começar.

![Hierarquia de objetos dos Serviços de Recuperação](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Para exibir a referência do cmdlet AzureRmRecoveryServicesBackup do PowerShell, veja [Backup do Azure - cmdlets dos Serviços de Recuperação](https://msdn.microsoft.com/library/mt723320.aspx) na biblioteca do Azure. Para exibir a referência do cmdlet AzureRmRecoveryServicesVault do PowerShell, veja [Cmdlets dos Serviços de Recuperação](https://msdn.microsoft.com/library/mt643905.aspx).


## Configuração e registro

Para começar:

1. [Baixe a versão mais recente do PowerShell](https://github.com/Azure/azure-powershell/releases) (a versão mínima necessária é: 1.0.0)

2. Localize os cmdlets do PowerShell do Backup do Azure disponíveis digitando o seguinte comando:

```
PS C:\> Get-Command *azurermrecoveryservices*

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
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.1.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.1.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.1.0      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.1.0      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.1.0      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.1.0      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.1.0      AzureRM.RecoveryServices
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

As etapas a seguir orientarão você durante a criação de um cofre dos Serviços de Recuperação. Um cofre dos Serviços de Recuperação é diferente de um cofre de Backup.

1. Se você estiver usando um Backup do Azure pela primeira vez, deverá usar o cmdlet **[Register-AzureRMResourceProvider](https://msdn.microsoft.com/library/mt679020.aspx)** para registrar o provedor do Serviço de Recuperação do Azure com sua assinatura.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. O cofre dos Serviços de Recuperação é um recurso do ARM e, portanto, você precisará colocá-lo em um Grupo de Recursos. Você pode usar um grupo de recursos existente ou criar um novo grupo de recursos com o cmdlet **[New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt678985.aspx)**. Ao criar um novo grupo de recursos, especifique o nome e o local para o grupo de recursos.

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Use o cmdlet **[New-AzureRmRecoveryServicesVault](https://msdn.microsoft.com/library/mt643910.aspx)** para criar o novo cofre. Lembre-se de especificar o mesmo local para o cofre usado para o grupo de recursos.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Especifique o tipo de redundância de armazenamento a ser usado, o [LRS (Armazenamento com Redundância Local)](../storage/storage-redundancy.md#locally-redundant-storage) ou o [GRS (Armazenamento com Redundância Geográfica)](../storage/storage-redundancy.md#geo-redundant-storage). O exemplo a seguir mostra que a opção BackupStorageRedundancy para o testVault está definida como GeoRedundant.

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

    > [AZURE.TIP] Muitos cmdlets do Backup do Azure exigem o objeto do cofre dos Serviços de Recuperação como entrada. Por esse motivo, pode ser útil armazenar o objeto do cofre dos Serviços de Recuperação de backup em uma variável.

## Exibir os cofres em uma assinatura
Use **[Get-AzureRmRecoveryServicesVault](https://msdn.microsoft.com/library/mt643907.aspx)** para exibir a lista de todos os cofres da assinatura atual. Você pode usar esse comando para verificar se um novo cofre foi criado ou para ver quais cofres estão disponíveis na assinatura.

Execute o comando Get-AzureRmRecoveryServicesVault e todos os cofres na assinatura serão listados.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## Fazer backup das VMs do Azure
Agora que você criou um cofre dos serviços de recuperação, poderá usá-lo para proteger uma máquina virtual. No entanto, antes de aplicar a proteção, você deverá definir o contexto de cofre e deverá verificar a política de proteção. O contexto do cofre define o tipo dos dados protegidos no cofre. A política de proteção será a agenda de quando o trabalho de backup for executado e de quanto tempo cada instantâneo de backup será mantido.

Antes de habilitar a proteção em uma VM, você deve definir o contexto do cofre. O contexto é aplicado a todos os cmdlets subsequentes.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name testvault | Set-AzureRmRecoveryServicesVaultContext
```

### Crie uma política de proteção

Quando você cria um novo cofre, ele vem com uma política padrão. Essa política dispara um trabalho de backup diariamente em um horário especificado. De acordo com a política padrão, o instantâneo de backup é mantido por 30 dias. Você pode usar a política padrão para proteger rapidamente sua VM e editá-la posteriormente com detalhes diferentes.

Use **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://msdn.microsoft.com/library/mt723300.aspx)** para exibir a lista de políticas disponível no cofre:

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType AzureVM
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [AZURE.NOTE] O fuso horário do campo BackupTime no PowerShell é UTC. No entanto, quando o tempo de backup é mostrado no Portal do Azure, o horário é ajustado para seu fuso horário local.

Uma política de proteção de backup está associada a pelo menos uma política de retenção. A política de retenção define por quanto tempo um ponto de recuperação é mantido pelo Backup do Azure. Use **Get-AzureRmRecoveryServicesBackupRetentionPolicyObject** para exibir a política de retenção padrão. Da mesma forma, você pode usar **Get-AzureRmRecoveryServicesBackupSchedulePolicyObject** para obter a política de agendamento padrão. Os objetos de política de retenção e agendamento são usados como entradas para o cmdlet **New-AzureRmRecoveryServicesBackupProtectionPolicy**.

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

Habilitar a proteção envolve dois objetos, o item e a política. Ambos os objetos são necessários para habilitar a proteção no cofre. Depois de a política ter sido associada ao cofre, o fluxo de trabalho de backup será disparado no momento definido no agendamento da política.

Para habilitar a política de proteção,

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
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name NewPolicy
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy  $RetPol
```

## Executar um backup inicial

O agendamento de backup dispara um backup completo no backup inicial para o item. Os backups posteriores serão apenas uma cópia incremental. Se você desejar forçar que o backup inicial ocorra em um dado momento ou até mesmo imediatamente, use o cmdlet **[Backup-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723312.aspx)**:

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "V2VM"
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [AZURE.NOTE: o fuso horário dos campos StartTime e EndTime no PowerShell é UTC. No entanto, quando a hora é exibida no Portal do Azure, ela é ajustada para seu fuso horário local.

## Monitoramento de um trabalho de backup

A maioria das operações de longa duração no Backup do Azure são modeladas como um trabalho. Isso facilita acompanhar o andamento sem a necessidade de manter o portal do Azure aberto em todos os momentos.

Para obter o status mais recente de um trabalho em andamento, use o cmdlet Get-AzureRmRecoveryservicesBackupJob.

```
PS C:\ > $joblist = Get-AzureRmRecoveryservicesBackupJob –Status InProgress
PS C:\ > $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Em vez de sondar esses trabalhos para conclusão (o que é um código adicional desnecessário), é mais simples usar o cmdlet **[Wait-AzureRmRecoveryServicesBackupJob](https://msdn.microsoft.com/library/mt723321.aspx)**. Esse cmdlet pausa a execução até que o trabalho seja concluído ou o valor de tempo limite especificado seja atingido.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## Restaurar uma VM do Azure

Há uma diferença importante entre a restauração de uma máquina virtual usando o portal do Azure e a restauração de uma máquina virtual usando o PowerShell. Com o PowerShell, a operação de restauração é concluída quando são criadas as informações dos discos e de configuração do ponto de recuperação. A operação de restauração não cria uma máquina virtual. As instruções para a criação da máquina virtual dos discos são fornecidas. No entanto, para restaurar completamente uma VM, você precisa trabalhar com os procedimentos a seguir:

- Selecione a VM
- Escolha um ponto de recuperação
- Restaure os discos
- Crie a VM de discos armazenados

O gráfico a seguir mostra a hierarquia de objetos do RecoveryServicesVault até o BackupRecoveryPoint.

![Hierarquia de objetos dos Serviços de Recuperação mostrando BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Para restaurar dados de backup, identifique o item de backup e o ponto de recuperação que mantém os dados pontuais. Em seguida, use o cmdlet **[Restore-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723316.aspx)** para restaurar dados do cofre para a conta do cliente.

### Selecione a VM

Para obter o objeto do PowerShell que identifica o item correto de backup, comece do contêiner no cofre e desça progressivamente na hierarquia de objetos. Para selecionar o contêiner que representa a VM, use o cmdlet **[Get-AzureRmRecoveryServicesBackupContainer](https://msdn.microsoft.com/library/mt723319.aspx)** e o redirecione para o cmdlet **[Get-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723305.aspx)**.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType AzureVM –Status Registered -Name 'V2VM'
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem –Container $namedContainer  –WorkloadType "AzureVM"
```

### Escolha um ponto de recuperação

Use o cmdlet **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://msdn.microsoft.com/library/mt723308.aspx)** para listar todos os pontos de recuperação para o item de backup. Em seguida, escolha o ponto de recuperação a ser restaurado. Se você não tiver certeza de qual ponto de recuperação será usado, é uma boa prática escolher o mais recente ponto RecoveryPointType = AppConsistent na lista.

No script a seguir, a variável **$rp** é uma matriz de pontos de recuperação para o item de backup selecionado. A matriz é classificada em ordem inversa de tempo com o último ponto de recuperação no índice 0. Use a indexação de matriz padrão do PowerShell para selecionar o ponto de recuperação. Por exemplo: $rp[0] selecionará o último ponto de recuperação.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
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



### Restaure os discos

Use o cmdlet **[Restore-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723316.aspx)** para restaurar os dados e a configuração de um item de Backup para um ponto de recuperação. Depois de ter identificado um ponto de recuperação, use-o como o valor do parâmetro **- RecoveryPoint**. No código de exemplo anterior, **$rp [0]** foi escolhido como o ponto de recuperação a ser usado. No código de exemplo abaixo, **$rp [0]** é especificado como o ponto de recuperação a ser usado para a restauração no disco.

Para restaurar as informações de discos e de configuração

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName DestAccount -StorageAccountResourceGroupName DestRG
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Após a conclusão do trabalho de Restauração, use o cmdlet **[Get-AzureRmRecoveryServicesBackupJobDetails](https://msdn.microsoft.com/library/mt723310.aspx)** para obter os detalhes da operação de restauração. A propriedade JobDetails tem as informações necessárias para recompilar a VM.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails
```

Depois de restaurar os discos, vá para a próxima seção para saber mais sobre como criar a VM.

### Criar uma máquina virtual de discos restaurados

Depois de ter restaurado os discos, use estas etapas para criar e configurar uma máquina virtual ARM do disco.

1. Consulte as propriedades do disco restaurado para obter os detalhes do trabalho.

    ```
    PS C:\> $properties = $details.properties
    PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
    PS C:\> $containerName = $properties["Config Blob Container Name"]
    PS C:\> $blobName = $properties["Config Blob Name"]
    ```

2. Defina o contexto do armazenamento do Azure e restaure o arquivo de configuração JSON.

    ```
    Set -AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName testvault
    PS C:\> $destination_path = "C:\vmconfig.json"
    Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination
    PS C:\> $destination_path -Context $storageContext
    PS C:\> $obj = ((Get-Content -Path $destination_path -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```

3. Use o arquivo de configuração JSON para criar a configuração da VM.

    ```
  PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.HardwareProfile.VirtualMachineSize -VMName "testrestore"
    ```

4. Anexe o disco do sistema operacional e os discos de dados.

    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.StorageProfile.OSDisk.VirtualHardDisk.Uri
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.StorageProfile.OSDisk.OperatingSystemType foreach($dd in $obj.StorageProfile.DataDisks)
    {
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.VirtualHardDisk.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption Attach
    }
    ```

5. Defina as configurações de Rede.

    ```
    PS C:\> $nicName="p1234"
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Crie a máquina virtual.

    ```
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.StorageProfile.OSDisk.OperatingSystemType
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "centralindia" -VM $vm
    ```

<!---HONumber=AcomDC_0525_2016-->