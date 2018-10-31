---
title: Serviços de área de trabalho remotos não estão iniciando uma VM do Azure | Microsoft Docs
description: Saiba como solucionar problemas com os serviços de área de trabalho remota ao se conectar a uma máquina Virtual | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 39b793e2722766f3f28829b4dc48534054abd97e
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988936"
---
# <a name="remote-desktop-services-is-not-starting-on-an-azure-vm"></a>Serviços de área de trabalho remota não está iniciando uma VM do Azure

Este artigo descreve como solucionar problemas de conexão para uma máquina Virtual do Azure (VM) quando os serviços de área de trabalho remota (TermService) não estiverem iniciando ou falharem ao iniciar.

>[!NOTE]
>O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo descreve o uso do modelo de implantação do Gerenciador de Recursos. É recomendável usar esse modelo para novas implantações, em vez de usar o modelo de implantação clássico.

## <a name="symptoms"></a>Sintomas

Ao tentar conectar uma VM, você enfrenta os seguintes cenários:

- A captura de tela da VM mostra que o sistema operacional está totalmente carregado e aguardando as credenciais.
- Todos os aplicativos na VM estão funcionando conforme o esperado e acessíveis.
- A VM está respondendo à conectividade TCP na porta da Área de Trabalho Remota da Microsoft do Protocolo TCP (padrãp 3389).
- Você não será solicitado para credenciais ao tentar fazer uma conexão de RDP.

## <a name="cause"></a>Causa

Esse problema ocorre porque os serviços de área de trabalho remota não estão em execução na máquina Virtual. A causa pode depender de cenários a seguir:

- O serviço TermService foi definido como **Desabilitado**.
- O serviço de TermService está falhando ou travando.

## <a name="solution"></a>Solução

Para resolver esse problema, use uma das seguintes soluções ou experimente as soluções individualmente:

### <a name="solution-1-using-the-serial-console"></a>Solução 1: Usar o Console Serial

1. Acesso ao [Console Serial](serial-console-windows.md) selecionando **Suporte e solução de problemas** > **Console serial (visualização)**. Se o recurso estiver habilitado na VM, você poderá conectar a VM com êxito.

2. Crie um novo canal para uma instância de CMD. Digite **CMD** para iniciar o canal e obter o nome do canal.

3. Alterne para o canal que estiver executando a instância de CMD, nesse caso, deve ser o canal 1.

   ```
   ch -si 1
   ```

4. Pressione **Enter** novamente e digite um nome de usuário e senha válidos (ID de domínio ou local) para a VM.

5. Consulte o status do serviço TermService.

   ```
   sc query TermService
   ```

6. Se o status do serviço mostra **Parado**, tente iniciar o serviço.

   ```
   sc start TermService
   ```

7. Consulte o serviço novamente para certificar-se de que o serviço é iniciado com êxito.

   ```
   sc query TermService
   ```

### <a name="solution-2-using-a-recovery-vm-to-enable-the-service"></a>Solução 2: usar uma VM de recuperação para habilitar o serviço

[Fazer backup de disco do sistema operacional](../windows/snapshot-copy-managed-disk.md) e [anexar o disco do sistema operacional a uma VM de resgate](../windows/troubleshoot-recovery-disks-portal.md). Em seguida, abra uma instância CMD com privilégios elevados e execute os scripts a seguir sobre o VM de resgate:

>[!NOTE]
>Vamos supor que a letra da unidade atribuída ao disco do sistema operacional anexado seja F. Substitua-o com o valor apropriado em sua VM. Depois que isso for feito, desanexe o disco da VM de recuperação e [recriar a VM](../windows/create-vm-specialized.md). Para solução de problemas, você pode usar a **Solução 1** porque o Console Serial foi ativado.

```
reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

REM Enable Serial Console
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} displaybootmenu yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} timeout 10
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} bootems yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /ems {<Boot Loader Identifier>} ON
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

REM Get the current ControlSet from where the OS is booting
for /f "tokens=3" %x in ('REG QUERY HKLM\BROKENSYSTEM\Select /v Current') do set ControlSet=%x
set ControlSet=%ControlSet:~2,1%

REM Suggested configuration to enable OS Dump
set key=HKLM\BROKENSYSTEM\ControlSet00%ControlSet%\Control\CrashControl
REG ADD %key% /v CrashDumpEnabled /t REG_DWORD /d 2 /f
REG ADD %key% /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
REG ADD %key% /v NMICrashDump /t REG_DWORD /d 1 /f

REM Set default values back on the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Image Path>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v type /t REG_DWORD /d 16 /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v type /t REG_DWORD /d 16 /f

REM Enable default dependencies from the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg unload HKLM\BROKENSYSTEM
```

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
