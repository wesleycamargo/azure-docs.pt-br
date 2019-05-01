---
title: Gerenciar um servidor de processo usado para recuperação de desastre de VMs VMware e servidores físicos no Azure com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve gerenciar um servidor de processo definido para recuperação de desastre de VMs VMware e servidores físicos no Azure usando o Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 2c27779719c73adf4d7fc1a61a0c77d03df71815
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925597"
---
# <a name="manage-process-servers"></a>Gerenciar servidores de processo

Este artigo descreve tarefas comuns para gerenciar o servidor de processo de recuperação de Site.

O servidor de processo é usado para receber, otimizar e enviar dados de replicação para o Azure. Ele também executa uma instalação por push do serviço de mobilidade em VMs VMware e servidores físicos que você deseja replicar e executa a descoberta automática de máquinas locais. Para replicar VMs do VMware locais ou servidores físicos no Azure, o servidor de processo é instalado por padrão no computador do servidor de configuração. 

- Para implantações grandes, você pode precisar de servidores em processo locais adicionais para dimensionar a capacidade.
- Para failback do Azure para local, você deve configurar um servidor de processo temporário no Azure. Você pode excluir essa VM após o failback. 

Saiba mais sobre o servidor de processo.


## <a name="upgrade-a-process-server"></a>Atualizar um servidor em processo

Quando você implanta um servidor de processo no local, ou como uma VM do Azure para failback, a versão mais recente do servidor de processo está instalado. As equipes do Site Recovery liberam correções e melhorias regularmente, e recomendamos que você mantenha os servidores em processo atualizados. Você pode atualizar um servidor de processo da seguinte maneira:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Mover VMs para balancear a carga do servidor de processo

Balancear a carga, movendo VMs entre dois servidores de processo, da seguinte maneira:

1. No cofre, sob **Manage** clique em **infraestrutura do Site Recovery**. Sob **computadores para VMware e físicos**, clique em **servidores de configuração**.
2. Clique no servidor de configuração com a qual os servidores de processo são registrados.
3. Clique no servidor de processo para o qual você deseja balancear o tráfego.

    ![LoadBalance](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Clique em **balancear a carga**, selecione o servidor de processo de destino ao qual você deseja mover as máquinas. Em seguida, clique em **Okey**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Clique em **selecionar máquinas**e escolha as máquinas que você deseja mover de atual para o servidor de processo de destino. Detalhes de alteração de dados médio são exibidos em cada máquina virtual. Em seguida, clique em **OK**. 
3. No cofre, monitorar o progresso do trabalho em **Monitoring** > **trabalhos do Site Recovery**.

Levará cerca de 15 minutos para que as alterações sejam refletidas no portal. Para obter um efeito mais rápido, [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>Alternar uma carga de trabalho inteira para outro servidor de processo

Mova a carga de trabalho inteira manuseada por um servidor de processo para outro servidor de processo, da seguinte maneira:

1. No cofre, sob **Manage** clique em **infraestrutura do Site Recovery**. Sob **computadores para VMware e físicos**, clique em **servidores de configuração**.
2. Clique no servidor de configuração com a qual os servidores de processo são registrados.
3. Clique no servidor de processo do qual você deseja alternar a carga de trabalho.
4. Clique em **comutador**, selecione o servidor de processo de destino ao qual você deseja mover a carga de trabalho. Em seguida, clique em **Okey**

    ![Switch](media/vmware-azure-manage-process-server/Switch.PNG)

5. No cofre, monitorar o progresso do trabalho em **Monitoring** > **trabalhos do Site Recovery**.

Levará cerca de 15 minutos para que as alterações sejam refletidas no portal. Para obter um efeito mais rápido, [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server).



## <a name="reregister-a-process-server"></a>Registrar um servidor em processo novamente

Registrar um servidor de processo em execução localmente ou em uma VM do Azure com o servidor de configuração da seguinte maneira:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Depois de salvar as configurações, faça o seguinte:

1. No servidor em processo, abra um prompt de comando do administrador.
2. Navegue para a pasta **%PROGRAMDATA%\ASR\Agent** e execute o comando:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Modificar as configurações de proxy de um servidor em processo local

Se um servidor de processo local usa um proxy para se conectar ao Azure, você pode modificar as configurações de proxy da seguinte maneira:

1. Entrar na máquina do servidor de processo. 
2. Abra uma janela de comando do PowerShell de administrador e execute o comando a seguir:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Navegue até a pasta **%PROGRAMDATA%\ASR\Agent**, e execute este comando:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Remover um servidor em processo

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Excluir pastas do software antivírus

Se um software antivírus estiver em execução em um servidor de processo de escalonamento horizontal (ou servidor de destino mestre), exclua as seguintes pastas de operações de antivírus:


- C:\Arquivos de Programas\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Diretório de instalação do servidor de processo. Por exemplo:  C:\Program Files (x86)\Microsoft Azure Site Recovery