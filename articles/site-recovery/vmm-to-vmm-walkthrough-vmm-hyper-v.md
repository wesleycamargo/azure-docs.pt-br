---
title: "Configurar o VMM e o Hyper-V para a replicação para um site secundário com o Azure Site Recovery | Microsoft Docs"
description: "Descreve como configurar servidores do System Center VMM e hosts do Hyper-V para replicação para um site secundário do VMM."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d0389e3b-3737-496c-bda6-77152264dd98
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 73bd1790c56ac52166f638de2e80c2a2cfb87e53
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2017
---
# <a name="step-4-set-up-vmm-and-hyper-v-for-hyper-v-vm-replication-to-a-secondary-site"></a>Etapa 4: Configurar o VMM e o Hyper-V para replicação de VM do Hyper-V para um site secundário 

Depois que você estiver preparado para a rede, configure servidores do System Center Virtual Machine Manager (VMM) e hosts do Hyper-V para replicação de máquina virtual (VM) do Hyper-V para um site secundário, usando o [Azure Site Recovery](site-recovery-overview.md) no portal do Azure. 

Depois de ler este artigo, poste comentários na parte inferior ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="prepare-vmm-servers"></a>Preparar servidores VMM 

Para se preparar para a implantação:


1. Verifique se os servidores do VMM está em conformidade com os [requisitos de suporte](site-recovery-support-matrix-to-sec-site.md#on-premises-servers) e os [pré-requisitos de implantação](vmm-to-vmm-walkthrough-prerequisites.md).
2. Verifique se os servidores do VMM estão conectados à Internet e têm acesso a essas URLs.
    
    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Se tiver regras de firewall baseadas no endereço IP, verifique se elas permitem a comunicação com o Azure.
    - Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).
    - Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usados para Controle de Acesso e Gerenciamento de Identidade).
3. Verifique se o servidor do VMM está [preparado para mapeamento de rede](vmm-to-vmm-walkthrough-network.md#prepare-for-network-mapping)


## <a name="prepare-hyper-v-hostsclusters"></a>Preparar os hosts/clusters do Hyper-V

1. Verifique se os hosts/clusters do Hyper-V estão em conformidade com os [requisitos de suporte](site-recovery-support-matrix-to-sec-site.md#on-premises-servers) e os [pré-requisitos de implantação](vmm-to-vmm-walkthrough-prerequisites.md).
2. Verifique os requisitos para [VMs do Hyper-V](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions)
3. Verifique os requisitos de [rede](site-recovery-support-matrix-to-sec-site.md#network-configuration) e de [armazenamento](site-recovery-support-matrix-to-sec-site.md#storage).
4. Verifique se os hosts do Hyper-V estão conectados à internet e têm acesso a essas URLs.
    
    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Se tiver regras de firewall baseadas no endereço IP, verifique se elas permitem a comunicação com o Azure.
    - Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).
    - Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usados para Controle de Acesso e Gerenciamento de Identidade).

## <a name="prepare-for-single-server-deployment"></a>Preparar para a implantação de servidor único


Se você tiver apenas um único servidor VMM, será possível replicar VMs em hosts Hyper-V na nuvem do VMM para o [Azure](hyper-v-site-walkthrough-overview.md) ou para uma nuvem secundária do VMM, como descrito neste documento. Recomendamos a primeira opção porque replicar entre nuvens não é perfeita.

Se você quiser replicar entre nuvens, você pode replicar com um único servidor VMM autônomo ou com um único servidor VMM implantado em um cluster estendido do Windows

### <a name="replicate-with-a-standalone-vmm-server"></a>Replicar com um servidor VMM autônomo

Neste cenário, você implanta um único servidor VMM como uma máquina virtual no site primário e replica essa VM para um site secundário com o Site Recovery e a Réplica do Hyper-V.

1. **Configure o VMM em uma VM Hyper-V**. Sugerimos que você coloque a instância do SQL Server usada pelo VMM na mesma VM. Isso economiza tempo, pois apenas uma VM deverá ser criada. Se você quiser usar uma instância remota do SQL Server e uma paralisação ocorrer, precisará recuperar essa instância antes de recuperar o VMM.
2. **Garanta que o servidor VMM tenha pelo menos duas nuvens configuradas**. Uma nuvem conterá as máquinas virtuais que deseja replicar e outra nuvem servirá como o local secundário. A nuvem que contém as VMs que você deseja proteger deve estar de acordo com os [pré-requisitos](#prerequisites).
3. Configure a Recuperação de Site conforme descrito neste artigo. Crie e registre o servidor VMM em um cofre, configure uma política de replicação e habilite a replicação. Os nomes VMM de origem e de destino será o mesmo. Especifique que a replicação inicial ocorrerá pela rede.
4. Ao configurar o mapeamento de rede, você vai mapear a rede da VM para a nuvem primária para a rede da VM para a nuvem secundária.
5. No console do Gerenciador do Hyper-V, habilite a Réplica do Hyper-V no host Hyper-V que contém a VM do VMM e habilite a replicação na VM. Certifique-se de que você não adicione a máquina virtual do VMM às nuvens que são protegidas pela Recuperação de Site, para garantir que as configurações de réplica do Hyper-V não sejam substituídas pela Recuperação de Site.
6. Se você criar planos de recuperação para failover, usará o mesmo servidor VMM para origem e destino.
7. Uma falha completa, failover e recuperação da seguinte maneira:

   1. Execute um failover não planejado no console do Gerenciador do Hyper-V no site secundário para fazer o failover da VM do VMM primário para o site secundário.
   2. Verifique se que a VM do VMM está funcionando e em execução e no cofre, execute um failover não planejado failover as VMs de nuvens primários para o secundário. Confirmar o failover e selecione um ponto de recuperação alternativo, se necessário.
   3. Após a conclusão do failover não planejado, todos os recursos poderão ser acessados no site principal novamente.
   4. Quando o site primário estiver disponível novamente, no console do Gerenciador do Hyper-V no site secundário, habilite a replicação inversa para a VM do VMM. Isso inicia a replicação para a VM de secundário para primário.
   5. Execute um failover planejado no console do Gerenciador do Hyper-V no site secundário, failover VM VMM para o site primário. Confirme o failover. Em seguida, habilite a replicação inversa, para que a VM VMM está replicando novamente do principal para o secundário.
   6. No cofre de Serviços de Recuperação, habilite a replicação inversa para as máquinas virtuais da carga de trabalho, para iniciar a replicação de secundário para primário.
   7. No cofre de Serviços de Recuperação, execute um failover planejado para realizar failback das máquinas virtuais da carga de trabalho para o site primário. Confirme o failover para concluí-lo. Em seguida, habilite a replicação inversa para iniciar a replicação das máquinas virtuais da carga de trabalho do primário para o secundário.

### <a name="replicate-with-a-stretched-vmm-cluster"></a>Replicar com um cluster VMM ampliado

Em vez de implantar um servidor VMM autônomo como uma máquina virtual que é replicada para um site secundário, você pode tornar o VMM altamente disponível, implantando-o como uma VM em um cluster de failover do Windows. Isso fornece resiliência de carga de trabalho e proteção contra falhas de hardware. Para implantar com Recuperação de Site, a VM de VMM deve ser implantada em um cluster estendido em sites geograficamente separados. Para fazer isso:

1. Instale o VMM em uma máquina virtual em um cluster de failover do Windows e selecione a opção para executar o servidor como altamente disponível durante a instalação.
2. A instância do SQL Server usada pelo VMM deve ser replicada com grupos de disponibilidade AlwaysOn do SQL Server para que haja uma réplica do banco de dados no site secundário.
3. Siga as instruções deste artigo para criar um cofre, registrar o servidor e configurar a proteção. Você precisa registrar cada servidor VMM no cluster no cofre de Serviços de Recuperação. Para fazer isso, você deve instala o provedor em um nó ativo e registrar o servidor VMM. Em seguida, instale o Provedor nos outros nós.
4. Quando ocorre uma interrupção, o servidor VMM e seu banco de dados SQL Server correspondente passam por failover e são acessados por meio do site secundário.



## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 5: Configurar um cofre](vmm-to-vmm-walkthrough-create-vault.md).