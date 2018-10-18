---
title: Redes virtuais de mapas entre duas regiões do Azure no Azure Site Recovery | Microsoft Docs
description: O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. Saiba mais sobre o failover no Azure ou em um datacenter secundário.
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
editor: ''
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: 95e6a388d0638d2fd477d33aaf7c39cf120e29aa
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353417"
---
# <a name="map-virtual-networks-in-different-azure-regions"></a>Redes virtuais de mapas em regiões do Azure diferentes


Este artigo descreve como mapear duas instâncias da Rede Virtual do Azure localizadas em regiões do Azure diferentes entre si. O mapeamento de rede garante que quando uma máquina virtual replicada for criada na região do Azure de destino, ela também será criada em uma rede virtual que é mapeada para a rede virtual da máquina virtual de origem.  

## <a name="prerequisites"></a>Pré-requisitos
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

### <a name="ip-assignment-behavior-during-failover"></a>Comportamento de atribuição de IP durante o Failover
#### <a name="1-same-address-space"></a>1. Mesmo espaço de endereço

Se a sub-rede de origem e a sub-rede de destino tiverem o mesmo espaço de endereço, o endereço IP da interface de rede da máquina virtual de origem será definido como o endereço IP de destino. Se o mesmo endereço IP não estiver disponível, o próximo endereço IP disponível será definido como o endereço IP de destino.

#### <a name="2-different-address-spaces"></a>2. Espaços de endereços diferentes

Se a sub-rede de origem e a sub-rede de destino tiverem espaços de endereço diferentes, o próximo endereço IP disponível na sub-rede de destino será definido como o endereço IP de destino.


### <a name="ip-assignment-behavior-during-test-failover"></a>Comportamento de atribuição de IP durante o Failover de Teste
#### <a name="1-if-the-target-network-chosen-is-the-production-vnet"></a>1. Se a rede de destino escolhida for a vNet de produção
- O IP de recuperação (IP de destino) será um endereço IP estático, mas ele **não será o mesmo endereço IP** como reservado para Failover.
- O endereço IP atribuído será o próximo IP disponível a partir do final do intervalo de endereços de sub-rede.
- Por exemplo, se o IP estático da VM de origem for configurado para ser: 10.0.0.19 e Failover de Teste tiver sido tentado com a rede de produção configurada: ***dr-PROD-nw***, com um intervalo de sub-rede como 10.0.0.0/24. </br>
A VM de failover seria atribuída com - O próximo IP disponível do final do intervalo de endereços de sub-rede é: 10.0.0.254 </br>

**Observação:** a terminologia **vNet de produção** é chamada de “Rede de destino” mapeada durante a configuração de recuperação de desastre.
#### <a name="2-if-the-target-network-chosen-is-not-the-production-vnet-but-has-the-same-subnet-range-as-production-network"></a>2. Se a rede de destino escolhida não é vNet de produção, mas tiver o mesmo intervalo de sub-rede que a rede de produção

- O IP de recuperação (IP de Destino) será um endereço IP estático com o **mesmo endereço IP** (ou seja, endereço IP estático configurado) como reservado para Failover. Desde que o mesmo endereço IP esteja disponível.
- Se o endereço IP estático configurado já estiver atribuído a algum outro dispositivo/VM, o IP de recuperação será o próximo IP disponível a partir do final do intervalo de endereços de sub-rede.
- Por exemplo, se o IP estático da VM de origem for configurado para ser: 10.0.0.19 e Failover de Teste tiver sido tentado com uma rede de teste: ***dr-PROD-nw***, com o mesmo intervalo de sub-rede da rede de produção - 10.0.0.0/24. </br>
  A VM de failover seria atribuída com o seguinte endereço IP estático </br>
    - IP estático configurado: 10.0.0.19 se IP estiver disponível.
    - Próximo IP disponível: 10.0.0.254 se o endereço IP 10.0.0.19 já estiver em uso.


Para modificar o IP de destino em cada interface de rede, vá para as configurações de **Computação e Rede** da máquina virtual.</br>
Como melhor prática, é sempre aconselhável escolher uma rede de teste para executar o Failover de Teste.
## <a name="next-steps"></a>Próximas etapas

* Analise as [diretrizes de rede para replicar máquinas virtuais do Azure](site-recovery-azure-to-azure-networking-guidance.md).
