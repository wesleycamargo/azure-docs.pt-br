---
title: "Configurar a recuperação de desastre em VMs do Azure após migração para o Azure com o Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve como preparar computadores para configurar a recuperação de desastre entre regiões do Azure após a migração para o Azure usando o Azure Site Recovery."
services: site-recovery
author: ponatara
ms.service: site-recovery
ms.topic: article
ms.date: 01/07/2018
ms.author: ponatara
ms.openlocfilehash: c06af21cd6e273b98c004e8bd0e6eac61ba7d644
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Configurar a recuperação de desastres para VMs do Azure após a migração para o Azure 

>[!NOTE]
> A recuperação de desastres para VMs do Azure usando o Azure Site Recovery está atualmente em versão prévia.

Use este artigo depois de [migrar computadores locais para VMs do Azure](tutorial-migrate-on-premises-to-azure.md) usando o serviço [Site Recovery](site-recovery-overview.md). Este artigo ajuda você a preparar as VMs do Azure para configurar a recuperação de desastres em uma região do Azure secundária, usando o Site Recovery.



## <a name="before-you-start"></a>Antes de começar

Antes de configurar a recuperação de desastres, certifique-se de que a migração foi concluída conforme o esperado. Para concluir uma migração com êxito, após o failover, você deve selecionar a opção **Concluir a migração** para cada computador que você deseja migrar. 



## <a name="install-the-azure-vm-agent"></a>Instalar o Agente de VM do Azure

O [agente de VM](../virtual-machines/windows/agent-user-guide.md) do Azure deve ser instalado na VM para que o Site Recovery possa replicá-lo.


1. Para instalar o agente de VM em VMs executando o Windows, baixe e execute o [instalador do agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisa de privilégios de administrador na VM para concluir a instalação.
2. Para instalar o agente de VM em VMs executando o Linux, instale a versão mais recente do [agente do Linux](../virtual-machines/linux/agent-user-guide.md). Você precisa de privilégios de administrador para concluir a instalação. É recomendável instalar do seu repositório de distribuição. Não é recomendável instalar o agente de VM Linux diretamente do GitHub. 


## <a name="validate-the-installation-on-windows-vms"></a>Validar a instalação em VMs do Windows

1. Na VM do Azure, na pasta C:\WindowsAzure\Packages, você deve ver o arquivo WaAppAgent.exe.
2. Clique com o botão direito do mouse no arquivo e em **Propriedades**, selecione a guia **Detalhes**.
3. Verifique se o campo **Versão do Produto** mostra 2.6.1198.718 ou mais recente.



## <a name="migration-from-vmware-vms-or-physical-servers"></a>Migração de VMs VMware ou servidores físicos

Se você migrar VMs VMware locais (ou servidores físicos) ao Azure, observe que:

- Você só precisa instalar o agente da VM do Azure se o serviço de Mobilidade instalado no computador migrado for versão 9.6 ou anterior.
- Em VMs Windows que executam a versão 9.7.0.0 ou mais recente do serviço Mobilidade, o instalador do serviço instala o último agente da VM do Azure disponível. Ao migrar, essas VMs já atendem aos pré-requisitos de instalação do agente para qualquer extensão da VM, incluindo a extensão do Site Recovery.
- Você precisa desinstalar manualmente o serviço de Mobilidade da VM do Azure, usando um dos métodos a seguir. Reinicie a máquina virtual antes de configurar a replicação.
    - Para Windows, no Painel de controle > **Adicionar ou remover programas**, desinstale **Serviço de Mobilidade do Microsoft Azure Site Recovery/Servidor de destino mestre**. Em um prompt de comandos com privilégios elevados, execute:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Para o Linux, entre como usuário raiz. Em um terminal, vá para **/user/local/ASR** e execute o seguinte comando:
        ```
        uninstall.sh -Y
        ```


## <a name="next-steps"></a>Próximas etapas

[Replicar rapidamente](azure-to-azure-quickstart.md) uma VM do Azure para uma região secundária.
