---
title: Instalar o Serviço de Mobilidade (VMware ou físico no Azure) | Microsoft Docs
description: Saiba como instalar o agente de serviço de mobilidade para proteger seus VMware VMs no local e servidores físicos com o Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 445a5f10eac0959dab57e10680659c0792ad6fba
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="install-the-mobility-service"></a>Instalar o serviço de Mobilidade 

O serviço de mobilidade está instalado em VMs VMware e servidores físicos que deseja replicar ao Azure ao Azure. O serviço captura gravações de dados em um computador e, em seguida, encaminha-as para o servidor de processo. Implante o Serviço de Mobilidade em cada computador (VM VMware ou servidor físico) que você deseja replicar para o Azure. É possível implantar o Serviço de Mobilidade nos servidores e VMware VMs que você deseja proteger usando os seguintes métodos:


* [Instalar usando as ferramentas de implantação de software como o System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)
* [Instalar com a Automação do Azure e a Desired State Configuration (DSC de Automação)](vmware-azure-mobility-deploy-automation-dsc.md)
* [Instalar manualmente a partir da interface do usuário](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui)
* [Instalar manualmente a partir de um prompt de comando](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt)
* [Instalar usando a instalação por push do Site Recovery](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> A partir da versão 9.7.0.0, em VMs Windows, o instalador do Serviço de Mobilidade também instala o [Agente de VM do Azure](../virtual-machines/windows/extensions-features.md#azure-vm-agent) mais recente disponível. Quando um computador faz failover no Azure, o computador atende ao pré-requisito da instalação do agente para usar qualquer extensão de VM.

## <a name="prerequisites"></a>pré-requisitos
Conclua estas etapas de pré-requisito antes de instalar o Serviço de Mobilidade manualmente no servidor:
1. Entre no servidor de configuração e, em seguida, abra uma janela do prompt de comando como administrador.
2. Altere o diretório para a pasta bin e, em seguida, crie um arquivo de frase secreta.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Armazene o arquivo de senha em uma localização segura. É possível usar o arquivo durante a instalação do Serviço de Mobilidade.
4. Os instaladores do Serviço de Mobilidade para todos os sistemas operacionais com suporte estão na pasta %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository.

### <a name="mobility-service-installer-to-operating-system-mapping"></a>Mapeamento do instalador para o sistema operacional do Serviço de Mobilidade

| Nome do modelo do arquivo do instalador| Sistema operacional |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 SP1 (64 bits) </br> Windows Server 2012 (64 bits) </br> Windows Server 2012 R2 (64 bits) </br> Windows Server 2016 (64 bits) |
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| Red Hat Enterprise Linux (RHEL) 6.4, 6.5, 6.6, 6.7, 6.8, 6.9 (somente 64 bits) </br> CentOS 6.4, 6.5, 6.6, 6.7, 6.8, 6.9 (somente 64 bits) |
|Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.1, 7.2, 7.3 (somente 64 bits) </br> CentOS 7.0, 7.1, 7.2, 7.3 (somente 64 bits) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (somente 64 bits)|
|Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 (somente 64 bits)|
|Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5 (somente 64 bits)|
|Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04 (somente 64 bits)|
|Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Servidor do Ubuntu Linux 16.04 LTS (somente 64 bits)|
|Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 (somente 64 bits)|
|Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8 (somente 64 bits)|


## <a name="install-mobility-service-manually-by-using-the-gui"></a>Instalar o Serviço de Mobilidade manualmente usando a GUI

>[!IMPORTANT]
> Se você usar um servidor de configuração para replicar as máquinas virtuais a partir de uma assinatura/região do Azure para outra, use o método de instalação baseado na linha de comando.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>Instalar o Serviço de Mobilidade manualmente em um prompt de comando

### <a name="command-line-installation-on-a-windows-computer"></a>Instalação de linha de comando em um computador Windows
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Instalação de linha de comando em um computador Linux
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Instalar o Serviço de Mobilidade usando a instalação por push por meio do Azure Site Recovery
Você pode fazer uma instalação por push do Serviço de Mobilidade usando o Site Recovery. Todos os computadores de destino devem atender aos seguintes pré-requisitos.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
Após a instalação do Serviço de Mobilidade, no portal do Azure, selecione **+ Replicar** para começar a proteger essas VMs.

## <a name="update-mobility-service"></a>Atualizar serviço de mobilidade

> [!WARNING]
> Certifique-se de que o servidor de configuração, os servidores de processo de expansão e quaisquer servidores de destino que não façam parte da sua implantação sejam atualizados antes de você começar a atualizar o Serviço de Mobilidade nos servidores protegidos.

1. No portal do Azure, navegue até o *nome do seu cofre* > **Modo de exibição Itens replicados**.
2. Se o servidor de configuração já tiver sido atualizado para a versão mais recente, você verá uma notificação que mostra "Nova atualização do agente de replicação de recuperação do site está disponível. Clique para instalar."

     ![Janela Itens replicados](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)
3. Clique na notificação para abrir a página de seleção de máquina virtual.
4. Selecione as máquinas virtuais nas quais deseja atualizar o serviço de mobilidade e selecione **OK**.

     ![Lista de VMs de itens replicados](.\media\vmware-azure-install-mobility-service\update-okpng.png)

O trabalho Atualizar o Serviço de Mobilidade é iniciado para cada uma das máquinas virtuais selecionadas.

> [!NOTE]
> [Leia mais](vmware-azure-manage-configuration-server.md) sobre como atualizar a senha para a conta usada para instalar o Serviço de Mobilidade.

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>Desinstalar o Serviço de Mobilidade de um computador Windows Server
Use um dos métodos a seguir para desinstalar o Serviço de Mobilidade em um computador Windows Server.

### <a name="uninstall-by-using-the-gui"></a>Desinstalar usando a GUI
1. No Painel de Controle, selecione **Programas**.
2. Selecione **Serviço de Mobilidade do Microsoft Azure Site Recovery/servidor de Destino Mestre** e, em seguida, **Desinstalar**.

### <a name="uninstall-at-a-command-prompt"></a>Desinstalar em um prompt de comando
1. Abra uma janela de prompt de comando como administrador.
2. Para desinstalar o Serviço de Mobilidade, execute o seguinte comando:

    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>Desinstalar o Serviço de Mobilidade de um computador Linux
1. No servidor Linux, entre como um usuário **raiz**.
2. Em um terminal, acesse /user/local/ASR.
3. Para desinstalar o Serviço de Mobilidade, execute o seguinte comando:

    ```
    uninstall.sh -Y
    ```
