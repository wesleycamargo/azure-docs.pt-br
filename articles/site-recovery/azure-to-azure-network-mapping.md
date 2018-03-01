---
title: "Redes virtuais de mapas entre duas regiões do Azure no Azure Site Recovery | Microsoft Docs"
description: "O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. Saiba mais sobre o failover no Azure ou em um datacenter secundário."
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 8f347827c640729112e2e8f4c11288b6bcb176ea
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="map-virtual-networks-in-different-azure-regions"></a>Redes virtuais de mapas em regiões do Azure diferentes


Este artigo descreve como mapear duas instâncias da Rede Virtual do Azure localizadas em regiões do Azure diferentes entre si. O mapeamento de rede garante que quando uma máquina virtual replicada for criada na região do Azure de destino, ela também será criada em uma rede virtual que é mapeada para a rede virtual da máquina virtual de origem.  

## <a name="prerequisites"></a>pré-requisitos
Antes de mapear as redes, verifique se você criou uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md) nas regiões do Azure de origem e de destino.

## <a name="map-virtual-networks"></a>Mapear redes virtuais

Para mapear uma rede virtual do Azure localizada em uma região do Azure (rede de origem) para uma rede virtual localizada em outra região (rede de destino), para máquinas virtuais do Azure, vá para **Infraestrutura do Site Recovery** > **Mapeamento de Rede**. Crie um mapeamento de rede.

![Janela de mapeamentos de rede - Criar um mapeamento de rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


No exemplo a seguir, a máquina virtual está em execução na região do Pacífico Asiático. A máquina virtual está sendo replicada para a região do Sudeste Asiático.

Para criar um mapeamento de rede da região do Pacífico Asiático para a região do Sudeste Asiático, selecione o local da rede de origem e o local da rede de destino. Depois, selecione **OK**.

![Adicionar janela de mapeamento de rede - Selecionar os locais de origem e destino para a rede de origem](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Repita o processo anterior para criar um mapeamento de rede da região Sudeste Asiático para a região Leste da Ásia.

![Adicionar painel de mapeamento de rede - Selecionar os locais de origem e destino para a rede de destino](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-a-network-when-you-enable-replication"></a>Mapear uma rede quando você habilita a replicação

Ao replicar uma máquina virtual de uma região do Azure para outra região pela primeira vez, não se existir nenhum mapeamento de rede, você poderá definir a rede de destino ao configurar a replicação. Com base nessa configuração, o Azure Site Recovery cria mapeamentos de rede a partir da região de origem para a região de destino e a partir da região de destino para a região de origem.   

![Configurar painel de configurações - Escolher o local de destino](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

Por padrão, o Site Recovery cria uma rede na região de destino que é idêntica à rede de origem. O Site Recovery cria uma rede adicionando **-asr** como um sufixo ao nome de rede de origem. Para escolher uma rede que já tenha sido criada, selecione **Personalizar**.

![Personalizar painel de configurações de destino - Definir nome do grupo de recursos de destino e nome da rede virtual de destino](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)

Se o mapeamento de rede já tiver ocorrido, você não poderá alterar a rede virtual de destino ao habilitar a replicação. Neste caso, para alterar a rede virtual de destino, modifique o mapeamento de rede existente.  

![Personalizar painel de configurações de destino - Definir o nome do grupo de recursos de destino](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Modificar painel de mapeamento de rede - Modificar um nome de rede virtual de destino existente](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> Se você modificar um mapeamento de rede da região A para a região B, verifique se modificou também o mapeamento de rede da região B para a região A.
>
>


## <a name="subnet-selection"></a>Seleção de sub-rede
A sub-rede da máquina virtual de destino é selecionada de acordo com o nome da sub-rede da máquina virtual de origem. Se houver uma sub-rede com o mesmo nome da máquina virtual de origem disponível na rede de destino, essa sub-rede será escolhida para a máquina virtual de destino. Se não houver nenhuma sub-rede com o mesmo nome na rede de destino, a primeira sub-rede em ordem alfabética será escolhida como a sub-rede de destino.

Para modificar a sub-rede, vá até as configurações **Computação e Rede** da máquina virtual.

![Janela de propriedades de Computação e Rede](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>Endereço IP

O endereço IP de cada interface de rede da máquina virtual de destino é tido como descrito nas seções a seguir.

### <a name="dhcp"></a>DHCP
Se a interface de rede da máquina virtual de origem estiver usando DHCP, a interface de rede da máquina virtual de destino também será definida como usando DHCP.

### <a name="static-ip-address"></a>Endereço IP estático
Se a interface de rede da máquina virtual de origem estiver usando um endereço IP estático, a interface de rede da máquina virtual de destino também será definida como usando um endereço IP estático. As seções a seguir descrevem como um endereço IP estático é definido.

#### <a name="same-address-space"></a>Mesmo espaço de endereço

Se a sub-rede de origem e a sub-rede de destino tiverem o mesmo espaço de endereço, o endereço IP da interface de rede da máquina virtual de origem será definido como o endereço IP de destino. Se o mesmo endereço IP não estiver disponível, o próximo endereço IP disponível será definido como o endereço IP de destino.

#### <a name="different-address-spaces"></a>Espaços de endereços diferentes

Se a sub-rede de origem e a sub-rede de destino tiverem espaços de endereço diferentes, o próximo endereço IP disponível na sub-rede de destino será definido como o endereço IP de destino.

Para modificar o IP de destino em cada interface de rede, vá para as configurações de **Computação e Rede** da máquina virtual.

## <a name="next-steps"></a>Próximas etapas

* Analise as [diretrizes de rede para replicar máquinas virtuais do Azure](site-recovery-azure-to-azure-networking-guidance.md).
