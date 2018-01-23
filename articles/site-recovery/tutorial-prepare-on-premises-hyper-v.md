---
title: "Preparar os servidores Hyper-V locais para recuperação de desastre das VMs Hyper-V para o Azure | Microsoft Docs"
description: "Saiba como preparar VMs Hyper-V locais não gerenciadas pelo System Center VMM para recuperação de desastre no Azure com o serviço Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6e2837341e16f5077cfff18d4a34c097c165ef09
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Preparar servidores Hyper-V locais para recuperação de desastre no Azure

Este tutorial mostra como preparar sua infraestrutura de Hyper-V local quando você deseja replicar VMs Hyper-V para o Azure para fins de recuperação de desastre. Os hosts Hyper-V podem ser gerenciados no System Center Virtual Machine Manager (VMM), mas isso não é obrigatório.  Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Examinar os requisitos do Hyper-V e os requisitos do VMM, se aplicável.
> * Preparar o VMM, se aplicável
> * Verificar o acesso à Internet para locais do Azure
> * Preparar as VMs para que você possa acessá-las após o failover para o Azure

Este é o segundo tutorial da série. Verifique se você [configurou os componentes do Azure](tutorial-prepare-azure.md) conforme foi descrito no tutorial anterior.



## <a name="review-server-requirements"></a>Examine os requisitos do servidor

Verifique se os servidores Hyper-V atendem aos seguintes requisitos. Se estiver gerenciando hosts em nuvens do System Center Virtual Machine Manager (VMM), verifique os requisitos do VMM.


**Componente** | **Hyper-V gerenciado pelo VMM** | **Hyper-V sem VMM**
--- | --- | ---
**Sistema operacional host do Hyper-V** | Windows Server 2016, 2012 R2 | ND
**VMM** | VMM 2012, VMM 2012 R2 | ND


## <a name="review-hyper-v-vm-requirements"></a>Examine os requisitos de VM do Hyper-V

Verifique se a VM está em conformidade com os requisitos resumidos na tabela.

**Requisitos de VM** | **Detalhes**
--- | ---
**Sistema operacional convidado** | Qualquer SO convidado [compatível com o Azure](https://technet.microsoft.com/library/cc794868.aspx).
**Requisitos do Azure** | VMs Hyper-V locais devem atender aos requisitos de VM do Azure (site-recovery-support-matrix-to-azure.md).

## <a name="prepare-vmm-optional"></a>Preparar o VMM (opcional)

Se os hosts do Hyper-V forem gerenciados pelo VMM, você precisará preparar o servidor do VMM localmente. 

- Verifique se o servidor do VMM tem pelo menos uma nuvem, com um ou mais grupos de host. O host do Hyper-V no qual as VMs estão em execução deve estar localizado na nuvem.
- Prepare o servidor do VMM para o mapeamento de rede.

### <a name="prepare-vmm-for-network-mapping"></a>Preparar VMM para mapeamento de rede

Se você estiver usando o VMM, o [mapeamento de rede](site-recovery-network-mapping.md) mapeará entre as redes de VM do VMM local e as redes virtuais do Azure. O mapeamento garante que as VMs do Azure serão conectadas à rede certa quando elas forem criadas após o failover.

Prepare o VMM para o mapeamento de rede da seguinte maneira:

1. Verifique se você tem uma [rede lógica do VMM](https://docs.microsoft.com/system-center/vmm/network-logical) associada à nuvem em que os hosts do Hyper-V estão localizados.
2. Verifique se você tem uma [rede de VM](https://docs.microsoft.com/system-center/vmm/network-virtual) vinculada à rede lógica.
3. No VMM, conecte as VMs à rede de VM.

## <a name="verify-internet-access"></a>Verifique o acesso à Internet

1. Para fins do tutorial, a configuração mais simples é que os hosts Hyper-V e o servidor do VMM, se aplicável, tenham acesso direto à Internet sem usar um proxy. 
2. Verifique se os hosts Hyper-V e o servidor do VMM, se for relevante, podem acessar estas URLs: 

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
3. Verifique se:
    - Qualquer regra de firewall baseada em endereço IP deve permitir a comunicação com o Azure.
    - Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).
    - Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usados para Controle de Acesso e Gerenciamento de Identidade).


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Durante um cenário de failover, você poderá se conectar à sua rede local replicada.

Para se conectar a VMs do Windows usando o RDP após o failover, faça o seguinte:

1. Para acessar a Internet, habilite o RDP na VM local antes do failover. Verifique se as regras de TCP e de UDP foram adicionadas no perfil **Público** e se o RDP é permitido no **Firewall do Windows** > **Aplicativos permitidos** para todos os perfis.
2. Para acessar por meio da VPN site a site, habilite o RDP no computador local. O RDP deve ser permitido no **Firewall do Windows** -> **Aplicativos e recursos permitidos** para redes de **Domínio e Privadas**.
   Verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135). Não deve haver nenhuma atualização pendente do Windows na VM quando você dispara um failover. Se houver, não será possível fazer logon na máquina virtual até que a atualização seja concluída.
3. Na VM do Microsoft Azure após o failover, verifique o **Diagnóstico de inicialização** para exibir uma captura de tela da VM. Se você não puder se conectar, verifique se a VM está em execução e examine estas [dicas de solução de problemas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar a recuperação de desastres do Azure para VMs Hyper-V](tutorial-hyper-v-to-azure.md)
> [Configurar a recuperação de desastres do Azure para VMs Hyper-V em nuvens do VMM](tutorial-hyper-v-vmm-to-azure.md)
