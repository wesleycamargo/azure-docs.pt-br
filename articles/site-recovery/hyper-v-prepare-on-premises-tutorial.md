---
title: Preparar servidores Hyper-V locais para recuperação de desastres de VMs do Hyper-V para o Azure | Microsoft Docs
description: Saiba como preparar VMs do Hyper-V no local para recuperação de desastre no Azure com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 10/28/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5f46f22ab524e1d51b01fcedb4ea1420d755ecbe
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209880"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Preparar servidores Hyper-V locais para recuperação de desastre no Azure

Este tutorial mostra como preparar sua infraestrutura de Hyper-V local quando você deseja replicar VMs Hyper-V para o Azure para fins de recuperação de desastre. Os hosts Hyper-V podem ser gerenciados no System Center Virtual Machine Manager (VMM), mas isso não é obrigatório.  Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Examinar os requisitos do Hyper-V e os requisitos do VMM, se aplicável.
> * Preparar o VMM, se aplicável
> * Verificar o acesso à Internet para locais do Azure
> * Preparar as VMs para que você possa acessá-las após o failover para o Azure

Este é o segundo tutorial da série. Verifique se você [configurou os componentes do Azure](tutorial-prepare-azure.md) conforme foi descrito no tutorial anterior.



## <a name="review-requirements-and-prerequisites"></a>Revisar requisitos e pré-requisitos

Certifique-se de que os hosts do Hyper-V e VMs estejam em conformidade com os requisitos.

1. [Verifique](hyper-v-azure-support-matrix.md#on-premises-servers) os requisitos do servidor local.
2. [Verifique os requisitos](hyper-v-azure-support-matrix.md#replicated-vms) para VMs Hyper-V que você deseja replicar para o Azure.
3. Verifique se a[rede](hyper-v-azure-support-matrix.md#hyper-v-network-configuration) do host Hyper-V; e o [armazenamento](hyper-v-azure-support-matrix.md#hyper-v-host-storage) do host e convidado dão suporte para hosts do Hyper-V local.
4. Verifique o que há de suporte para [rede do Azure](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [armazenamento](hyper-v-azure-support-matrix.md#azure-storage) e [computação](hyper-v-azure-support-matrix.md#azure-compute-features) após o failover.
5. Suas VMs locais replicadas no Azure devem atender aos [requisitos de VM do Azure](hyper-v-azure-support-matrix.md#azure-vm-requirements).


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

1. Para fins do tutorial, a configuração mais simples é que os hosts Hyper-V e o servidor VMM tenham acesso direto à Internet sem usar um proxy. 
2. Verifique se os hosts do Hyper-V e o servidor do VMM, se relevante, podem acessar as URLs necessárias abaixo.   
3. Se você estiver controlando o acesso pelo endereço IP, certifique-se de que:
    - As regras de firewall baseadas em endereço IP podem conectar os [Intervalos IP do Azure Datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).
    - Permita intervalos de endereços IP para a região do Azure de sua assinatura.
    
### <a name="required-urls"></a>URLs necessárias


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Durante um cenário de failover, você poderá se conectar à sua rede local replicada.

Para se conectar a VMs do Windows usando o RDP após o failover, permita o acesso da seguinte maneira:

1. Para acessar a Internet, habilite o RDP na VM local antes do failover. Verifique se as regras de TCP e de UDP foram adicionadas no perfil **Público** e se o RDP é permitido no **Firewall do Windows** > **Aplicativos permitidos** para todos os perfis.
2. Para acessar por meio da VPN site a site, habilite o RDP no computador local. O RDP deve ser permitido no **Firewall do Windows** -> **Aplicativos e recursos permitidos** para redes de **Domínio e Privadas**.
   Verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135). Não deve haver nenhuma atualização pendente do Windows na VM quando você dispara um failover. Se houver, não será possível fazer logon na máquina virtual até que a atualização seja concluída.
3. Na VM do Microsoft Azure após o failover, verifique o **Diagnóstico de inicialização** para exibir uma captura de tela da VM. Se você não puder se conectar, verifique se a VM está em execução e examine estas [dicas de solução de problemas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Após o failover, você pode acessar as VMs do Azure usando o mesmo endereço IP da VM replicada no local ou um endereço IP diferente. [Saiba mais](concepts-on-premises-to-azure-networking.md) sobre como configurar o endereçamento IP para failover.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar a recuperação de desastres do Azure para VMs Hyper-V](tutorial-hyper-v-to-azure.md)
> [Configurar a recuperação de desastres do Azure para VMs Hyper-V em nuvens do VMM](tutorial-hyper-v-vmm-to-azure.md)
