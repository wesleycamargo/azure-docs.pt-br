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
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 09/05/2018
ms.openlocfilehash: 685183f61c1574d8c533efad8a7e0c46b1e2d23c
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870305"
---
# <a name="back-up-azure-stack"></a>Fazer backup do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Execute um backup sob demanda no Azure Stack. Para obter instruções sobre como configurar o ambiente do PowerShell, consulte [instalar o PowerShell para Azure Stack](azure-stack-powershell-install.md). Para entrar Azure Stack, consulte [usando o portal de administrador no Azure Stack](azure-stack-manage-portals.md).

## <a name="start-azure-stack-backup"></a>Iniciar o backup do Azure Stack

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Iniciar um novo backup sem controle de andamento do trabalho
Use AzSBackup Iniciar para iniciar um novo backup imediatamente com nenhum progresso do trabalho de acompanhamento.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-backup-with-job-progress-tracking"></a>Iniciar o backup do Azure Stack com controle de andamento do trabalho
Use AzSBackup Iniciar para iniciar um novo backup com o **- AsJob** parâmetro e salvá-lo como uma variável para acompanhar o andamento do trabalho de backup.

> [!NOTE]
> Trabalho de backup será exibido como bem-sucedida concluída no portal de aproximadamente 10 a 15 minutos antes do trabalho será concluído.
>
> Portanto, o status real é melhor observado por meio de código a seguir.

> [!IMPORTANT]
> O atraso de milissegundos 1 inicial é introduzido, porque o código é muito rápido registrar o trabalho corretamente e volta e sem nenhum **PSBeginTime** e, por sua vez, sem nenhum **estado** do trabalho.

```powershell
    $BackupJob = Start-AzsBackup -Force -AsJob
    While (!$BackupJob.PSBeginTime) {
        Start-Sleep -Milliseconds 1
    }
    Write-Host "Start time: $($BackupJob.PSBeginTime)"
    While ($BackupJob.State -eq "Running") {
        Write-Host "Job is currently: $($BackupJob.State) - Duration: $((New-TimeSpan -Start ($BackupJob.PSBeginTime) -End (Get-Date)).ToString().Split(".")[0])"
        Start-Sleep -Seconds 30
    }

    If ($BackupJob.State -eq "Completed") {
        Get-AzsBackup | Where-Object {$_.BackupId -eq $BackupJob.Output.BackupId}
        $Duration = $BackupJob.Output.TimeTakenToCreate
        $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Months>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
        If ($Duration -match $Pattern) {
            If (!$Matches.ContainsKey("Hours")) {
                $Hours = ""
            } 
            Else {
                $Hours = ($Matches.Hours).ToString + 'h '
            }
            $Minutes = ($Matches.Minutes)
            $Seconds = [math]::round(($Matches.Seconds))
            $Runtime = '{0}{1:00}m {2:00}s' -f $Hours, $Minutes, $Seconds
        }
        Write-Host "BackupJob: $($BackupJob.Output.BackupId) - Completed with Status: $($BackupJob.Output.Status) - It took: $($Runtime) to run" -ForegroundColor Green
    }
    ElseIf ($BackupJob.State -ne "Completed") {
        $BackupJob
        $BackupJob.Output
    }
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
2. Selecione **todos os serviços**e, em seguida, sob o **administração** categoria, selecione > **backup da infra-estrutura**. Escolher **Configuration** na **backup de infraestrutura** folha.
3. Localizar o **nome** e **data de conclusão** do backup **backups disponíveis** lista.
4. Verifique se o **estado** é **Succeeded**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o fluxo de trabalho [recuperação de um evento de perda de dados](azure-stack-backup-recover-data.md).
