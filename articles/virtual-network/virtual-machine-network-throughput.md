---
title: Taxa de transferência de rede da máquina virtual do Azure | Microsoft Docs
description: Saiba mais sobre a taxa de transferência de rede da máquina virtual do Azure.
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: kumud,steveesp, mareat
ms.openlocfilehash: 9d74e53c754367ecfa63642514db93354fcadf25
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153715"
---
# <a name="virtual-machine-network-bandwidth"></a>Largura de banda de rede da máquina virtual

O Azure oferece uma variedade de tamanhos e tipos de VM, cada um com uma combinação diferente de funcionalidades de desempenho. Uma funcionalidade é a taxa de transferência (ou largura de banda) de rede, medida em megabits por segundo (Mbps). Como as máquinas virtuais são hospedadas em um hardware compartilhado, a capacidade de rede deve ser compartilhada adequadamente entre as máquinas virtuais que compartilham o mesmo hardware. Máquinas virtuais maiores recebem relativamente mais largura de banda que máquinas virtuais menores.
 
A largura de banda de rede alocada a cada máquina virtual é limitada no tráfego de saída (saída) da máquina virtual. Todo o tráfego de rede que deixa a máquina virtual é contado para o limite alocado, independentemente do destino. Por exemplo, se uma máquina virtual tem um limite de 1.000 Mbps, esse limite se aplica se o tráfego de saída é destinado a outra máquina virtual na mesma rede virtual ou fora do Azure.
 
A entrada não é limitada diretamente. No entanto, há outros fatores, como limites de CPU e armazenamento, que podem afetar a capacidade de uma máquina virtual de processar dados de entrada.

Rede acelerada é um recurso projetado para melhorar o desempenho de rede, incluindo latência, taxa de transferência e utilização da CPU. Embora a rede acelerada possa melhorar a taxa de transferência de uma máquina virtual, ela poderá fazer isso somente até a largura de banda alocada da máquina virtual. Para saber mais sobre a Rede Acelerada, consulte Rede acelerada para máquinas virtuais do [Windows](create-vm-accelerated-networking-powershell.md) ou do [Linux](create-vm-accelerated-networking-cli.md).
 
As máquinas virtuais do Azure devem ter um, mas podem ter vários adaptadores de rede anexados a elas. A largura de banda alocada a uma máquina virtual é a soma de todo o tráfego de saída em todos os adaptadores de rede anexados a uma máquina virtual. Em outras palavras, a largura de banda alocada é por máquina virtual, independentemente de quantos adaptadores de rede estão anexados à máquina virtual. Para saber para quantos adaptadores de rede diferentes tamanhos de VM do Azure dão suporte, consulte Tamanhos de VM [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure. 

## <a name="expected-network-throughput"></a>Taxa de transferência de rede esperada

A taxa de transferência de saída esperada e o número de adaptadores de rede com suporte em cada tamanho de VM são detalhados em Tamanhos de VM [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure. Selecione um tipo, como Uso geral e, em seguida, uma série de tamanho na página resultante, como a série Dv2. Cada série tem uma tabela com as especificações de rede na última coluna intitulada **Máx. de NICs/Desempenho de rede esperado (Mbps)**. 

O limite de taxa de transferência se aplica à máquina virtual. A taxa de transferência não é afetada pelos seguintes fatores:
- **Número de adaptadores de rede**: O limite de largura de banda é cumulativo de todo o tráfego de saída da máquina virtual.
- **Rede acelerada**: Embora o recurso pode ser útil para alcançar o limite publicado, ele não altera o limite.
- **Destino do tráfego**: Todos os destinos contam para o limite de saída.
- **Protocolo**: Todo o tráfego de saída em todos os protocolos conta para o limite.

## <a name="network-flow-limits"></a>Limites de fluxo de rede

Além de largura de banda, o número de conexões de rede presentes em uma VM em um determinado momento pode afetar o desempenho de rede. A pilha de rede do Azure mantém o estado para cada direção de uma conexão TCP/UDP em estruturas de dados chamado 'fluxos'. Uma conexão TCP/UDP típico terá 2 fluxos criados, um para a entrada e outro para a direção de saída. 

Transferência de dados entre pontos de extremidade exige a criação de vários fluxos, além daqueles que executam a transferência de dados. Alguns exemplos são fluxos criados para a resolução DNS e fluxos criados para investigações de integridade do balanceador de carga. Também Observe que a rede virtuais NVAs (soluções), como gateways, proxies, firewalls, verá fluxos que estão sendo criados para conexões encerrado no dispositivo e foi originada pelo dispositivo. 

![Contagem de fluxo para a conversa TCP por meio de um dispositivo de encaminhamento](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Limites de fluxo e recomendações

Hoje, a pilha de rede do Azure dá suporte a fluxos de rede total de 250 mil com bom desempenho para VMs com mais de 8 núcleos de CPU e de 100 mil fluxos total com bom desempenho para VMs com menos de 8 núcleos de CPU. Após essa rede de limite desempenho degrada normalmente para fluxos adicionais até um limite rígido de 1 M total de fluxos, 500 K de entrada e 500 K de saída, após o quais fluxos adicionais são descartados.

||VMs com < 8 núcleos de CPU|VMs com 8 núcleos de CPU|
|---|---|---|
|<b>Bom desempenho</b>|100 mil fluxos |Fluxos de 250 mil|
|<b>Degradação do desempenho</b>|Acima de 100 mil fluxos|Acima de 250 mil fluxos|
|<b>Limite de fluxo</b>|1M fluxos|1M fluxos|

As métricas estão disponíveis no [do Azure Monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) para controlar o número de fluxos de rede e a taxa de criação de fluxo em suas instâncias VM ou VMSS.

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

As taxas de estabelecimento e encerramento da Conexão também podem afetar o desempenho de rede como conexão estabelecimento e término compartilhamentos de CPU com rotinas de processamento de pacote. É recomendável que você submeter a benchmark as cargas de trabalho em relação a padrões de tráfego esperado e as cargas de trabalho de expansão adequadamente para corresponder às suas necessidades de desempenho. 

## <a name="next-steps"></a>Próximas etapas

- [Otimizar a taxa de transferência de rede para um sistema operacional da máquina virtual](virtual-network-optimize-network-bandwidth.md)
- [Testar a taxa de transferência de rede](virtual-network-bandwidth-testing.md) de uma máquina virtual.
