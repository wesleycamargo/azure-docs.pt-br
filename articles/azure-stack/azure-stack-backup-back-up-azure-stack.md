---
title: Fazer backup do Azure Stack | Microsoft Docs
description: Execute um backup sob demanda no Azure Stack com backup em vigor.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/08/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: c9e7ffae1b988d0940d10acdb1b387a25e0466ec
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242879"
---
# <a name="back-up-azure-stack"></a>Fazer backup do Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Execute um backup sob demanda no Azure Stack com backup em vigor. Para obter instruções sobre como configurar o ambiente do PowerShell, consulte [instalar o PowerShell para Azure Stack ](azure-stack-powershell-install.md). Para entrar Azure Stack, consulte [configurar o ambiente de operador e entrar no Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="start-azure-stack-backup"></a>Iniciar o backup do Azure Stack

Use AzSBackup Iniciar para iniciar um novo backup com variável - AsJob para acompanhar o progresso. 

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output
```

## <a name="confirm-backup-completed-via-powershell"></a>Confirme se o backup foi concluído por meio do PowerShell

```powershell
    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

- O resultado deve ser semelhante a seguinte saída:

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

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Confirme se o backup foi concluído no portal de administração

1. Abra o portal de administração do Azure Stack em [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Selecione **mais serviços** > **backup da infra-estrutura**. Escolher **Configuration** na **backup de infraestrutura** folha.
3. Localizar o **nome** e **data de conclusão** do backup **backups disponíveis** lista.
4. Verifique se o **estado** é **Succeeded**.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o fluxo de trabalho para se recuperar de um evento de perda de dados. Ver [recuperar da perda de dados catastrófica](azure-stack-backup-recover-data.md).