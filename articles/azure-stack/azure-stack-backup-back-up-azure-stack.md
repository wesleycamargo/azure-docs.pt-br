---
title: Fazer backup do Azure Stack | Microsoft Docs
description: Execute um backup sob demanda no Azure Stack com backup em vigor.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 578bb864f56b788db77d1201533e73d3b9616669
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2018
ms.locfileid: "42139482"
---
# <a name="back-up-azure-stack"></a>Fazer backup do Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Execute um backup sob demanda no Azure Stack com backup em vigor. Para obter instruções sobre como configurar o ambiente do PowerShell, consulte [instalar o PowerShell para Azure Stack ](azure-stack-powershell-install.md). Para entrar Azure Stack, consulte [usando o portal de administrador no Azure Stack](azure-stack-manage-portals.md).

## <a name="start-azure-stack-backup"></a>Iniciar o backup do Azure Stack

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Iniciar um novo backup sem controle de andamento do trabalho
Use AzSBackup Iniciar para iniciar um novo backup imediatamente com nenhum progresso do trabalho de acompanhamento.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-backup-with-job-progress-tracking"></a>Iniciar o backup do Azure Stack com controle de andamento do trabalho
Use AzSBackup Iniciar para iniciar um novo backup com a variável - AsJob para acompanhar o andamento do trabalho de backup.

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob 
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " `
    + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) `
    -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output

    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

## <a name="confirm-backup-has-completed"></a>Confirme se o backup foi concluído

### <a name="confirm-backup-has-completed-using-powershell"></a>Confirme se o backup foi concluído usando o PowerShell
Use os seguintes comandos do PowerShell para garantir que o backup foi concluído com êxito:

```powershell
   Get-AzsBackup
```

O resultado deve ser semelhante a seguinte saída:

```powershell
    BackupDataVersion : 1.0.1
    BackupId          : <backup ID>
    RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
    Status            : Succeeded
    CreatedDateTime   : 7/6/2018 6:46:24 AM
    TimeTakenToCreate : PT20M32.364138S
    DeploymentID      : <deployment ID>
    StampVersion      : 1.1807.0.41
    OemVersion        : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
    Name              : local/<local name>
    Type              : Microsoft.Backup.Admin/backupLocations/backups
    Location          : local
    Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administration-portal"></a>Confirme se o backup foi concluído no portal de administração
Use o portal de administração do Azure Stack para verificar que o backup foi concluído com êxito seguindo estas etapas:

1. Abra o [portal de administração do Azure Stack](azure-stack-manage-portals.md).
2. Selecione **mais serviços** > **backup da infra-estrutura**. Escolher **Configuration** na **backup de infraestrutura** folha.
3. Localizar o **nome** e **data de conclusão** do backup **backups disponíveis** lista.
4. Verifique se o **estado** é **Succeeded**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o fluxo de trabalho para se recuperar de um evento de perda de dados. Ver [recuperar da perda de dados catastrófica](azure-stack-backup-recover-data.md).
