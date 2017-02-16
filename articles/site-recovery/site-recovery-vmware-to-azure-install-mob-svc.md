---
title: "Instalando o Serviço de Mobilidade (VMware/Físico para Azure) | Microsoft Docs"
description: "Este artigo descreve como instalar o Agente de Serviço de Mobilidade nos computadores locais para começar a protegê-los."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 12/9/2016
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 9dfcdeb4dc3e84089eb8031272b870f87d90b689
ms.openlocfilehash: 09a84e5afa4bd462207179c7505bc80400a5a3a5

---

# <a name="installing-mobility-service-vmwarephysical-to-azure"></a>Instalando o Serviço de Mobilidade (VMware/Físico para Azure)
O Serviço de Mobilidade precisa ser implantado em cada computador (VM VMware ou servidor físico) que você quer replicar no Azure. Ele captura gravações de dados no computador e as encaminha ao servidor em processo.  O Serviço de Mobilidade pode ser implantado nos servidores que exigem proteção nos seguintes métodos
1. [Instalar o Serviço de Mobilidade usando as ferramentas de implantação de software, como o System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
2. [Instalar o Serviço de Mobilidade usando a Automação do Azure e a DSC (Configuração de Estado Desejado)](site-recovery-automate-mobility-service-install.md)
3. [Instalar o Serviço de Mobilidade manualmente usando a GUI (Interface Gráfica do Usuário)](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-using-the-graphical-user-interface)
4. [Instalar o Serviço de Mobilidade manualmente usando a linha de comando](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-using-command-line)
5. [Instalar o Serviço de Mobilidade usando a Instalação de Envio por Push no Azure Site Recovery](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-using-push-install-from-azure-site-recovery)

## <a name="prerequisites"></a>Pré-requisitos
Execute estes pré-requisitos antes de começar a instalar manualmente o Serviço de Mobilidade nos servidores.
1. Faça logon no Servidor de Configuração e abra um prompt de comando com privilégios Administrativos.
2. Altere o diretório para a pasta bin e crie um arquivo de senha

  ```
  cd %ProgramData%\ASR\home\svsystems\bin
  genpassphrase.exe -v > MobSvc.passphrase
  ```
3. Armazene esse arquivo em um local seguro, pois precisaremos usá-lo durante a instalação do Serviço de Mobilidade.
4. Os instaladores do Serviço de Mobilidade para todos os sistemas operacionais compatíveis podem ser encontrados no diretório     

  `%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

#### <a name="mobility-service-installer-to-operating-system-mapping"></a>Mapeamento do instalador do Serviço de Mobilidade para o sistema operacional

| Nome do modelo do arquivo do instalador| Sistema operacional |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 (64 bits) SP1</br> Windows Server 2012 (64 bits) </br> Windows Server 2012 R2 (64 bits) |
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| RHEL 6.4, 6.5, 6.6 (somente 64 bits) </br> CentOS 6.4, 6.5, 6.6 (somente 64 bits) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (somente 64 bits)|
|Microsoft-ASR_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5 (somente 64 bits)|
|Microsoft-ASR_UA\*Ubuntu-14.04-64\*release.tar.gz | Ubuntu 14.04 (somente 64 bits)|

## <a name="install-mobility-service-manually-using-the-graphical-user-interface"></a>Instalar o Serviço de Mobilidade manualmente usando a Interface Gráfica do Usuário

>[!NOTE]
> A instalação baseada na Interface Gráfica do Usuário só é compatível com sistemas operacionais Microsoft Windows.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-using-command-line"></a>Instalar o Serviço de Mobilidade manualmente usando a linha de comando
### <a name="command-line-based-install-on-windows-computers"></a>Instalação baseada na linha de comando em computadores Windows
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-based-install-on-linux-computers"></a>Instalação baseada na linha de comando em computadores Linux
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-using-push-install-from-azure-site-recovery"></a>Instalar o Serviço de Mobilidade usando a Instalação de Envio por Push no Azure Site Recovery
Para poder executar a instalação de envio por push do Serviço de Mobilidade usando o Azure Site Recovery, você precisa garantir que os pré-requisitos a seguir sejam atendidos em todos os computadores de destino.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


## <a name="next-steps"></a>Próximas etapas
Depois de instalar o Serviço de Mobilidade, você poderá usar o botão **+Replicar** no Portal do Azure para começar a proteger essas VMs.



<!--HONumber=Jan17_HO2-->


