---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: 459c99d1b45af9c98cc1a6d0d7dd7a9a04c824ec
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52268682"
---
Um Azure Load Balancer é um balanceador de carga de Camada 4 (TCP, UDP). O balanceador de carga fornece alta disponibilidade, distribuindo o tráfego de entrada entre instâncias do serviço de integridade em serviços de nuvem ou máquinas virtuais em um conjunto de balanceadores de carga. O Azure Load Balancer também pode apresentar esses serviços em várias portas, vários endereços IP ou ambos.

Você pode configurar um balanceador de carga para:

* Balancear a carga do tráfego de entrada na Internet para VMs (máquinas virtuais). Nós nos referimos a um balanceador de carga neste cenário como um [Balanceador de carga voltado para a Internet](../articles/load-balancer/load-balancer-internet-overview.md).
* Balancear a carga do tráfego entre máquinas virtuais em uma VNet (rede virtual), entre VMs em serviços de nuvem ou entre computadores locais e máquinas virtuais em uma rede virtual entre instalações. Nós nos referimos a um balanceador de carga neste cenário como um [ILB (Balanceador de carga interno)](../articles/load-balancer/load-balancer-internal-overview.md).
* Encaminhar tráfego externo para uma instância VM específica.
