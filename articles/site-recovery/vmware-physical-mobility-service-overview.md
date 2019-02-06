---
title: Sobre o Serviço de Mobilidade para recuperação de desastre de VMs VMware e servidores físicos com Azure Site Recovery | Microsoft Docs
description: Saiba mais sobre o agente do Serviço de Mobilidade para recuperação de desastre de VMs do VMware e servidores físicos no Azure usando o serviço Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 6319ef908b5b040bf61285451448c08bb3960fe2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215003"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Sobre o serviço de Mobilidade para VMs do VMware e servidores físicos

Ao configurar a recuperação de desastres para VMs do VMware e servidores físicos usando o [Azure Site Recovery](site-recovery-overview.md), instale o serviço de mobilidade do Site Recovery em cada VM do VMware local e servidor físico.  O Serviço de mobilidade captura gravações de dados no computador e as encaminha para o servidor de processo do Site Recovery. Você pode implantar o Serviço de Mobilidade usando os seguintes métodos:

[Logs de instalação por push](vmware-azure-install-mobility-service.md): Configure a Recuperação do Site para executar uma instalação por push do serviço de mobilidade: Para isso, ao configurar a recuperação de desastre, você também tem de configurar uma conta que o servidor de processo do Site Recovery possa usar para acessar a VM ou servidor físico para fins de instalação do serviço.
[Instalar manualmente](vmware-physical-mobility-service-install-manual.md): Você pode instalar o serviço de mobilidade manualmente em cada computador usando a interface do usuário ou o prompt de comando.
[Implantação automatizada](vmware-azure-mobility-install-configuration-mgr.md): Você pode automatizar a instalação com ferramentas de implantação de software, como o System Center Configuration Manager.

## <a name="azure-virtual-machine-agent"></a>Agente de Máquina Virtual do Azure

- **VMs do Windows**: A partir da versão 9.7.0.0 do serviço de mobilidade, o [agente de VM do Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) é instalado pelo instalador do serviço de mobilidade. Isso garante que, quando um computador fizer failover no Azure, a VM do Azure atenderá ao pré-requisito da instalação do agente para usar qualquer extensão de VM.
- **VMs Linux**: O [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) deve ser instalado manualmente na VM do Azure após o failover.

## <a name="installer-files"></a>Arquivos do instalador

A tabela resume os arquivos do instalador para cada VM do VMware e sistema operacional de servidor físico. Você pode revisar os [sistemas operacionais compatíveis](vmware-physical-azure-support-matrix.md#replicated-machines) antes de começar.


**Arquivo de instalador** | **Sistema operacional (somente 64 bits)** 
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1 
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.* 
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1,SP2,SP3 
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Servidor do Ubuntu Linux 16.04 LTS
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="anti-virus-on-replicated-machines"></a>Antivírus em computadores replicados

Se as máquinas que você deseja replicar tiverem o software antivírus ativo em execução, exclua a pasta de instalação do serviço Mobility das operações de antivírus (*C:\ProgramData\ASR\agent*). Isso garante que a replicação funciona conforme o esperado.

## <a name="update-the-mobility-service"></a>Atualizar o Serviço de Mobilidade

1. Antes de iniciar, certifique-se de que o servidor de configuração, os servidores de processo de expansão e quaisquer servidores de destino que não façam parte da sua implantação sejam atualizados antes de você atualizar o Serviço de Mobilidade nos computadores protegidos.
2. No portal, abra o cofre > **Itens replicados**.
3. Se o servidor de configuração estiver na versão mais recente, você verá uma notificação que mostra "Nova atualização do agente de replicação de recuperação do site está disponível. Clique para instalar."

     ![Janela Itens replicados](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Clique na notificação e, em **Atualização do agente**, selecione os computadores nos quais você deseja atualizar o serviço de mobilidade. Em seguida, clique em **OK**.

     ![Lista de VMs de itens replicados](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. O trabalho Atualizar o Serviço de Mobilidade é iniciado para cada um dos computadores selecionados.

## <a name="update-the-account-used-for-push-installation-of-the-mobility-service"></a>Atualizar a conta usada para a instalação por push do Serviço de Mobilidade

Quando você implantou o Site Recovery para habilitar a instalação por push do serviço de mobilidade, especificou uma conta que o servidor de processo do Site Recovery usa para acessar os computadores e instalar o serviço quando a replicação é habilitada para o computador. Se você quiser atualizar as credenciais dessa conta, siga [estas instruções](vmware-azure-manage-configuration-server.md).

## <a name="uninstall-the-mobility-service"></a>Desinstalar o serviço de Mobilidade

### <a name="on-a-windows-machine"></a>Em uma máquina Windows

Desinstale pela interface do usuário ou por um prompt de comando.

- **Na interface de usuário**: No Painel de Controle do computador, selecione **Programas**. Selecione **Servidor de destino mestre/Serviço de Mobilidade do Microsoft Azure Site Recovery** > **Desinstalar**.
- **Em um prompt de comando**: Abra uma janela de prompt de comando como administrador no computador. Execute o comando a seguir: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="on-a-linux-machine"></a>Em um computador Linux
1. No computador Linux, entre como um usuário **raiz**.
2. Em um terminal, acesse /user/local/ASR.
3. Execute o comando a seguir:

    ```
    uninstall.sh -Y
    ```

## <a name="next-steps"></a>Próximas etapas

[Configurar instalação do Serviço de Mobilidade por push](vmware-azure-install-mobility-service.md).
