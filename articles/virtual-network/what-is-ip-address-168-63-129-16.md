---
title: O que é o endereço IP 168.63.129.16? | Microsoft Docs
description: Saiba mais sobre o endereço IP 168.63.129.16 e como ele funciona com seus recursos.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: genli
ms.openlocfilehash: 78d2392e32465b3091c49032dc5df5f3a5b6061a
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65416027"
---
# <a name="what-is-ip-address-1686312916"></a>O que é o endereço IP 168.63.129.16?

O endereço IP 168.63.129.16 é um endereço IP público virtual usado para facilitar um canal de comunicação com os recursos da plataforma do Azure. Os clientes podem definir qualquer espaço de endereço para sua rede virtual privada no Azure. Portanto, os recursos da plataforma do Azure devem ser apresentados como um endereço IP público exclusivo. Esse endereço IP público virtual facilita o seguinte:

- Habilita o agente de VM a se comunicar com a plataforma do Azure para sinalizar que ele está em um estado "Pronto".
- Habilita a comunicação com o servidor virtual do DNS para fornecer a resolução de nome filtrada para os recursos (como VM) que não têm um servidor DNS personalizado. Essa filtragem garante que os clientes possam resolver os nomes de host de seus recursos.
- Habilita [investigações de integridade do balanceador de carga do Azure](../load-balancer/load-balancer-custom-probe-overview.md) para determinar o estado de integridade de VMs.
- Permite que a VM obtenha um endereço IP dinâmico do serviço DHCP no Azure.
- Habilita mensagens de pulsação de Agente Convidado para a função de PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Escopo de endereço IP 168.63.129.16

O endereço IP público 168.63.129.16 é usado em todas as regiões e todas as nuvens nacionais. Esse endereço IP público especial pertence à Microsoft e não será alterado. Ele é permitido pela regra de grupo de segurança de rede padrão. Recomendamos que você permita esse endereço IP em todas as políticas de firewall local. A comunicação entre esse endereço IP especial e os recursos é segura porque a plataforma interna do Azure pode originar uma mensagem desse endereço IP. Se esse endereço for bloqueado, um comportamento inesperado poderá ocorrer em uma variedade de cenários.

[As investigações de integridade do balanceador de carga do Azure](../load-balancer/load-balancer-custom-probe-overview.md) proveniente desse endereço IP. Se você bloquear esse endereço IP, suas investigações falharão.

Em um cenário de rede não-virtual, a investigação de integridade é originada de um endereço IP privado e 168.63.129.16 não é usado.

## <a name="next-steps"></a>Próximas etapas

- [Grupos de segurança](security-overview.md)
- [Criar, alterar ou excluir um Grupo de Segurança de Rede](manage-network-security-group.md)
