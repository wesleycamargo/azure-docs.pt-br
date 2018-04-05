---
title: Visão geral de portas de alta disponibilidade no Azure | Microsoft Docs
description: Saiba mais sobre o balanceamento de carga de portas de alta disponibilidade em um balanceador de carga interno.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: kumud
ms.openlocfilehash: 09c51441d393de5d801e7a4c259b711a527349d8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="high-availability-ports-overview"></a>Visão geral de portas de alta disponibilidade

O Azure Load Balancer Standard ajuda você a balancear cargas de fluxos TCP e UDP em todas as portas simultaneamente quando estiver usando um Load Balancer interno. 

Uma regra de portas de alta disponibilidade é uma variante de uma regra de balanceamento de carga configurada em um Load Balancer Standard interno. Você pode simplificar o uso do Load Balancer fornecendo uma única regra para balancear a carga de todos os fluxos TCP e UDP que chegam em todas as portas de um Load Balancer Standard interno. A decisão de balanceamento de carga é feita por fluxo. Isso é baseado na seguinte conexão de cinco tuplas: endereço IP de origem, porta de origem, endereço IP de destino, porta de destino e protocolo.

O recurso de portas de alta disponibilidade o ajuda com cenários críticos, como alta disponibilidade e escala para soluções de virtualização de rede (NVA) dentro de redes virtuais. Ele também pode ajudar quando um grande número de portas precisar de balanceamento de carga. 

O recurso de portas de alta disponibilidade é configurado quando você define as portas de front-end e back-end **0**e o protocolo como **Todos**. O recurso de Load Balancer interno agora balanceia todos os fluxos TCP e UDP, independentemente do número de portas.

## <a name="why-use-ha-ports"></a>Por que usar portas de alta disponibilidade?

### <a name="nva"></a>Soluções de virtualização de rede

É possível usar as NVA para proteger as cargas de trabalho do Azure contra vários tipos de ameaças à segurança. Quando as NVA são usadas nesses cenários, elas devem ser confiáveis, altamente disponíveis e expandir sob demanda.

Para atingir essas metas, basta adicionar instâncias NVA ao pool de back-end do Azure Load Balancer interno e configurar uma regra do Load Balancer para portas de alta disponibilidade.

Portas de alta disponibilidade oferecem diversas vantagens para cenários de NVA de alta disponibilidade:
- Failover rápido para instâncias íntegras com investigações de integridade por instância
- Alto desempenho com expansão para instâncias *n*-ativas
- Cenários *N*-ativo e ativo-passivo
- Eliminar a necessidade de soluções complexas como nós de Apache ZooKeeper para monitorar dispositivos

O diagrama a seguir apresenta uma implantação de rede virtual de hub e spoke. Os spokes forçam o tráfego por túnel para a rede virtual do hub e por meio de NVA, antes de deixar o espaço confiável. As NVAs estão por atrás de um Load Balancer Standard interno com a configuração de portas de alta disponibilidade. Todo o tráfego pode ser devidamente processado e encaminhado.

![Diagrama de rede virtual de hub e spoke com NVAs implantadas no modo de alta disponibilidade](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Se você usar NVAs, confirme com o respectivo provedor a melhor maneira de usar portas de alta disponibilidade e quais cenários são compatíveis.

### <a name="load-balancing-large-numbers-of-ports"></a>Balanceamento de carga de grandes quantidades de portas

Também é possível usar portas de alta disponibilidade para aplicativos que exigem o balanceamento de carga de grandes quantidades de portas. Você pode simplificar esses cenários usando um [Load Balancer Standard](load-balancer-standard-overview.md) interno com portas de alta disponibilidade. Uma única regra de balanceamento de carga substitui várias regras individuais de balanceamento de carga, uma para cada porta.

## <a name="region-availability"></a>Disponibilidade de região

O recurso de portas de alta disponibilidade está disponível em todas as regiões globais do Azure.

## <a name="supported-configurations"></a>Configurações com suporte

### <a name="one-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-the-internal-standard-load-balancer"></a>Uma única configuração de portas de alta disponibilidade de IP não flutuante (retorno de servidor não direto) no Load Balancer Standard interno

Essa é uma configuração básica de portas de alta disponibilidade. A configuração a seguir permite que você configure o balanceamento de carga de portas de alta disponibilidade em um único endereço IP de front-end -
- Ao configurar o Load Balancer Standard, selecione a caixa **Portas de alta disponibilidade** na configuração de regra do Load Balancer, 
- Certificando-se de que **IP flutuante** seja definido como **Desabilitado**.

Essa configuração não permite nenhuma outra configuração de regra de balanceamento de carga no recurso do Load Balancer nem nenhuma outra configuração de recurso do balanceador de carga interno para o conjunto de instâncias de back-end.

No entanto, você pode configurar um Load Balancer Standard para as instâncias de back-end além dessa regra de porta de alta disponibilidade.

## <a name="one-single-floating-ip-direct-server-return-ha-ports-configuration-on-the-internal-standard-load-balancer"></a>Uma única configuração de portas de alta disponibilidade de IP flutuante (retorno de servidor direto) no Load Balancer Standard interno

Você pode configurar o balanceador de carga de forma similar para usar uma regra de balanceamento de carga com **Porta de alta disponibilidade** com um único front-end e o **IP flutuante** definido como **Habilitado**. 

Essa configuração permite que você adicione mais regras de balanceamento de carga de IP flutuante e/ou um Load Balancer público. No entanto, você não pode usar uma configuração de balanceamento de carga de portas de alta disponibilidade de IP não flutuante com essa configuração.

## <a name="multiple-ha-ports-configurations-on-the-internal-standard-load-balancer"></a>Várias configurações de portas de alta disponibilidade no Load Balancer Standard interno

Se seu cenário exigir que você configure mais de um front-end de porta de alta disponibilidade para o mesmo pool de back-end, você pode fazer isso da seguinte maneira: 
- configurar mais de um endereço IP privado de front-end para um único recurso do Load Balancer Standard interno.
- configurar várias regras de balanceamento de carga, em que cada regra tem um único endereço IP de front-end selecionado.
- Selecione a opção **Portas de alta disponibilidade** e defina **IP flutuante** para **Habilitado** em todas as regras de balanceamento de carga.

## <a name="internal-load-balancer-with-ha-ports--public-load-balancer-on-the-same-backend-instances"></a>Load Balancer interno com portas de alta disponibilidade e Load Balancer público nas mesmas instâncias de back-end

Você pode configurar **um** recurso de Load Balancer Standard público para os recursos de back-end junto com um único padrão Load Balancer Standard interno com porta de alta disponibilidade.

>[!NOTE]
>Esse recurso está disponível por meio de modelos do Azure Resource Manager atualmente, mas não por meio do portal do Azure.

## <a name="limitations"></a>Limitações

- A configuração de portas de alta disponibilidade só está disponível para o Load Balancer interno e não está disponível para um Load Balancer público.

- Não há suporte para uma combinação de regra de balanceamento de carga de portas de alta disponibilidade e regra de balanceamento de carga de portas sem alta disponibilidade.

- O recurso de portas de alta disponibilidade não está disponível para IPv6.

- A simetria de fluxo para cenários NVA é compatível somente com uma única NIC. Veja a descrição e o diagrama para [Soluções de virtualização de rede](#nva). No entanto, se um NAT de destino pode funcionar para seu cenário, você pode usá-lo para certificar-se de que o Load Balancer interno enviará o tráfego de retorno para a mesma NVA.


## <a name="next-steps"></a>Próximas etapas

- [Configurar portas de alta disponibilidade em um Load Balancer Standard interno](load-balancer-configure-ha-ports.md)
- [Saiba mais sobre o Load Balancer Standard](load-balancer-standard-overview.md)
