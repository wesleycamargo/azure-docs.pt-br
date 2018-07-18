---
title: Faça backup do Azure pilha | Microsoft Docs
description: Execute um backup sob demanda na pilha do Azure com o backup em vigor.
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
ms.openlocfilehash: c2a6727692a7a74b3e5fe32de8800722a9ed91b5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
---
# <a name="back-up-azure-stack"></a>Fazer backup do Azure pilha

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Execute um backup sob demanda na pilha do Azure com o backup em vigor. Se você precisar habilitar o serviço de Backup de infraestrutura, consulte [habilitar o Backup para a pilha do Azure no portal de administração](azure-stack-backup-enable-backup-console.md).

> [!Note]  
>  Para obter instruções sobre como configurar o ambiente do PowerShell, consulte [instalar o PowerShell para Azure pilha ](azure-stack-powershell-install.md).

## <a name="start-azure-stack-backup"></a>Iniciar o backup de pilha do Azure

Abra o Windows PowerShell com um prompt com privilégios elevados no ambiente de gerenciamento de operador e execute os seguintes comandos:

```powershell
    Start-AzSBackup -Location $location.Name
```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Confirme o backup foi concluído no portal de administração

1. Abra o portal de administração de pilha do Azure em [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Selecione **mais serviços** > **backup da infra-estrutura**. Escolha **configuração** no **backup da infra-estrutura** folha.
3. Localizar o **nome** e **data de conclusão** do backup em **backups disponíveis** lista.
4. Verifique se o **estado** é **êxito**.

<!-- You can also confirm the backup completed from the administration portal. Navigate to `\MASBackup\<datetime>\<backupid>\BackupInfo.xml`

In ‘Confirm backup completed’ section, the path at the end doesn’t make sense (ie relative to what, datetime format, etc?)
\MASBackup\<datetime>\<backupid>\BackupInfo.xml -->


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o fluxo de trabalho para se recuperar de um evento de perda de dados. Consulte [recuperação da perda de dados catastrófica de](azure-stack-backup-recover-data.md).
