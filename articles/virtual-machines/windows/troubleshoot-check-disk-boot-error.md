---
title: Verificando o sistema de arquivos ao inicializar uma VM do Azure| Microsoft Docs
description: Saiba como resolver o problema em que a VM mostra Verificando o sistema de arquivos ao inicializar| Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 71619bed63d5d53e6d24eac434ce5a696a445165
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43346559"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>O Windows mostra "verificando o sistema de arquivos" ao inicializar uma VM do Azure
Este artigo descreve o erro "Verificando o sistema de arquivos" que você pode encontrar ao inicializar uma VM (Máquina Virtual) do Windows no Microsoft Azure.

> [!NOTE] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo descreve o uso do modelo de implantação do Resource Manager, que recomendamos usar para novas implantações em vez do modelo de implantação clássico.

## <a name="symptom"></a>Sintoma 

Uma VM do Windows não inicia. Quando você verifica as capturas de tela de inicialização em [Diagnóstico de inicialização](./boot-diagnostics.md), você verá que o processo de verificação de disco (chkdsk.exe) está executando com uma das seguintes mensagens:

- Examinando e reparando unidade (C :)
- Verificando o sistema de arquivos em C:

## <a name="cause"></a>Causa

Se um erro NTFS for encontrado no sistema de arquivos, o Windows irá verificar e reparar a consistência do disco no próximo reinício. Geralmente isso acontece se a VM tiver um reinício inesperado ou se o processo de desligamento da VM foi abruptamente interrompido.

## <a name="solution"></a>Solução 

O Windows inicializará normalmente depois que o processo Verificar Disco for concluído. Se a VM estiver paralisada no processo Verificar Disco, tente executar Verificar Disco na VM offline:
1.  Tire um instantâneo do disco do SO da VM afetada como um backup. Para obter mais informações, consulte [Instantâneo de um disco](snapshot-copy-managed-disk.md).
2.  [Anexar o disco de SO a uma VM de recuperação](.\troubleshoot-recovery-disks-portal.md).  
3.  Na VM de recuperação, execute Verificar Disco no disco de SO anexado. No exemplo a seguir, a letra da unidade do disco de SO anexado é E: 
        
        chkdsk E: /f
4.  Depois que o processo Verificar Disco for concluído, desconecte o disco da VM de recuperação e, em seguida, anexe novamente o disco à VM afetada como um disco de SO. Para obter mais informações, consulte [Solucionar problemas de uma VM do Windows, anexando o disco de SO a uma VM de recuperação](.\troubleshoot-recovery-disks-portal.md).
