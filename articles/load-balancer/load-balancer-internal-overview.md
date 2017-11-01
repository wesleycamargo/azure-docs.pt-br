---
title: "Visão geral do Azure Load Balancer interno | Microsoft Docs"
description: "Como um balanceador de carga interno funciona no Azure e cenários para configurar pontos de extremidade internos."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 36065bfe-0ef1-46f9-a9e1-80b229105c85
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 54e390dbdb07cb4c45c801b638099aa0dcc6db1a
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2017
---
# <a name="overview-of-azure-internal-load-balancer"></a>Visão geral do Azure Load Balancer interno

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

O Azure ILB (Load Balancer interno) apenas direciona o tráfego a recursos que estão dentro de um serviço de nuvem ou que usam uma VPN para acessar a infraestrutura do Azure. Nesse sentido, o ILB é diferente de um balanceador de carga para a Internet. A infraestrutura do Azure restringe o acesso aos endereços VIP (IP virtual) com balanceamento de carga de um serviço de nuvem ou a uma rede virtual. Os endereços VIP e as redes virtuais nunca são expostos diretamente a um ponto de extremidade de Internet. Os aplicativos de linha de negócios interno são executados no Azure e acessados de dentro do Azure ou de recursos locais.

## <a name="why-you-might-need-an-internal-load-balancer"></a>Por que talvez seja necessário um balanceador de carga interno

O Load Balancer interno oferece balanceamento de carga entre VMs (máquinas virtuais) que residem em um serviço de nuvem ou em uma rede virtual com escopo regional. Para obter informações sobre redes virtuais com escopo regional, consulte [Regional virtual networks](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) (Redes virtuais regionais) no blog do Azure. As redes virtuais existentes configuradas para um grupo de afinidades não podem usar o ILB.

O ILB habilita os seguintes tipos de balanceamento de carga:

* Dentro de um serviço de nuvem: balanceamento de carga de VMs para um conjunto de VMs que residem no mesmo serviço de nuvem. Consulte este <a href="#figure1">exemplo</a>.
* Dentro de uma rede virtual: balanceamento de carga de VMs na rede virtual para um conjunto de VMs que residem no mesmo serviço de nuvem da rede virtual. Consulte este <a href="#figure2">exemplo</a>.
* Para uma rede virtual entre instalações: balanceamento de carga de computadores locais para um conjunto de VMs que residem no mesmo serviço de nuvem da rede virtual. Consulte este <a href="#figure3">exemplo</a>.
* Para aplicativos de várias camadas: balanceamento de carga para aplicativos de várias camadas para a Internet em que as camadas de back-end não são para a Internet. As camadas de back-end requerem balanceamento de carga de tráfego da camada para a Internet.
* Para aplicativos de linha de negócios: balanceamento de carga para aplicativos de linha de negócios hospedados no Azure sem hardware ou software de balanceador de carga adicional. Esse cenário inclui servidores locais que estão no conjunto de computadores cujo tráfego tem balanceamento de carga.

## <a name="load-balancing-for-internet-facing-multi-tier-applications"></a>Balanceamento de carga para aplicativos de várias camadas para a Internet

A camada Web tem pontos de extremidade para a Internet para clientes de Internet e faz parte de um conjunto de balanceamento de carga. O ILB distribui o tráfego de entrada de clientes Web para a porta TCP 443 (HTTPS) para os servidores Web.

Os servidores de banco de dados estão atrás de um ponto de extremidade ILB que os servidores Web usam para armazenamento. O ponto de extremidade ILB é um ponto de extremidade com balanceamento de carga do serviço do banco de dados. O tráfego tem balanceamento de carga entre os servidores de banco de dados no conjunto do ILB.

A imagem a seguir mostra o balanceamento de carga interno para o aplicativo de várias camadas para a Internet dentro do mesmo serviço de nuvem.

<a name="figure1"></a>
![Aplicativo de várias camadas para a Internet](./media/load-balancer-internal-overview/IC736321.png)

Outro cenário está disponível para aplicativos de várias camadas. O balanceador de carga é implantado em um serviço de nuvem diferente daquele que consome o serviço para o ILB.

Os serviços de nuvem que usam a mesma rede virtual podem acessar o ponto de extremidade ILB. A imagem mostra que os servidores Web front-end estão em um serviço de nuvem diferente do back-end do banco de dados. Os servidores front-end usam o ponto de extremidade ILB dentro da mesma rede virtual como o back-end.

<a name="figure2"></a>
![Servidores front-end em um serviço de nuvem diferente](./media/load-balancer-internal-overview/IC744147.png)

## <a name="load-balancing-for-intranet-line-of-business-applications"></a>Balanceamento de carga para aplicativos de linha de negócios da intranet

O tráfego de clientes na rede local tem balanceamento de carga entre o conjunto de servidores de linha de negócios que usam uma conexão VPN com a rede do Azure.

O computador cliente pode acessar um endereço IP do serviço de VPN do Azure usando uma VPN ponto a site. O aplicativo de linha de negócios pode ser hospedado atrás do ponto de extremidade ILB.

<a name="figure3"></a>
![Aplicativo de linha de negócios hospedado atrás do ponto de extremidade ILB](./media/load-balancer-internal-overview/IC744148.png)

Outro cenário para aplicativos de linha de negócios é uma VPN site a site para a rede virtual em que o ponto de extremidade ILB está configurado. O tráfego de rede local é roteado para o ponto de extremidade ILB.

<a name="figure4"></a>
![Tráfego de rede local roteado para o ponto de extremidade ILB](./media/load-balancer-internal-overview/IC744150.png)

## <a name="limitations"></a>Limitações

As configurações do Load Balancer interno não são compatíveis com o SNAT. Neste artigo, SNAT refere-se a cenários que envolvem conversão de endereço de rede de origem de representação de porta. Uma VM em um pool de balanceador de carga deve atingir o endereço IP de front-end do respectivo balanceador de carga interno. Ocorrerão falhas de conexão quando a carga do fluxo for balanceada para a VM que o originou. Esses cenários não são compatíveis com o ILB. Em vez disso, um balanceador de carga de estilo de proxy deve ser usado.

## <a name="next-steps"></a>Próximas etapas

* [Suporte do Azure Resource Manager para o Azure Load Balancer](load-balancer-arm.md)
* [Introdução à configuração de um balanceador de carga para a Internet](load-balancer-get-started-internet-arm-ps.md)
* [Introdução à configuração de um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)
* [Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)
* [Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
