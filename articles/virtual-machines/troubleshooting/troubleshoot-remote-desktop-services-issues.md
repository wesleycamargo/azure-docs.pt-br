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
ms.openlocfilehash: a9967aec61aaab5bc6b4517407f36e2a6c7342c8
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238855"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Serviços de área de trabalho remota não estiver iniciando uma VM do Azure

Este artigo descreve como solucionar problemas de conexão com uma máquina virtual do Azure (VM) quando os Serviços de Área de Trabalho Remota (TermService) não estão sendo iniciados ou não são iniciados.

>[!NOTE]
>O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo descreve o uso do modelo de implantação do Gerenciador de Recursos. É recomendável usar esse modelo para novas implantações, em vez de usar o modelo de implantação clássico.

## <a name="symptoms"></a>Sintomas

Ao tentar conectar uma VM, você enfrenta os seguintes cenários:

- A captura de tela da VM mostra que o sistema operacional está totalmente carregado e aguardando as credenciais.

    ![Captura de tela do status da VM](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Você visualizar remotamente os logs de eventos na VM usando o Visualizador de Eventos, verá que os Serviços de Área de Trabalho Remota (TermServ) não estão iniciando ou não iniciando. A seguir está um exemplo de log:

    **Nome de log**: sistema </br>
    **Origem**: Gerenciador de controle de serviço </br>
    **Data**: 12/16/2017 11:19:36 AM</br>
    **A ID de evento**: 7022</br>
    **Categoria de tarefa**: nenhum</br>
    **Nível**: erro</br>
    **Palavras-chave**: clássico</br>
    **Usuário**: n/d</br>
    **Computador**: vm.contoso.com</br>
    **Descrição**: serviço The Remote Desktop Services parou ao iniciar. 

    Você também pode usar o recurso Serial Access Console para procurar esses erros usando a seguinte consulta: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Causa
 
Esse problema ocorre porque os Serviços de Área de Trabalho Remota não estão sendo executados na VM. A causa pode depender de cenários a seguir: 

- O serviço TermService é definido como **desabilitado**. 
- O serviço de TermService está falhando ou travando. 

## <a name="solution"></a>Solução

Para solucionar esse problema, use o Console serial ou [repare a VM off-line](#repair-the-vm-offline) anexando o disco do SO da VM a uma VM de recuperação.

### <a name="use-serial-console"></a>Use o Console Serial

1. Acesse o [Console Serial](serial-console-windows.md) selecionando **Support & Troubleshooting** > **Console serial**. Se o recurso estiver habilitado na VM, você poderá conectar a VM com êxito.

2. Crie um novo canal para uma instância de CMD. Digite **CMD** para iniciar o canal e obter o nome do canal.

3. Alterne para o canal que está executando a instância do CMD. Nesse caso, ele deve ser 1 do canal.

   ```
   ch -si 1
   ```

4. Pressione **Enter** novamente e insira um nome de usuário e senha válidos (ID de domínio ou local) para a VM.

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
    Se o serviço não for iniciado, execute a solução baseada no erro recebido:

    |  Erro |  Sugestão |
    |---|---|
    |5 - ACESSO NEGADO |Veja o serviço [TermService interrompido devido a erro de Acesso Negado](#termService-service-is-stopped-because-of-access-denied-error) |
    |1058 - ERROR_SERVICE_DISABLED  |Consulte [TermService serviço está desabilitado.](#termService-service-is-disabled)  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |[Entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente|
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|[Entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente|
    |-1069 ERROR_SERVICE_LOGON_FAILED  |[Entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente    |
    |1070 - ERROR_SERVICE_START_HANG   | [Entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente  |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | Consulte [TermService serviço está desabilitado](#termService-service-is-disabled)  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente |
    |1753   |[Entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente   |

#### <a name="termservice-service-is-stopped-because-of-access-denied-error"></a>Serviço TermService está parado devido a erro de acesso negado

1. Conecte-se ao [Console serial](serial-console-windows.md#) e abra uma instância do PowerShell.
2. Faça o download da ferramenta Monitor de Processos executando o seguinte script:

        remove-module psreadline  
        $source = "https://download.sysinternals.com/files/ProcessMonitor.zip" 
        $destination = "c:\temp\ProcessMonitor.zip" 
        $wc = New-Object System.Net.WebClient 
        $wc.DownloadFile($source,$destination) 
3. Agora, inicie um rastreamento procmon:

        procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML 
4. Reproduza o problema iniciando o serviço que está dando acesso negar: 

        sc start TermService 
        
    Quando falhar, prossiga e finalize o rastreio do Process Monitor:

        procmon /Terminate 
5. Colete o arquivo  **c: \ temp \ ProcMonTrace.PML**, abra-o usando procmon e, em seguida, filtre por  **O resultado é ACCESS DENIED** como mostra a seguinte figura:

    ![Filtrar porResultar no Process Monitor](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Corrigir as chaves do registro, pastas ou arquivos que estão na saída. Normalmente, esse problema é causado pela conta de logon usada no serviço não tem permissão da ACL para acessar esses objetos. Para saber a permissão correta da ACL para a conta de logon, você pode verificar uma VM saudável. 

#### <a name="termservice-service-is-disabled"></a>Serviço TermService está desabilitado

1.  Restaure o serviço para seu valor de inicialização padrão:

        sc config TermService start= demand 
        
2.  Inicie o serviço:

        sc start TermService 
3.  Consulte o status novamente para garantir que o serviço esteja em execução: sc query TermService 
4.  Tente se conectar à VM usando a área de trabalho remota.


### <a name="repair-the-vm-offline"></a>Repare a VM offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anexar o disco de SO a uma VM de recuperação

1. [Anexar o disco de SO a uma VM de recuperação](../windows/troubleshoot-recovery-disks-portal.md).
2. Inicie uma conexão de área de trabalho remota para a VM de recuperação. Certifique-se de que o disco conectado esteja sinalizado como **Online** no console de gerenciamento de disco. Anote a letra da unidade atribuída ao disco do SO anexado.
3.  Abra uma instância do prompt de comando com privilégios elevados (**executar como administrador**), e, em seguida, execute o script a seguir. Vamos supor que a letra da unidade atribuída ao disco do sistema operacional anexado seja F. Substitua-o com o valor apropriado em sua VM. 

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
        REM Set default values back on the broken service 
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
4. [Desanexe o disco do SO e recrie a VM](../windows/troubleshoot-recovery-disks-portal.md) e verifique se o problema foi resolvido.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
