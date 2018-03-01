---
title: "Gerenciar adaptadores de rede no Azure Site Recovery para cenários locais para o Azure | Microsoft Docs"
description: "Descreve como gerenciar adaptadores de rede para cenários locais para o Azure com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: ab8582d9c32cf13bd7b21a59031af8fde58effbf
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-to-azure-scenarios"></a>Gerenciar adaptadores de rede de máquina virtual para cenários locais para o Azure

Uma VM (máquina virtual) no Azure deve ter pelo menos um adaptador de rede anexado. Ela pode ter tantos adaptadores de rede anexados quantos o tamanho da VM der suporte.

Por padrão, o primeiro adaptador de rede anexado a uma máquina virtual do Azure é definido como o adaptador de rede primário. Todos os outros adaptadores de rede na máquina virtual são adaptadores de rede secundários. Também por padrão, todo o tráfego de saída da máquina virtual é enviado pelo endereço IP atribuído à configuração de IP primária do adaptador de rede primário.

Em um ambiente local, servidores ou máquinas virtuais podem ter vários adaptadores de rede para redes diferentes dentro do ambiente. Redes diferentes normalmente são usadas para executar operações específicas, como atualizações, manutenção e acesso à Internet. Quando estiver migrando ou fazendo failover para o Azure de um ambiente local, tenha em mente que os adaptadores de rede na mesma máquina virtual devem estar todos conectadas à mesma rede virtual.

Por padrão, o Azure Site Recovery cria tantos adaptadores de rede em uma máquina virtual do Azure quantos estão conectados ao servidor local. Você pode evitar a criação de adaptadores de rede redundantes durante a migração ou failover editando as configurações de adaptador de rede nas configurações da máquina virtual replicada.

## <a name="select-the-target-network"></a>Selecionar a rede de destino

Para o VMware e computadores físicos e para máquinas virtuais Hyper-V (sem o System Center Virtual Machine Manager), você pode especificar a rede virtual de destino para máquinas virtuais individuais. Para máquinas virtuais Hyper-V gerenciadas com o Virtual Machine Manager, use o [mapeamento de rede](site-recovery-network-mapping.md) para mapear as redes de VM em um servidor do Virtual Machine Manager de origem e nas redes Azure de destino.

1. Em **Itens Replicados** em um cofre dos Serviços de Recuperação, selecione qualquer item replicado para acessar as configurações do item replicado.

2. Selecione a guia **Computação e Rede** para acessar as configurações de rede para o item replicado.

3. Em **Propriedades de rede**, escolha uma rede virtual da lista de adaptadores de rede disponíveis.

    ![Configurações de rede](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Modificar a rede de destino afeta todos os adaptadores de rede para a máquina virtual específica.

Para nuvens do Virtual Machine Manager, modificar o mapeamento de rede afeta todas as máquinas virtuais e seus adaptadores de rede.

## <a name="select-the-target-interface-type"></a>Selecione o tipo de adaptador de destino

Na seção **Adaptadores de rede** do painel **Computação e Rede**, você pode exibir e editar configurações de adaptador de rede. Você também pode especificar o tipo de adaptador de rede de destino.

- Um adaptador de rede **Primário** é necessário para o failover.
- Todos os outros adaptadores de rede selecionados, se há algum, são **Secundários**.
- Selecione **Não usar** para excluir um adaptador de rede da criação no failover.

Por padrão, quando você está habilitando a replicação, o Site Recovery seleciona todos os adaptadores de rede detectados no servidor local. Ele marca um como **Primário** e todos os outros como **Secundários**. Todos os adaptadores adicionados posteriormente no servidor local serão marcados como **Não usar** por padrão. Quando estiver adicionando mais adaptadores de rede, certifique-se de que o tamanho de destino da máquina virtual do Azure correto esteja selecionado para acomodar todos os adaptadores de rede necessários.

## <a name="modify-network-interface-settings"></a>Modificar configurações de adaptador de rede

Você pode modificar a sub-rede e o endereço IP para adaptadores de rede de um item replicado. Se um endereço IP não for especificado, o Site Recovery atribuirá o próximo endereço IP disponível da sub-rede ao adaptador de rede no failover.

1. Selecione qualquer adaptador de rede disponível para abrir as configurações de adaptador de rede.

2. Escolha a sub-rede desejada na lista de sub-redes disponíveis.

3. Insira o endereço IP desejado (conforme necessário).

    ![Configurações de adaptador de rede](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Selecione **OK** para terminar de editar e retornar para o painel **Computação e Rede**.

5. Repita as etapas 1 a 4 para outros adaptadores de rede.

6. Selecione **Salvar** para salvar as alterações.

## <a name="next-steps"></a>Próximas etapas
  [Saiba mais](../virtual-network/virtual-network-network-interface-vm.md) sobre adaptadores de rede para máquinas virtuais do Azure.
