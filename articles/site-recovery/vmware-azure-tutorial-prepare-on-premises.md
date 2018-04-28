---
title: Preparar os servidores VMware locais para recuperação de desastre das VMs VMware para o Azure | Microsoft Docs
description: Saiba como preparar os servidores VMware locais para recuperação de desastre para o Azure usando o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: f7722891af15111fd0151055c35bf24100ed79b1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Preparar servidores VMware locais para recuperação de desastre para o Azure

Este tutorial mostra como preparar sua infraestrutura do VMware local, quando você deseja replicar VMs VMware para o Azure. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Preparar uma conta no vCenter Server ou no host ESXi do vSphere para automatizar a descoberta de VM
> * Preparar uma conta para a instalação automática do serviço de Mobilidade nas VMs VMware
> * Examinar os requisitos do servidor VMware
> * Examinar os requisitos de VM VMware

Nesta série de tutoriais, mostramos como replicar uma única VM usando o Azure Site Recovery. 

Este é o segundo tutorial da série. Verifique se você [configurou os componentes do Azure](tutorial-prepare-azure.md) conforme foi descrito no tutorial anterior.

Se você estiver replicando várias VMs, baixe a [Ferramenta do Planejador de Implantação](https://aka.ms/asr-deployment-planner) para a replicação do VMware. [Saiba mais](site-recovery-deployment-planner.md).


## <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a descoberta automática

O Site Recovery precisa de acesso aos servidores VMware para:

- Descobrir as VMs automaticamente. Pelo menos uma conta somente leitura é necessária.
- Orquestrar a replicação, o failover e o failback. Você precisa de uma conta que possa executar operações como criar e remover discos e ativar VMs.

Crie a conta da seguinte maneira:

1. Para usar uma conta dedicada, crie uma função no nível do vCenter. Dê um nome à função como **Azure_Site_Recovery**.
2. Atribua à função as permissões resumidas na tabela a seguir.
3. Crie um usuário no vCenter Server ou no host do vSphere. Atribua a função ao usuário.

### <a name="vmware-account-permissions"></a>Permissões de conta do VMware

**Tarefa** | **Função/Permissões** | **Detalhes**
--- | --- | ---
**Descoberta VM** | Pelo menos um usuário somente leitura<br/><br/> Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).
**Replicação, failover, failback totais** |  Crie uma função (Azure_Site_Recovery) com as permissões necessárias e, em seguida, atribua a função a um usuário ou grupo do VMware<br/><br/> Objeto de Data Center –> Propagar para o Objeto Filho, função = Azure_Site_Recovery<br/><br/> Armazenamento de dados -> alocar espaço, procurar armazenamento de dados, operações de arquivo de baixo nível, remover arquivo, atualizar arquivos de máquina virtual<br/><br/> Rede -> Atribuição de rede<br/><br/> Recurso -> Atribuir VM ao pool de recursos, migrar VM desligada, migrar VM ligada<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder à pergunta, conexão de dispositivo, configurar mídia de CD, configurar mídia de disquete, desligar, ligar, instalação de ferramentas VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registrar, cancelar registro<br/><br/> Máquina virtual -> Provisionamento -> Permitir download de máquina virtual, permitir upload de arquivos de máquina virtual<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).

## <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de Mobilidade deve ser instalado na VM que você deseja replicar. O Site Recovery instala esse serviço automaticamente quando você habilita a replicação para a VM. Para instalação automática, você precisa preparar uma conta que o Site Recovery usará para acessar a VM. Você especificará esta conta quando configurar a recuperação de desastre no console do Azure.

1. Prepare um domínio ou uma conta local com permissões para instalar na VM.
2. Para instalar em VMs do Windows, se você não estiver usando uma conta de domínio, desabilite o controle de Acesso de Usuários Remoto no computador local.
   - No Registro, em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy**, com um valor igual 1.
3. Para instalar em VMs do Linux, prepare uma conta raiz no servidor Linux de origem.


## <a name="check-vmware-requirements"></a>Verificar requisitos de VMware

Certifique-se de que os servidores e VMs de VMware estejam em conformidade com os requisitos.

1. [Verifique](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers) os requisitos do servidor VMware.
2. Para o Linux, [verifique](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) requisitos de armazenamento e sistema de arquivos. 
3. Verifique o suporte a [rede](vmware-physical-azure-support-matrix.md#network) local e [armazenamento](vmware-physical-azure-support-matrix.md#storage). 
4. Verifique o que há de suporte para [rede do Azure](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [armazenamento](vmware-physical-azure-support-matrix.md#azure-storage) e [computação](vmware-physical-azure-support-matrix.md#azure-compute) após o failover.
5. Suas VMs locais replicadas no Azure devem atender aos [requisitos de VM do Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Durante um cenário de failover você poderá se conectar da sua rede local às suas VMs replicadas no Azure.

Para se conectar a VMs do Windows usando o RDP após o failover, faça o seguinte:

1. Para acessar a Internet, habilite o RDP na VM local antes do failover. Verifique se as regras de TCP e de UDP foram adicionadas no perfil **Público** e se o RDP é permitido no **Firewall do Windows** > **Aplicativos permitidos** para todos os perfis.
2. Para acessar por meio da VPN site a site, habilite o RDP no computador local. O RDP deve ser permitido no **Firewall do Windows** -> **Aplicativos e recursos permitidos** para redes de **Domínio e Privadas**.
   Verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135). Não deve haver nenhuma atualização pendente do Windows na VM quando você dispara um failover. Se houver, não será possível fazer logon na máquina virtual até que a atualização seja concluída.
3. Na VM do Microsoft Azure após o failover, verifique o **Diagnóstico de inicialização** para exibir uma captura de tela da VM. Se você não puder se conectar, verifique se a VM está em execução e examine estas [dicas de solução de problemas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Para conectar-se a VMs do Linux usando SSH após o failover, faça o seguinte:

1. No computador local antes do failover, verifique se o serviço Secure Shell está definido para iniciar automaticamente na inicialização do sistema. Verifique se as regras de firewall permitem uma conexão SSH.

2. Na VM do Azure após o failover, permita as conexões de entrada com a porta SSH para as regras do grupo de segurança de rede na VM com failover e para a sub-rede do Azure à qual ela está conectada.
   [Adicione um endereço IP público](site-recovery-monitoring-and-troubleshooting.md) para a VM. Você pode verificar o **Diagnóstico de inicialização** para exibir uma captura de tela da VM.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar a recuperação de desastre de VMs VMware para o Azure](vmware-azure-tutorial.md)
