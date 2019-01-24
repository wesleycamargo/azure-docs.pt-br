---
title: Implantar e gerenciar backups para VMs implantadas com o Gerenciador de Recursos usando o PowerShell
description: Use o PowerShell para implantar e gerenciar backups no Azure para VMs implantadas com o Gerenciador de Recursos
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: raynew
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0a18931c037a1cf34d8a296a6330264bc8d38af
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54424504"
---
# <a name="use-powershell-to-back-up-and-restore-virtual-machines"></a>Usar o PowerShell para fazer backup e restaurar máquinas virtuais

Este artigo mostra como usar os cmdlets do Azure PowerShell para fazer backup e recuperar uma máquina virtual do Azure (VM) de uma área segura do Recovery Services. Um cofre do Recovery Services é um recurso do Azure Resource Manager usado para proteger dados e ativos nos serviços do Azure Backup e do Azure Site Recovery.

> [!NOTE]
> O Azure tem dois modelos de implantação para criar e trabalhar com recursos: [Resource Manager e Clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo destina-se a VMs criadas usando o modelo do Resource Manager.
>
>

Ele guiará você sobre como usar o PowerShell para proteger uma VM e como restaurar dados de um ponto de recuperação.

## <a name="concepts"></a>Conceitos

Se você não estiver familiarizado com o serviço Backup do Azure, para obter uma visão geral do serviço, consulte o artigo [O que é o Backup do Azure?](backup-introduction-to-azure-backup.md) Antes de começar, assegure-se de cobrir os pré-requisitos necessários com o Backup do Azure e as limitações da solução atual de backup da VM.

Para usar efetivamente o PowerShell, é necessário compreender a hierarquia de objetos e de onde começar.

![Hierarquia de objetos dos Serviços de Recuperação](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Para exibir a referência do cmdlet AzureRm.RecoveryServices.Backup do PowerShell, veja [Backup do Azure – cmdlets dos Serviços de Recuperação](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) na biblioteca do Azure.

## <a name="setup-and-registration"></a>Configuração e registro

Para começar:

1. [Baixe a última versão do PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) (a versão mínima necessária é: 1.4.0)

2. Localize os cmdlets do PowerShell do Backup do Azure disponíveis digitando o seguinte comando:

    ```powershell
    Get-Command *azurermrecoveryservices*
    ```    
    Os aliases e os cmdlets do Azure Backup, do Azure Site Recovery e do cofre do Recovery Services são exibidos. A imagem a seguir é um exemplo do que você verá. Não é a lista completa dos cmdlets.

    ![lista de serviços de recuperação](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Entre em sua conta do Azure usando **Connect-AzureRmAccount**. Esse cmdlet abre uma página da Web que solicita suas credenciais de conta:

    * Como alternativa, é possível incluir as credenciais de conta como um parâmetro no cmdlet **Connect-AzureRmAccount**, usando o parâmetro **-Credential**.
    * Se você é um parceiro CSP trabalhando em nome de um locatário, especifique o cliente como um locatário usando sua tenantID ou o nome de domínio primário do locatário. Por exemplo:  **Connect-AzureRmAccount -Tenant "fabrikam.com"**

4. Associe a assinatura que deseja usar com a conta, uma vez que uma conta pode ter várias assinaturas:

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Se você estiver usando um Backup do Azure pela primeira vez, deverá usar o cmdlet **[Register-AzureRmResourceProvider](https://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** para registrar o provedor do Serviço de Recuperação do Azure com sua assinatura.

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Você pode verificar se os provedores foram registrados com êxito usando os seguintes comandos:
    ```powershell
    Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    Na saída do comando, o **RegistrationState** deve mudar para **Registered**. Caso contrário, basta executar novamente o cmdlet **[Register-AzureRmResourceProvider](https://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)**.

As seguintes tarefas podem ser automatizadas com o PowerShell:

* [Criar um cofre dos Serviços de Recuperação](backup-azure-vms-automation.md#create-a-recovery-services-vault)
* [Fazer backup de VMs do Azure](backup-azure-vms-automation.md#back-up-azure-vms)
* [Disparar um trabalho de backup](backup-azure-vms-automation.md#trigger-a-backup-job)
* [Monitorar um trabalho de backup](backup-azure-vms-automation.md#monitoring-a-backup-job)
* [Restaurar uma VM do Azure](backup-azure-vms-automation.md#restore-an-azure-vm)

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

As etapas a seguir orientarão você durante a criação de um cofre dos Serviços de Recuperação. Um cofre dos Serviços de Recuperação é diferente de um cofre de Backup.

1. O cofre dos Serviços de Recuperação é um recurso do Resource Manager e, portanto, você precisará colocá-lo em um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um grupo de recursos com o cmdlet **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)**. Ao criar um grupo de recursos, especifique o nome e o local para o grupo de recursos.  

    ```powershell
    New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Use o cmdlet **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** para criar um cofre dos Serviços de Recuperação. Lembre-se de especificar o mesmo local para o cofre usado para o grupo de recursos.

    ```powershell
    New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Especifique o tipo de redundância de armazenamento a usar. Você pode usar o [Armazenamento com Redundância Local (LRS)](../storage/common/storage-redundancy-lrs.md) ou o [Armazenamento com Redundância Geográfica (GRS)](../storage/common/storage-redundancy-grs.md). O exemplo a seguir mostra que a opção BackupStorageRedundancy para o testvault está definida como GeoRedundant.

    ```powershell
    $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Muitos cmdlets do Backup do Azure exigem o objeto do cofre dos Serviços de Recuperação como entrada. Por esse motivo, pode ser útil armazenar o objeto do cofre dos Serviços de Recuperação de backup em uma variável.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Exibir os cofres em uma assinatura

Para visualizar todos os vaults da assinatura, use **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)**:

```powershell
Get-AzureRmRecoveryServicesVault
```

A saída é semelhante ao exemplo a seguir, observe que o ResourceGroupName e o local associado são fornecidos.

```
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Fazer backup de VMs do Azure

Use um cofre dos Serviços de Recuperação para proteger as máquinas virtuais. Antes de aplicar a proteção, defina o contexto de cofre (o tipo de dados protegidos no cofre) e verifique a política de proteção. A política de proteção é a agenda de quando o trabalho de backup é executado e de quanto tempo cada instantâneo de backup é mantido.

### <a name="set-vault-context"></a>Definir o contexto de cofre

Antes de habilitar a proteção em uma VM, use **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** para definir o contexto de cofre. Depois que o contexto de cofre é definido, ele se aplica a todos os cmdlets subsequentes. O exemplo a seguir define o contexto de cofre para o cofre, *testvault*.

```powershell
Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Crie uma política de proteção

Quando você cria um cofre dos Serviços de Recuperação, ele vem com proteção e políticas de retenção padrão. A política de proteção padrão dispara um trabalho de backup diariamente em um horário especificado. A política de retenção padrão retém o ponto de recuperação diário por 30 dias. Você pode usar a política padrão para proteger rapidamente sua VM e editá-la posteriormente com detalhes diferentes.

Use **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)** para visualizar as políticas de proteção disponíveis no cofre. Você pode usar este cmdlet para obter uma política específica ou para exibir as políticas associadas a um tipo de carga de trabalho. O exemplo a seguir obtém as políticas para o tipo de carga de trabalho, AzureVM.

```powershell
Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
```

A saída deverá ser semelhante ao seguinte exemplo:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> O fuso horário do campo BackupTime no PowerShell é UTC. No entanto, quando o tempo de backup é mostrado no Portal do Azure, o horário é ajustado para seu fuso horário local.
>
>

Uma política de proteção de backup está associada a pelo menos uma política de retenção. A política de retenção define por quanto tempo um ponto de recuperação é mantido até ser excluído. Use **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)** para exibir a política de retenção padrão.  Da mesma forma, você pode usar **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)** para obter a política de agendamento padrão. O cmdlet **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** cria um objeto do PowerShell que mantém as informações da política de backup. Os objetos de política de retenção e agendamento são usados como entradas para o cmdlet **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)**. O exemplo a seguir armazena a política de agendamento e a política de retenção em variáveis. O exemplo usa essas variáveis para definir os parâmetros ao criar uma política de proteção, *NewPolicy*.

```powershell
$schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A saída deverá ser semelhante ao seguinte exemplo:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Habilitar proteção

Depois de definir a política de proteção, você deve habilitar a política para um item. Use **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)** para habilitar a proteção. Habilitar a proteção envolve dois objetos – o item e a política. Depois de a política ter sido associada ao cofre, o fluxo de trabalho de backup será disparado no momento definido no agendamento da política.

Os exemplos a seguir permitem a proteção do item, V2VM, usando a política NewPolicy. Os exemplos diferem com base em se a VM está criptografada e em qual tipo de criptografia.

Para ativar a proteção em **VMs do Gerenciador de Recursos não criptografadas**:

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Para habilitar a proteção em **VMs criptografadas (criptografadas usando BEK e KEK)**, você deve conceder a permissão de serviço de Backup do Azure para ler as chaves e segredos do Cofre de chaves.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Para habilitar a proteção em **VMs criptografadas (criptografadas usando apenas BEK)**, você deve conceder a permissão de serviço de Backup do Azure para ler segredos do Cofre de chaves.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Se você estiver usando a nuvem do Azure Governamental, use o valor ff281ffe-705c-4f53-9f37-a40e6f2c68f3 para o parâmetro **-ServicePrincipalName** no cmdlet [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy).
>
>

Para ativar a proteção em uma VM clássica:

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Modificar uma política de proteção

Para modificar a política de proteção, use [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) para modificar os objetos SchedulePolicy ou RetentionPolicy.

O exemplo a seguir altera retenção de ponto de recuperação para 365 dias.

```powershell
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Disparar um backup

Use **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** para disparar um trabalho de backup. Se for o backup inicial, ele é um backup completo. Os backups posteriores fazem uma cópia incremental. Você deve usar **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** para definir o contexto de cofre antes de disparar o trabalho de backup. O exemplo a seguir supõe que o contexto de cofre já foi definido.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

A saída deverá ser semelhante ao seguinte exemplo:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> O fuso horário dos campos StartTime e EndTime mostrado no PowerShell é UTC. No entanto, quando a hora é exibida no Portal do Azure, ela é ajustada para seu fuso horário local.
>
>

## <a name="monitoring-a-backup-job"></a>Monitoramento de um trabalho de backup

Você pode monitorar operações de longa duração, como trabalhos de backup, sem usar o Portal do Azure. Para obter o status de um trabalho em andamento, use o cmdlet **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)**. Esse cmdlet obtém os trabalhos de backup para um cofre específico, o qual está especificado no contexto de cofre. O exemplo a seguir obtém o status de um trabalho em andamento como uma matriz e armazena o status na variável $joblist.

```powershell
$joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
$joblist[0]
```

A saída deverá ser semelhante ao seguinte exemplo:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Em vez de sondar esses trabalhos para conclusão (o que é um código adicional desnecessário), é mais simples usar o cmdlet **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)**. Esse cmdlet pausa a execução até que o trabalho seja concluído ou o valor de tempo limite especificado seja atingido.

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Restaurar uma VM do Azure

Há uma diferença importante entre a restauração de uma VM usando o portal do Azure e restaurar uma VM usando o PowerShell. Com o PowerShell, a operação de restauração é concluída quando são criadas as informações dos discos e de configuração do ponto de recuperação. A operação de restauração não cria a máquina virtual. Para criar uma máquina virtual com base no disco, consulte a seção [Criar a VM com base em discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Se você não quiser restaurar toda a VM, mas deseja restaurar ou recuperar alguns arquivos de um backup de VM do Azure, consulte a [seção de recuperação de arquivo](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> A operação de restauração não cria a máquina virtual.
>
>

O gráfico a seguir mostra a hierarquia de objetos do RecoveryServicesVault até o BackupRecoveryPoint.

![Hierarquia de objetos dos Serviços de Recuperação mostrando BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Para restaurar dados de backup, identifique o item de backup e o ponto de recuperação que mantém os dados pontuais. Use **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** para restaurar dados do cofre para sua conta.

As etapas básicas para restaurar uma VM do Azure são:

* Selecione a VM.
* Escolha um ponto de recuperação.
* Restaure os discos.
* Crie a VM de discos armazenados.

### <a name="select-the-vm"></a>Selecione a VM

Para obter o objeto do PowerShell que identifica o item correto de backup, comece do contêiner no cofre e desça progressivamente na hierarquia de objetos. Para selecionar o contêiner que representa a VM, use o cmdlet **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** e o redirecione para o cmdlet **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)**.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Escolha um ponto de recuperação

Use o cmdlet **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** para listar todos os pontos de recuperação para o item de backup. Em seguida, escolha o ponto de recuperação a ser restaurado. Se você não tiver certeza de qual ponto de recuperação será usado, é uma boa prática escolher o mais recente ponto RecoveryPointType = AppConsistent na lista.

No script a seguir, a variável **$rp** é uma matriz de pontos de recuperação dos últimos sete dias para o item de backup selecionado. A matriz é classificada em ordem inversa de tempo com o último ponto de recuperação no índice 0. Use a indexação de matriz padrão do PowerShell para selecionar o ponto de recuperação. No exemplo, $rp[0] seleciona o último ponto de recuperação.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

A saída deverá ser semelhante ao seguinte exemplo:

```powershell
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>Restaure os discos

Use o cmdlet **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** para restaurar os dados de backup e a configuração de um item de backup para um ponto de recuperação. Depois de identificar um ponto de recuperação, use-o como o valor para o parâmetro **-RecoveryPoint**. No exemplo acima, **$rp[0]** foi o ponto de recuperação a ser usado. No código de exemplo abaixo, **$rp [0]** é o ponto de recuperação a ser usado para a restauração do disco.

Para restaurar as informações de discos e de configuração:

```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
$restorejob
```

#### <a name="restore-managed-disks"></a>Restaurar discos gerenciados

> [!NOTE]
> Se a VM da qual foi feito backup tem discos gerenciados e você deseja restaurá-los como discos gerenciados, introduzimos a funcionalidade do Azure PowerShell v 6.7.0. em diante
>
>

Forneça um parâmetro adicional **TargetResourceGroupName** para especificar o RG para o qual os discos gerenciados serão restaurados.

```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

O arquivo **VMConfig.JSON** será restaurado para a conta de armazenamento, e os discos gerenciados serão restaurados para o RG de destino especificado.

A saída deverá ser semelhante ao seguinte exemplo:

```powershell
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Use o cmdlet **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** para aguardar a conclusão do trabalho de Restauração.

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Após a conclusão do trabalho de Restauração, use o cmdlet **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** para obter os detalhes da operação de restauração. A propriedade JobDetails tem as informações necessárias para recompilar a VM.

```powershell
$restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

Depois de restaurar os discos, vá para a próxima seção para criar a VM.

## <a name="create-a-vm-from-restored-disks"></a>Criar uma máquina virtual de discos restaurados

Depois de restaurar os discos, use as seguintes etapas para criar e configurar a máquina virtual do disco.

> [!NOTE]
> Para criar VMs criptografadas de discos restaurados, a função do Azure deverá ter permissão para executar a ação **Microsoft.KeyVault/vaults/deploy/action**. Se sua função não tem essa permissão, crie uma função personalizada com esta ação. Para obter mais informações, veja [Funções personalizadas no RBAC do Azure](../role-based-access-control/custom-roles.md).
>
>

> [!NOTE]
> Após a restauração dos discos, você pode obter um modelo de implantação para criar diretamente uma nova VM. Não há mais cmdlets diferentes do PS para criar VMs gerenciadas/não gerenciadas que são criptografadas/descriptografadas.

Os detalhes do trabalho resultante fornecem o URI do modelo, o qual pode ser consultado e implantado.

```powershell
   $properties = $details.properties
   $templateBlobURI = $properties["Template Blob Uri"]
```

Basta implantar o modelo para criar uma nova VM, conforme explicado [aqui](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy#deploy-a-template-from-an-external-source).

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobURI -storageAccountType Standard_GRS
```

A seção a seguir lista as etapas necessárias para criar uma VM usando o arquivo "VMConfig".

> [!NOTE]
> Recomendamos muito o uso do modelo de implantação detalhado acima para criar uma VM. Esta seção (pontos 1 a 6) será preterida em breve.

1. Consulte as propriedades do disco restaurado para obter os detalhes do trabalho.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Defina o contexto do armazenamento do Azure e restaure o arquivo de configuração JSON.

   ```powershell
   Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzureStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Use o arquivo de configuração JSON para criar a configuração da VM.

   ```powershell
   $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Anexe o disco do sistema operacional e os discos de dados. Esta etapa fornece exemplos para várias configurações de VM gerenciadas e criptografadas. Use o exemplo que atenda à sua configuração de VM.

   * **VMs não gerenciadas e não criptografadas** - use a amostra a seguir para VMs não gerenciadas e não criptografadas.

       ```powershell
       Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **VMs não gerenciadas e criptografadas com o Azure AD (somente BEK)** – para VMs não gerenciadas e criptografadas com o Azure AD (criptografadas usando apenas BEK), é necessário restaurar o segredo do cofre de chaves antes de poder conectar discos. Para obter mais informações, confira [Restore an encrypted virtual machine from an Azure Backup recovery point](backup-azure-restore-key-secret.md) (Restaurar uma máquina virtual criptografada de um ponto de recuperação do Backup do Azure). O exemplo a seguir mostra como anexar discos de dados e de SO a VMs criptografadas. Ao definir o disco do sistema operacional, certifique-se de mencionar o tipo de sistema operacional relevante.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **VMs não gerenciadas e criptografadas com o Azure AD (BEK e KEK)** – para VMs não gerenciadas e criptografadas com o Azure AD (criptografadas usando BEK e KEK), restaure a chave e o segredo para o cofre de chaves antes de anexar os discos. Para obter mais informações, confira [Restore an encrypted virtual machine from an Azure Backup recovery point (Restaurar uma máquina virtual criptografada de um ponto de recuperação do Backup do Azure)](backup-azure-restore-key-secret.md). O exemplo a seguir mostra como anexar discos de dados e de SO a VMs criptografadas.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
      ```

   * **VMs criptografadas e não gerenciadas sem o Azure AD (apenas BEK)** – para VMs criptografadas não gerenciadas sem o Azure AD (criptografadas usando apenas BEK), se **keyVault/segredo de origem não estiverem disponíveis**, restaure os segredos para o cofre de chaves usando o procedimento em [Restore an non-encrypted virtual machine from an Azure Backup recovery point (Restaurar uma máquina virtual não criptografada de um ponto de recuperação do Backup do Azure)](backup-azure-restore-key-secret.md). Em seguida, execute os seguintes scripts para definir detalhes de criptografia no blob do sistema operacional restaurado (essa etapa não é necessária para blob de dados). O $dekurl pode ser buscado do keyVault restaurado.<br>

   O script abaixo precisará ser executado somente quando o keyVault/segredo de origem não estiver disponível.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzureStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```

    Depois que os **segredos forem disponibilizados** e os detalhes de criptografia também estiverem definidos no blob do sistema operacional, anexe os discos usando o script abaixo.<br>

    Se o keyVault/segredos de origem já estiverem disponíveis, o script acima não precisará ser executado.

      ```powershell
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **VMs criptografadas e não gerenciadas sem o Azure AD (BEK e KEK)** – para VMs criptografadas não gerenciadas sem o Azure AD (criptografadas usando BEK e KEK), se **keyVault/chave/segredo de origem não estiverem disponíveis**, restaure a chave e os segredos para o cofre de chaves usando o procedimento em [Restore an non-encrypted virtual machine from an Azure Backup recovery point (Restaurar uma máquina virtual não criptografada de um ponto de recuperação do Backup do Azure)](backup-azure-restore-key-secret.md). Em seguida, execute os seguintes scripts para definir detalhes de criptografia no blob do sistema operacional restaurado (essa etapa não é necessária para blob de dados). O $dekurl e $kekurl podem ser buscados do keyVault restaurado.

   O script abaixo precisará ser executado somente quando o keyVault/chave/segredo de origem não estiver disponível.

    ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzureStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```
   Depois que **chave/segredos forem disponibilizados** e os detalhes de criptografia estiverem definidos no blob do sistema operacional, anexe os discos usando o script abaixo.

    Se o keyVault/chave/segredos de origem estiverem disponíveis, o script acima não precisará ser executado.

    ```powershell
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

  * **VMs gerenciadas e não criptografadas** ‒ para as VMs não criptografadas gerenciadas, anexe os discos gerenciados restaurados. Para obter informações detalhadas, confira [Anexar um disco de dados a uma VM do Windows usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

  * **VMs gerenciadas e criptografadas com o Azure AD (apenas BEK)** ‒ para VMs criptografadas gerenciadas com o Azure AD (criptografadas usando apenas BEK), anexe os discos gerenciados restaurados. Para obter informações detalhadas, confira [Anexar um disco de dados a uma VM do Windows usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

  * **VMs gerenciadas e criptografadas com o Azure AD (BEK e KEK)** ‒ para VMs criptografadas gerenciadas com o Azure AD (criptografadas usando BEK e KEK), anexe os discos gerenciados restaurados. Para obter informações detalhadas, confira [Anexar um disco de dados a uma VM do Windows usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

  * **VMs criptografadas e gerenciadas sem o Azure AD (apenas BEK)** – para VMs criptografadas e gerenciadas sem o Azure AD (criptografadas usando apenas BEK), se **keyVault/segredo de origem não estiverem disponíveis**, restaure os segredos para o cofre de chaves usando o procedimento em [Restore an non-encrypted virtual machine from an Azure Backup recovery point (Restaurar uma máquina virtual não criptografada de um ponto de recuperação do Backup do Azure)](backup-azure-restore-key-secret.md).Em seguida, execute os seguintes scripts para definir os detalhes de criptografia no disco do sistema operacional restaurado (esta etapa não é necessária para o disco de dados). O $dekurl pode ser buscado do keyVault restaurado.

    O script abaixo precisará ser executado somente quando o keyVault/segredo de origem não estiver disponível.  

    ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $diskupdateconfig = New-AzureRmDiskUpdateConfig -EncryptionSettingsEnabled $true
      $diskupdateconfig = Set-AzureRmDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
      Update-AzureRmDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
      ```

    Depois que os segredos estiverem disponíveis e os detalhes da criptografia forem definidos no disco do sistema operacional, para anexar os discos gerenciados restaurados, confira [Anexar um disco de dados a uma VM do Windows com o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

  * **VMs criptografadas e gerenciadas sem o Azure AD (BEK e KEK)** – para VMs criptografadas e gerenciadas sem o Azure AD (criptografadas usando BEK e KEK), se **keyVault/chave/segredo de origem não estiverem disponíveis**, restaure a chave e os segredos para o cofre de chaves usando o procedimento em [Restore an non-encrypted virtual machine from an Azure Backup recovery point (Restaurar uma máquina virtual não criptografada de um ponto de recuperação do Backup do Azure)](backup-azure-restore-key-secret.md). Em seguida, execute os seguintes scripts para definir detalhes de criptografia no disco do sistema operacional restaurado (essa etapa não é necessária para o disco de dados). O $dekurl e $kekurl podem ser buscados do keyVault restaurado.

  O script abaixo precisará ser executado somente quando o keyVault/chave/segredo de origem não estiver disponível.

  ```powershell
     $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
     $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
     $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
     $diskupdateconfig = New-AzureRmDiskUpdateConfig -EncryptionSettingsEnabled $true
     $diskupdateconfig = Set-AzureRmDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
     $diskupdateconfig = Set-AzureRmDiskUpdateKeyEncryptionKey -DiskUpdate $diskupdateconfig -KeyUrl $kekUrl -SourceVaultId $keyVaultId  
     Update-AzureRmDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Depois que a chave/segredos estiverem disponíveis e os detalhes da criptografia forem definidos no disco do sistema operacional, para anexar os discos gerenciados restaurados, confira [Anexar um disco de dados a uma VM do Windows com o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

5. Defina as configurações de Rede.

    ```powershell
    $nicName="p1234"
    $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzureRmVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzureRmVirtualNetwork
    $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Crie a máquina virtual.

    ```powershell  
    New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Enviar por push extensão ADE.

  * **Para VM com Azure AD** – Use o seguinte comando para habilitar manualmente a criptografia dos discos de dados  

    **Somente BEK**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

    **BEK e KEK**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

  * **Para VM sem Azure AD** – Use o seguinte comando para habilitar manualmente a criptografia dos discos de dados.

    Se, durante a execução do comando, AADClientID for solicitado, então será necessário atualizar seu Azure PowerShell.

    **Somente BEK**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK e KEK**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Restaurar arquivos de um backup de VM do Azure

Além de restauração de discos, você também pode restaurar arquivos individuais de um backup de VM do Azure. A funcionalidade de restauração de arquivos fornece acesso a todos os arquivos em um ponto de recuperação. Gerencie os arquivos via File Explorer como faria para arquivos normais.

As etapas básicas para restaurar um arquivo de um backup de VM do Azure são:

* Selecione a VM
* Escolha um ponto de recuperação
* Monte os discos do ponto de recuperação
* Copie os arquivos necessários
* Desmonte o disco

### <a name="select-the-vm"></a>Selecione a VM

Para obter o objeto do PowerShell que identifica o item correto de backup, comece do contêiner no cofre e desça progressivamente na hierarquia de objetos. Para selecionar o contêiner que representa a VM, use o cmdlet **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** e o redirecione para o cmdlet **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)**.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Escolha um ponto de recuperação

Use o cmdlet **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** para listar todos os pontos de recuperação para o item de backup. Em seguida, escolha o ponto de recuperação a ser restaurado. Se você não tiver certeza de qual ponto de recuperação será usado, é uma boa prática escolher o mais recente ponto RecoveryPointType = AppConsistent na lista.

No script a seguir, a variável **$rp** é uma matriz de pontos de recuperação dos últimos sete dias para o item de backup selecionado. A matriz é classificada em ordem inversa de tempo com o último ponto de recuperação no índice 0. Use a indexação de matriz padrão do PowerShell para selecionar o ponto de recuperação. No exemplo, $rp[0] seleciona o último ponto de recuperação.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

A saída deverá ser semelhante ao seguinte exemplo:

```
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>Monte os discos do ponto de recuperação

Use o cmdlet **[Get-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprpmountscript)** para obter o script para montar todos os discos do ponto de recuperação.

> [!NOTE]
> Os discos são montados como discos conectados do iSCSI no computador em que o script é executado. A montagem ocorre imediatamente e você não incorre em nenhuma cobrança.
>
>

```powershell
Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

A saída deverá ser semelhante ao seguinte exemplo:

```powershell
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Execute o script no computador em que deseja recuperar os arquivos. Para executar o script, você deve inserir a senha fornecida. Depois que os discos estiverem conectados, use o Windows File Explorer para procurar os novos volumes e arquivos. Para obter mais informações, consulte o artigo Backup, [Recuperar arquivos do backup da máquina virtual do Azure](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Desmonte os discos

Depois que os arquivos necessários forem copiados, use **[Disable-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)** para desmontar os discos. Certifique-se de desmontar os discos para que o acesso aos arquivos do ponto de recuperação é removido.

```powershell
Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>Próximas etapas

Se você preferir usar o PowerShell para interagir com os recursos do Azure, confira o artigo do PowerShell, [Implantar e gerenciar Backup do Windows Server](backup-client-automation.md). Se você gerencia backups do DPM, consulte o artigo [Implantar e gerenciar o backup do DPM](backup-dpm-automation.md). Esses dois artigos têm uma versão para implantações do Resource Manager, bem como para implantações do modelo Clássico.  
