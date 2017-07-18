---
title: "Planejar a rede para a replicação de servidor físico para o Azure | Microsoft Docs"
description: "Este artigo aborda o planejamento de rede necessário ao replicar servidores físicos para o Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 71db2435-b5ce-4263-83f6-093d10d1d4e1
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: f8a20b45b50f71631122e574b634818c1912f12e
ms.contentlocale: pt-br
ms.lasthandoff: 06/29/2017


---

# <a name="step-4-plan-networking-for-physical-server-replication-to-azure"></a>Etapa 4: Planejar a rede para a replicação de servidor físico para o Azure

Este artigo resume as considerações sobre planejamento de rede ao replicar servidores físicos locais para o Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md).

Publique eventuais comentários no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="connect-to-replica-azure-vms"></a>Conectar-se às VMs de réplica do Azure

Ao planejar sua estratégia de replicação e failover, uma das principais questões é como se conectar à VM do Azure após o failover. Há algumas opções ao criar sua estratégia de rede para VMs de réplica do Azure:

- **Usar outro endereço IP**: você pode optar por usar outro intervalo de endereços IP para a rede VM replicada do Azure. Nesse cenário, o computador obtém um novo endereço IP após o failover e uma atualização de DNS é necessária.
- **Usar o mesmo endereço IP**: talvez você deseje usar o mesmo intervalo de endereços IP do site primário local para a rede do Azure após o failover. Manter os mesmos endereços IP simplifica a recuperação, reduzindo problemas relacionados à rede após o failover. No entanto, quando você estiver replicando para o Azure, precisará atualizar as rotas com o novo local dos endereços IP após o failover.

## <a name="retain-ip-addresses"></a>Reter os endereços IP

O Site Recovery fornece a capacidade de reter endereços IP fixos durante o failover para o Azure, com um failover de sub-rede.
Com o failover de sub-rede, uma sub-rede específica está presente no Site 1 ou Site 2, mas nunca em ambos os sites ao mesmo tempo. Para manter o espaço de endereço IP no caso de um failover, de forma programática, faça com que a infraestrutura do roteador mova as sub-redes de um site para outro. Durante o failover, as sub-redes são movidas com as VMs protegidas associadas. A principal desvantagem é que, em caso de falha, você precisa mover toda a sub-rede.

### <a name="failover-example"></a>Exemplo de failover

Vejamos um exemplo de failover para o Azure.

- Uma organização fictícia, o Woodgrove Bank, tem uma infraestrutura local que hospeda seus aplicativos de negócios. Seus aplicativos móveis são hospedados no Azure.
- A conectividade entre as VMs do Woodgrove Bank no Azure e os servidores locais é fornecida por uma conexão (VPN) site a site entre a rede de borda local e a rede virtual do Azure.
- Essa VPN significa que a rede virtual da empresa no Azure aparece como uma extensão de sua rede local.
- O Woodgrove deseja usar o Site Recovery para replicar as cargas de trabalho locais para o Azure.
 - O Woodgrove precisa lidar com aplicativos e configurações que dependem de endereços IP embutidos em código e, portanto, precisa reter endereços IP para seus aplicativos após o failover para o Azure.
 - O Woodgrove atribuiu endereços IP do intervalo 172.16.1.0/24 a 172.16.2.0/24 aos seus recursos em execução no Azure.


Para que o Woodgrove consiga replicar seus servidores para o Azure, ao mesmo tempo que retém os endereços IP, veja o que a empresa precisa fazer:

1. Crie uma rede virtual do Azure. Ela deve ser uma extensão da rede local, de forma que os aplicativos possam fazer failover de forma ininterrupta.
2. O Azure permite que você adicione a conectividade VPN site a site, além da conectividade ponto a site, às redes virtuais criadas no Azure.
3. Ao configurar a conexão site a site, na rede do Azure, você poderá encaminhar o tráfego para a localização local (rede local) somente se o intervalo de endereços IP for diferente do intervalo de endereços IP local.
    - Isso ocorre porque o Azure não dá suporte a sub-redes ampliadas. Portanto, se você tiver uma sub-rede 192.168.1.0/24 local, não poderá adicionar uma rede local 192.168.1.0/24 à rede do Azure.
    - Isso é esperado, porque o Azure não reconhece que não há nenhum computador ativo na sub-rede e que a sub-rede está sendo criada apenas para fins de recuperação de desastre.
    - Para poder encaminhar o tráfego de rede corretamente para fora de uma rede do Azure, as sub-redes na rede e a rede local não devem entrar em conflito.

![Antes do failover da sub-rede](./media/physical-walkthrough-network/network-design7.png)

#### <a name="before-failover"></a>Antes do failover

1. Crie uma rede adicional (por exemplo, uma Rede de Recuperação). Essa é a rede na qual as VMs com failover são criadas.
2. Para garantir que o endereço IP de um computador é retido após um failover, nas propriedades do computador > **Configurar**, especifique o mesmo endereço IP que o servidor tem local e clique em **Salvar**.
3. Quando o computador passar por failover, o Azure Site Recovery atribuirá o endereço IP fornecido a ele.
4. Depois que o failover for disparado e as VMs forem criadas no Azure com o endereço IP solicitado, você poderá se conectar à rede usando uma [conexão Vnet a Vnet](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Essa ação pode ser inserida em um script.
5. As rotas precisam ser modificadas de maneira apropriada, para refletir que 192.168.1.0/24 agora foi movido para o Azure.

    ![Após o failover da sub-rede](./media/physical-walkthrough-network/network-design9.png)

#### <a name="after-failover"></a>Após o failover

Caso você não tenha uma rede do Azure, conforme ilustrado acima, poderá criar uma conexão VPN site a site entre o site primário e o Azure, após o failover.

## <a name="change-ip-addresses"></a>Alterar os endereços IP

Esta [postagem no blog](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) explica como configurar a infraestrutura de rede do Azure quando você não precisa reter os endereços IP após o failover. Ela começa com uma descrição do aplicativo, explica como configurar a rede local e no Azure e termina com informações sobre como executar failovers.  

## <a name="next-steps"></a>Próximas etapas

Ir para a [Etapa 5: Preparar o Azure](physical-walkthrough-prepare-azure.md)

