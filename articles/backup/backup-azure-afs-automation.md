---
title: Implantar e gerenciar backups para compartilhamentos de arquivos do Azure usando o PowerShell
description: Use o PowerShell para implantar e gerenciar backups no Azure para compartilhamentos de arquivos do Azure
services: backup
author: pvrk
manager: shivamg
keywords: PowersShell; backup de arquivos do Azure; restauração de arquivos do Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: pullabhk
ms.assetid: 80da8ece-2cce-40dd-8dce-79960b6ae073
ms.openlocfilehash: 90623981f67bbed15ade743192525676e58a0a83
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318363"
---
# <a name="use-powershell-to-back-up-and-restore-azure-file-shares"></a>Usar o PowerShell para fazer backup e restaurar compartilhamentos de arquivos do Azure

Este artigo mostra como usar cmdlets do Azure PowerShell para fazer backup e recuperar um compartilhamento de arquivos do Azure de um cofre dos Serviços de Recuperação. Um cofre do Recovery Services é um recurso do Azure Resource Manager usado para proteger dados e ativos nos serviços do Azure Backup e do Azure Site Recovery.

## <a name="concepts"></a>Conceitos

Se você não estiver familiarizado com o serviço de Backup do Azure, para obter uma visão geral do serviço, confira o artigo [O que é o Backup do Azure?](backup-introduction-to-azure-backup.md). Antes de começar, tome nota dos recursos em versão prévia do backup de compartilhamentos de arquivos do Azure documentados [aqui](backup-azure-files.md).

Para usar efetivamente o PowerShell, é necessário compreender a hierarquia de objetos e de onde começar.

![Hierarquia de objetos dos Serviços de Recuperação](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Para exibir a referência do cmdlet AzureRm.RecoveryServices.Backup do PowerShell, veja [Backup do Azure – cmdlets dos Serviços de Recuperação](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) na biblioteca do Azure.

## <a name="setup-and-registration"></a>Configuração e registro

> [!NOTE]
> Conforme observado [aqui](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.13.0), o suporte para novos recursos de módulo do AzureRM termina em novembro de 2018. Portanto, estamos fornecendo suporte para backup de compartilhamentos de arquivos do Azure com o novo módulo 'Az' PS. Também estamos planejando a integração do módulo do Az com a versão do GA.

Para começar:

1. [Baixe a versão mais recente do PowerShell 'Az'](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-6.13.0) (a versão mínima necessária é: 0.7.0)

2. Localize os cmdlets do PowerShell do Backup do Azure disponíveis digitando o seguinte comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```
    Os aliases e os cmdlets do Azure Backup, do Azure Site Recovery e do cofre do Recovery Services são exibidos. A imagem a seguir é um exemplo do que você verá. Não é a lista completa dos cmdlets.

    ![lista de serviços de recuperação](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Entre em sua conta do Azure usando **Connect-AzAccount**. Esse cmdlet abre uma página da Web que solicita suas credenciais de conta:

    * Como alternativa, é possível incluir as credenciais de conta como um parâmetro no cmdlet **Connect-AzAccount**, usando o parâmetro **-Credential**.
    * Se você é um parceiro CSP trabalhando em nome de um locatário, especifique o cliente como um locatário usando sua tenantID ou o nome de domínio primário do locatário. Por exemplo:  **Connect-AzAccount -Tenant "fabrikam.com"**

4. Associe a assinatura que deseja usar com a conta, uma vez que uma conta pode ter várias assinaturas:

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Se você estiver usando um Backup do Azure pela primeira vez, deverá usar o cmdlet **Register-AzResourceProvider** para registrar o provedor do Serviço de Recuperação do Azure com sua assinatura.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Você pode verificar se os provedores foram registrados com êxito usando os seguintes comandos:
    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    Na saída do comando, o **RegistrationState** deve mudar para **Registered**. Caso contrário, basta executar novamente o cmdlet **Register-AzResourceProvider**.

As seguintes tarefas podem ser automatizadas com o PowerShell:

* Criar um cofre dos Serviços de Recuperação
* Configurar o backup para compartilhamentos de arquivos do Azure
* Disparar um trabalho de backup
* Monitorar um trabalho de backup
* Restaurar um compartilhamento de arquivos do Azure
* Restaurar um arquivo do individual do Azure de um compartilhamento de arquivos do Azure

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

As etapas a seguir orientarão você durante a criação de um cofre dos Serviços de Recuperação.

1. O cofre dos Serviços de Recuperação é um recurso do Resource Manager e, portanto, você precisará colocá-lo em um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um grupo de recursos com o cmdlet **New-AzResourceGroup**. Ao criar um grupo de recursos, especifique o nome e o local para o grupo de recursos.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Use o cmdlet **New-AzRecoveryServicesVault** para criar um cofre dos Serviços de Recuperação. Lembre-se de especificar o mesmo local para o cofre usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Especifique o tipo de redundância de armazenamento a usar. Você pode usar o [Armazenamento com Redundância Local (LRS)](../storage/common/storage-redundancy-lrs.md) ou o [Armazenamento com Redundância Geográfica (GRS)](../storage/common/storage-redundancy-grs.md). O exemplo a seguir mostra que a opção BackupStorageRedundancy para o testvault está definida como GeoRedundant.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Exibir os cofres em uma assinatura

Para exibir todos os cofres da assinatura, use **Get-AzRecoveryServicesVault**:

```powershell
Get-AzRecoveryServicesVault
```

A saída é semelhante ao exemplo a seguir, observe que o ResourceGroupName e o local associado são fornecidos.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

Muitos cmdlets do Backup do Azure exigem o objeto do cofre dos Serviços de Recuperação como entrada.

Use **Set-AzRecoveryServicesVaultContext** para definir o contexto do cofre. Depois que o contexto de cofre é definido, ele se aplica a todos os cmdlets subsequentes. O exemplo a seguir define o contexto de cofre para o cofre, *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

> [!NOTE]
> Estamos planejando preterir a configuração do contexto do cofre, de acordo com as diretrizes do Azure PowerShell. Em vez disso, é recomendável que os usuários passem a ID do cofre como mencionado abaixo

Como alternativa, você pode armazenar/buscar a ID do cofre no qual deseja executar uma operação do PowerShell e passá-la para o comando relevante.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-backup-for-an-azure-file-share"></a>Configurar o backup para um compartilhamento de arquivos do Azure

### <a name="create-protection-policy"></a>Crie política de proteção

Uma política de proteção de backup está associada a pelo menos uma política de retenção. A política de retenção define por quanto tempo um ponto de recuperação é mantido até ser excluído. Use **Get-AzRecoveryServicesBackupRetentionPolicyObject** para exibir a política de retenção padrão.  Da mesma forma, você pode usar **Get-AzRecoveryServicesBackupSchedulePolicyObject** para obter a política de agendamento padrão. O cmdlet **New-AzRecoveryServicesBackupProtectionPolicy** cria um objeto do PowerShell que mantém as informações da política de backup. Os objetos de política de retenção e agendamento são usados como entradas para o cmdlet **New-AzRecoveryServicesBackupProtectionPolicy**. O exemplo a seguir armazena a política de agendamento e a política de retenção em variáveis. O exemplo usa essas variáveis para definir os parâmetros ao criar uma política de proteção, *NewPolicy*.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A saída deverá ser semelhante ao seguinte exemplo:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```

### <a name="enable-protection"></a>Habilitar proteção

Depois de definir a política de proteção, você poderá habilitar a proteção para o compartilhamento de arquivos do Azure com essa política.

Primeiro, busque o objeto de política relevante com o cmdlet **Get-AzRecoveryServicesBackupProtectionPolicy**. Você pode usar este cmdlet para obter uma política específica ou para exibir as políticas associadas a um tipo de carga de trabalho.

O exemplo a seguir obtém as políticas para o tipo de carga de trabalho, AzureFiles.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

A saída deverá ser semelhante ao seguinte exemplo:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> O fuso horário do campo BackupTime no PowerShell é UTC. No entanto, quando o tempo de backup é mostrado no Portal do Azure, o horário é ajustado para seu fuso horário local.
>
>

A política a seguir recupera a política de backup chamada "dailyafs"

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

Use **Enable-AzRecoveryServicesBackupProtection** para habilitar a proteção do item com a política determinada. Depois de a política ter sido associada ao cofre, o fluxo de trabalho de backup será disparado no momento definido no agendamento da política.

O exemplo a seguir habilita a proteção para o compartilhamento de arquivos do Azure, "testAzureFileShare", na conta de armazenamento "testStorageAcct", com a política dailyafs.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

O comando aguarda até que o trabalho de configurar a proteção seja concluído e fornece uma saída semelhante à mostrada abaixo.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

### <a name="trigger-an-on-demand-backup"></a>Disparar um backup sob demanda

Use **Backup-AzRecoveryServicesBackupItem** para disparar um trabalho de backup para um compartilhamento de arquivos do Azure protegido. Recupere a conta de armazenamento e o compartilhamento de arquivos nele usando os comandos a seguir e dispare um backup sob demanda.

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

O comando retorna um trabalho a ser acompanhado com uma ID, como no exemplo a seguir.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Estamos aproveitando os instantâneos do compartilhamento de arquivos do Azure enquanto fazemos backups e, portanto, normalmente o trabalho é concluído no momento em que o comando retorna essa saída

### <a name="modify-protection-policy"></a>Modificar a política de proteção

Se quiser alterar a política com que o compartilhamento de arquivos do Azure é protegida, use **Enable-AzRecoveryServicesBackupProtection** com o item de backup relevante e a nova política de proteção.

Os exemplos a seguir alteram a política de proteção de "testAzureFS" de "dailyafs" para "monthlyafs"

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-sharesazure-files"></a>Restaurar compartilhamentos de arquivos do Azure/arquivos do Azure

Você pode restaurar um compartilhamento de arquivos inteiro para a localização original ou uma localização alternativa. De forma semelhante, arquivos individuais do compartilhamento de arquivos também podem ser restaurados.

### <a name="fetching-recovery-points"></a>Buscando pontos de recuperação

Use o cmdlet **Get-AzRecoveryServicesBackupRecoveryPoint** para listar todos os pontos de recuperação para o item de backup. No script a seguir, a variável **$rp** é uma matriz de pontos de recuperação dos últimos sete dias para o item de backup selecionado. A matriz é classificada em ordem inversa de tempo com o último ponto de recuperação no índice 0. Use a indexação de matriz padrão do PowerShell para selecionar o ponto de recuperação. No exemplo, $rp[0] seleciona o último ponto de recuperação.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

A saída deverá ser semelhante ao seguinte exemplo:

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

Após selecionar o ponto de recuperação relevante, restaure o compartilhamento de arquivos/arquivo para uma localização alternativa/original, conforme explicado abaixo.

### <a name="restore-azure-file-shares-to-an-alternate-location"></a>Restaurar compartilhamentos de arquivos do Azure para uma localização alternativa

#### <a name="restoring-an-azure-file-share"></a>Restaurando um compartilhamento de arquivos do Azure

Identifique a localização alternativa fornecendo as informações a seguir:

* ***TargetStorageAccountName***: conta de armazenamento na qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar na mesma localização que o cofre.
* ***TargetFileShareName***: compartilhamentos de arquivos dentro da conta de armazenamento na qual o conteúdo de backup é restaurado
* ***TargetFolder***: a pasta no compartilhamento de arquivos na qual os dados são restaurados. Se o conteúdo de backup precisar ser restaurado para a pasta raiz, forneça os valores de pasta de destino como uma cadeia de caracteres vazia
* ***ResolveConflict***: instrução em caso de conflito com os dados restaurados. Aceita "Overwrite" ou "skip"

Forneça esses parâmetros para o comando de restauração para restaurar um compartilhamento de arquivos de backup em uma localização alternativa.

````powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
````

O comando retorna um trabalho com uma ID a ser acompanhada, como no exemplo a seguir.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
````

#### <a name="restoring-an-azure-file"></a>Restaurando um arquivo do Azure

Caso você queira restaurar um arquivo individual em vez de um compartilhamento de arquivos inteiro, o arquivo individual deve ser identificado exclusivamente, fornecendo os parâmetros a seguir.

* ***TargetStorageAccountName***: conta de armazenamento na qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar na mesma localização que o cofre.
* ***TargetFileShareName***: compartilhamentos de arquivos dentro da conta de armazenamento na qual o conteúdo de backup é restaurado
* ***TargetFolder***: a pasta no compartilhamento de arquivos na qual os dados são restaurados. Se o conteúdo de backup precisar ser restaurado para a pasta raiz, forneça os valores de pasta de destino como uma cadeia de caracteres vazia
* ***SourceFilePath***: o caminho absoluto do arquivo a ser restaurado no compartilhamento de arquivos, como uma cadeia de caracteres. É o mesmo caminho usado no cmdlet ```Get-AzStorageFile``` do PS.
* ***SourceFileType***: se um diretório ou um arquivo está selecionado. Aceita "Directory" ou "File"
* ***ResolveConflict***: instrução em caso de conflito com os dados restaurados. Aceita "Overwrite" ou "skip"

Como você pode ver, os parâmetros adicionais estão relacionados apenas ao arquivo individual a ser restaurado.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Isso também retornará um trabalho a ser acompanhado com uma ID, conforme mostrado acima.

### <a name="restore-azure-file-shares-to-original-location"></a>Restaurar compartilhamentos de arquivos do Azure para a localização original

No caso de uma restauração para a localização original, os parâmetros relacionados ao destino não precisam ser especificados. Somente ```ResolveConflict``` precisa ser fornecido

#### <a name="overwrite-an-azure-file-share"></a>Substituir um compartilhamento de arquivos do Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Substituir um arquivo do Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Acompanhar tarefas de backup e restauração

Operações de backup e restauração sob demanda retornam um trabalho com uma ID, conforme mostrado [acima](#trigger-an-on-demand-backup). Use o commandlet ```Get-AzRecoveryServicesBackupJobDetails``` para acompanhar o progresso do trabalho e obter mais detalhes.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support
```
