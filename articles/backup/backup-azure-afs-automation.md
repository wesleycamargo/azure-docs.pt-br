---
title: Implantar e gerenciar backups para compartilhamentos de arquivos do Azure usando o PowerShell
description: Use o PowerShell para implantar e gerenciar backups no Azure para compartilhamentos de arquivos do Azure
services: backup
author: pvrk
manager: shivamg
keywords: PowerShell; backup de Arquivos do Azure; restauração de Arquivos do Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: pullabhk
ms.assetid: 80da8ece-2cce-40dd-8dce-79960b6ae073
ms.openlocfilehash: 4ead84ef415dcb85682b15414380055d8799b54c
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051213"
---
# <a name="use-powershell-to-back-up-and-restore-azure-file-shares"></a>Usar o PowerShell para fazer backup e restaurar compartilhamentos de arquivos do Azure

Este artigo mostra como usar cmdlets do Azure PowerShell para fazer backup e recuperar um compartilhamento de arquivos do Azure de um cofre dos Serviços de Recuperação. O cofre dos Serviços de Recuperação é um recurso do Azure Resource Manager usado para proteger dados e ativos no Azure Backup e no Azure Site Recovery.

## <a name="concepts"></a>Conceitos

Se você não estiver familiarizado com o Backup do Azure, consulte [O que é o Backup do Azure?](backup-introduction-to-azure-backup.md) para obter uma visão geral do serviço. Antes de começar, consulte os recursos de visualização usados para fazer backup dos compartilhamentos de arquivos do Azure em [Backup de compartilhamentos de arquivos do Azure](backup-azure-files.md).

Para usar o PowerShell de maneira eficaz, é necessário compreender a hierarquia de objetos e de onde começar.

![Hierarquia de objetos dos Serviços de Recuperação](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Para exibir a referência do cmdlet **AzureRm.RecoveryServices.Backup** do PowerShell, consulte [Backup do Azure – cmdlets dos Serviços de Recuperação](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) na biblioteca do Azure.

## <a name="setup-and-registration"></a>Configuração e registro

> [!NOTE]
> Como observado em [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.13.0), o suporte para novos recursos no módulo AzureRM termina em novembro de 2018. O suporte para backup de compartilhamentos de arquivos do Azure é oferecido com o novo módulo PowerShell Az que agora está disponível para o público em geral.

Siga estas etapas para começar:

1. [Baixe a versão mais recente do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-6.13.0). A versão 1.0.0 é a mínima necessária.

2. Localize os cmdlets do **PowerShell do Backup do Azure** disponíveis ao digitar o comando a seguir:

    ```powershell
    Get-Command *azrecoveryservices*
    ```
    Os aliases e os cmdlets do Azure Backup, do Azure Site Recovery e do cofre do Recovery Services são exibidos. A imagem a seguir é um exemplo do que você verá. Esta não é a lista completa dos cmdlets.

    ![Listar cmdlets dos Serviços de Recuperação](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Entre em sua conta do Azure usando **Connect-AzAccount**. Esse cmdlet abre uma página da Web que solicita suas credenciais de conta:

    * Como alternativa, é possível incluir as credenciais de conta como um parâmetro no cmdlet **Connect-AzAccount** ao usar o parâmetro **-Credential**.
    * Se você é um parceiro CSP trabalhando em nome de um locatário, especifique o cliente como um locatário usando sua tenantID ou o nome de domínio primário do locatário. Um exemplo é **Connect-AzAccount -Tenant** fabrikam.com.

4. Associe a assinatura que deseja usar com a conta, porque uma conta pode ter várias assinaturas.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Se você estiver usando um Backup do Azure pela primeira vez, use o cmdlet **Register-AzResourceProvider** para registrar o provedor dos Serviços de Recuperação do Azure com sua assinatura.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Verifique se os provedores foram registrados com êxito usando os comandos a seguir.
    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    Na saída do comando, o **RegistrationState** se altera para **Registrado**. Se você não vir essa alteração, execute novamente o cmdlet **Register-AzResourceProvider**.

As seguintes tarefas podem ser automatizadas com o PowerShell:

* Crie um cofre dos Serviços de Recuperação.
* Configure o backup para compartilhamentos de arquivos do Azure.
* Acione um trabalho de backup.
* Monitore um trabalho de backup.
* Restaure um compartilhamento de arquivos do Azure.
* Restaure um arquivo individual do Azure a partir de um compartilhamento de arquivos do Azure.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Siga estas etapas para criar um cofre dos Serviços de Recuperação:

1. O cofre dos Serviços de Recuperação é um recurso do Resource Manager e, portanto, você precisará colocá-lo em um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um grupo de recursos com o cmdlet **New-AzResourceGroup**. Ao criar um grupo de recursos, especifique o nome e o local.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Use o cmdlet **New-AzRecoveryServicesVault** para criar um cofre dos Serviços de Recuperação. Especifique o mesmo local para o cofre usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Especifique o tipo de redundância de armazenamento a usar. Você pode usar [armazenamento com redundância local](../storage/common/storage-redundancy-lrs.md) ou [armazenamento com redundância geográfica](../storage/common/storage-redundancy-grs.md). O exemplo a seguir mostra que a opção **-BackupStorageRedundancy** para o **testvault** está definida como **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Exibir os cofres em uma assinatura

Para exibir todos os cofres da assinatura, use **Get-AzRecoveryServicesVault**.

```powershell
Get-AzRecoveryServicesVault
```

A saída deverá ser semelhante ao seguinte exemplo: Observe que são fornecidos o **ResourceGroupName** e **Location** associados.

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

Use **Set-AzRecoveryServicesVaultContext** para definir o contexto do cofre. Depois que o contexto de cofre estiver definido, ele será aplicado a todos os cmdlets subsequentes. O exemplo a seguir define o contexto de cofre de **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

> [!NOTE]
> Estamos planejando preterir a configuração do contexto do cofre de acordo com as diretrizes do Azure PowerShell. Assim, é recomendável que os usuários passem a ID do cofre, como mencionado nas instruções a seguir.

Como alternativa, armazene ou busque a ID do cofre no qual deseja executar uma operação do PowerShell e passe-a para o comando relevante.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-backup-for-an-azure-file-share"></a>Configurar o backup para um compartilhamento de arquivos do Azure

### <a name="create-a-protection-policy"></a>Crie uma política de proteção

Uma política de proteção de backup está associada a pelo menos uma política de retenção. Uma política de retenção define por quanto tempo um ponto de recuperação é mantido até ser excluído. Use **Get-AzRecoveryServicesBackupRetentionPolicyObject** para exibir a política de retenção padrão. 

Da mesma forma, você pode usar **Get-AzRecoveryServicesBackupSchedulePolicyObject** para obter a política de agendamento padrão. O cmdlet **New-AzRecoveryServicesBackupProtectionPolicy** cria um objeto do PowerShell que mantém as informações da política de backup. Os objetos de política de retenção e agendamento são usados como entradas para o cmdlet **New-AzRecoveryServicesBackupProtectionPolicy**. 

O exemplo a seguir armazena a política de agendamento e a política de retenção em variáveis. O exemplo usa essas variáveis para definir os parâmetros ao criar a política de proteção **NewPolicy**.

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

**NewAFSPolicy** faz um backup diário e os retém por 30 dias.

### <a name="enable-protection"></a>Habilitar proteção

Depois de definir a política de proteção, você pode habilitar a proteção para o compartilhamento de arquivos do Azure com essa política.

Primeiro, busque o objeto de política relevante com o cmdlet **Get-AzRecoveryServicesBackupProtectionPolicy**. Use este cmdlet para obter uma política específica ou para exibir as políticas associadas a um tipo de carga de trabalho.

O exemplo a seguir obtém as políticas para o tipo de carga de trabalho **AzureFiles**.

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
> O fuso horário do campo **BackupTime** no PowerShell é o UTC (Tempo Universal Coordenado). Quando a hora de backup é mostrada no Portal do Azure, o horário é ajustado para seu fuso horário local.
>
>

A política a seguir recupera a política de backup **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

Use **Enable-AzRecoveryServicesBackupProtection** para habilitar a proteção do item com a política determinada. Depois de associar a política ao cofre, o fluxo de trabalho de backup será acionado no momento definido no agendamento da política.

O exemplo a seguir habilita a proteção para o compartilhamento de arquivos do Azure, **testAzureFileShare**, na conta de armazenamento **testStorageAcct** com a política **dailyafs**.

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

Use **Backup-AzRecoveryServicesBackupItem** para disparar um trabalho de backup para um compartilhamento de arquivos do Azure protegido. Recupere a conta de armazenamento e o compartilhamento de arquivos nela usando os comandos a seguir e inicie um backup sob demanda.

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

O comando retorna um trabalho com uma ID a ser rastreada, como no exemplo a seguir.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Os instantâneos do compartilhamento de arquivos do Azure são usados enquanto os backups são feitos, portanto, o trabalho normalmente é concluído no momento em que o comando retorna essa saída.

### <a name="modify-the-protection-policy"></a>Modificar a política de proteção

Para alterar a política de proteção do compartilhamento de arquivos do Azure, use **Enable-AzRecoveryServicesBackupProtection** com o item de backup relevante e a nova política de proteção.

O exemplo a seguir altera a política de proteção de **testAzureFS** de **dailyafs** para **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-azure-files"></a>Restaurar compartilhamentos de arquivos e arquivos do Azure

Você pode restaurar um compartilhamento de arquivos inteiro para o local original ou um local alternativo. De forma semelhante, arquivos individuais do compartilhamento de arquivos também podem ser restaurados.

### <a name="fetch-recovery-points"></a>Buscar pontos de recuperação

Use o cmdlet **Get-AzRecoveryServicesBackupRecoveryPoint** para listar todos os pontos de recuperação para o item de backup. No script a seguir, a variável **$rp** é uma matriz de pontos de recuperação dos últimos sete dias para o item de backup selecionado. A matriz é classificada em ordem inversa de tempo com o último ponto de recuperação no índice **0**. Use a indexação de matriz padrão do PowerShell para selecionar o ponto de recuperação. No exemplo, **$rp[0]** seleciona o ponto de recuperação mais recente.

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

Após selecionar o ponto de recuperação relevante, restaure o compartilhamento de arquivos ou arquivo em um local alternativo ou no local original, como explicado abaixo.

### <a name="restore-azure-file-shares-to-an-alternate-location"></a>Restaurar compartilhamentos de arquivos do Azure para uma localização alternativa

#### <a name="restore-an-azure-file-share"></a>Restaurar um compartilhamento de arquivos do Azure

Identifique a localização alternativa fornecendo as informações a seguir:

* **TargetStorageAccountName**: a conta de armazenamento na qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **TargetFileShareName**: compartilhamentos de arquivos dentro da conta de armazenamento de destino na qual o conteúdo de backup é restaurado.
* **TargetFolder**: a pasta no compartilhamento de arquivos na qual os dados são restaurados. Se for para restaurar o conteúdo do backup em uma pasta raiz, forneça os valores da pasta de destino como uma cadeia de caracteres vazia.
* **ResolveConflict**: a instrução para o caso de um conflito com os dados restaurados. Aceita **Overwrite** ou **Skip**.

Forneça esses parâmetros para o comando de restauração para restaurar um compartilhamento de arquivos de backup em um local alternativo.

````powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
````

O comando retorna um trabalho com uma ID a ser rastreada, como no exemplo a seguir.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
````

#### <a name="restore-an-azure-file"></a>Restaurar um arquivo do Azure

Para restaurar um arquivo individual ao invés de um compartilhamento de arquivos inteiro, o arquivo individual deve ser identificado exclusivamente pelo fornecimento dos parâmetros a seguir:

* **TargetStorageAccountName**: a conta de armazenamento na qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **TargetFileShareName**: compartilhamentos de arquivos dentro da conta de armazenamento de destino na qual o conteúdo de backup é restaurado.
* **TargetFolder**: a pasta no compartilhamento de arquivos na qual os dados são restaurados. Se for para restaurar o conteúdo do backup em uma pasta raiz, forneça os valores da pasta de destino como uma cadeia de caracteres vazia.
* **SourceFilePath**: o caminho absoluto do arquivo a ser restaurado no compartilhamento de arquivos, como uma cadeia de caracteres. Esse caminho é o mesmo que foi usado no cmdlet **Get-AzStorageFile** do PowerShell.
* **SourceFileType**: se um diretório ou um arquivo está selecionado. Aceita **Directory** ou **File**.
* **ResolveConflict**: a instrução para o caso de um conflito com os dados restaurados. Aceita **Overwrite** ou **Skip**.

Os parâmetros adicionais estão relacionados apenas ao arquivo individual a ser restaurado.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Esse comando também retorna um trabalho com uma ID a ser rastreada, como mostrado anteriormente.

### <a name="restore-azure-file-shares-to-the-original-location"></a>Restaurar compartilhamentos de arquivos do Azure para o local original

Ao restaurar para um local original, não é necessário especificar todos os parâmetros do destino e relacionados ao destino. Somente **ResolveConflict** deve ser fornecido.

#### <a name="overwrite-an-azure-file-share"></a>Substituir um compartilhamento de arquivos do Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Substituir um arquivo do Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Acompanhar tarefas de backup e restauração

As operações de backup e restauração sob demanda retornam um trabalho com uma ID, como mostrado na seção ["Disparar um backup sob demanda"](#trigger-an-on-demand-backup). Use o cmdlet **Get-AzRecoveryServicesBackupJobDetails** para acompanhar o progresso do trabalho e saber mais.

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
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```
