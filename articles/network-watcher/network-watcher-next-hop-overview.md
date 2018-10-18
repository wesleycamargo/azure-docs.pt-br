---
title: Introdução ao próximo salto no Observador de Rede do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da capacidade do próximo salto do Observador de Rede.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 28eacdce922e26d391cf34f78cb03ead9c6887a1
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391259"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Use o próximo salto para diagnosticar problemas de roteamento da máquina virtual

O tráfego de uma máquina virtual (VM) é enviado para um destino com base nas rotas efetivas associadas a uma interface de rede (NIC). O próximo salto obtém o tipo do próximo salto e o endereço IP de um pacote em uma VM e na NIC. Saber o próximo salto ajuda a determinar se o tráfego está sendo direcionado para o destino pretendido, ou se o tráfego não está sendo enviado a nenhum lugar. Uma configuração incorreta das rotas, onde o tráfego é direcionado para um caminho local ou um dispositivo virtual, pode levar a problemas de conectividade. O próximo salto também retorna a tabela de rotas associada ao próximo salto. Se a rota for especificada como sendo definida pelo usuário, ela será retornada. Caso contrário, o próximo salto retornará a **Rota do Sistema**.

![visão geral do próximo salto](./media/network-watcher-next-hop-overview/figure1.png)

Os próximos saltos que podem ser retornados pelo próximo recurso de salto são os seguintes:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* Nenhum

Para saber mais sobre cada tipo de próximo salto, consulte [Visão geral do roteamento](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Próximas etapas

Para saber como usar o próximo salto para diagnosticar problemas de roteamento de rede de VM, consulte [Diagnosticar problemas de roteamento de rede de VM](diagnose-vm-network-routing-problem.md).
