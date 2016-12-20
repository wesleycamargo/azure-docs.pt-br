---
title: "O que é o Gerenciador de Tráfego| Microsoft Docs"
description: "Este artigo o ajudará a entender o que é o Gerenciador de Tráfego e se é a opção de roteamento do tráfego correta para seu aplicativo"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 69b94c93ad3e9c9745af8485766b4237cac0062c
ms.openlocfilehash: 79c14e713fd58d3f69f5978e550a78e8e84d286d

---

# <a name="overview-of-traffic-manager"></a>Visão Geral do Gerenciador de Tráfego

O Gerenciador de Tráfego do Microsoft Azure permite controlar a distribuição do tráfego do usuário para pontos de extremidade do serviço em diferentes datacenters. Os pontos de extremidade de serviço com suporte no Gerenciador de Tráfego incluem VMs do Azure, Aplicativos Web e serviços de nuvem. Você também pode usar o Gerenciador de Tráfego com pontos de extremidade externos e não do Azure.

O Gerenciador de Tráfego usa o DNS (Sistema de Nome de Domínio) para direcionar solicitações de cliente para o ponto de extremidade mais apropriado com base em um [método de roteamento de tráfego](traffic-manager-routing-methods.md) e a integridade dos pontos de extremidade. O Gerenciador de Tráfego oferece uma variedade de métodos de roteamento de tráfego para atender às necessidades de diferentes aplicativo, [monitoramento](traffic-manager-monitoring.md) de integridade do ponto de extremidade e failover automático. O Gerenciador de Tráfego é resistente a falhas, incluindo a falha de toda a região do Azure.

## <a name="traffic-manager-benefits"></a>Benefícios do Gerenciador de Tráfego

O Gerenciador de Tráfego pode ajudá-lo a:

* **Aumentar a disponibilidade de aplicativos críticos**

    O Gerenciador de Tráfego fornece alta disponibilidade para seus aplicativos monitorando seus pontos de extremidade e fornecendo failover automático quando um ponto de extremidade fica inativo.

* **Melhora a capacidade de resposta de aplicativos de alto desempenho**

    O Azure permite executar serviços de nuvem ou sites em data centers espalhados pelo mundo inteiro. O Gerenciador de Tráfego melhora a capacidade de resposta do aplicativo direcionando o tráfego para o ponto de extremidade com a menor latência de rede para o cliente.

* **Realizar manutenção de serviço sem tempo de inatividade**

    Você pode executar operações de manutenção planejada em seus aplicativos sem tempo de inatividade. O Gerenciador de Tráfego direciona tráfego para pontos de extremidade alternativos enquanto a manutenção está em andamento.

* **Combinar aplicativos baseados em nuvem e locais**

    O Gerenciador de Tráfego tem suporte para pontos de extremidade externos não do Azure habilitando seu uso com implantações locais e de nuvem híbrida, incluindo os cenários de "intermitência para a nuvem", "migrar para a nuvem" e "failover para a nuvem".

* **Distribuir o tráfego para implantações grandes e complexas**

    Usando [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md), métodos de roteamento de tráfego podem ser combinados para criar regras sofisticadas e flexíveis para atender às necessidades de implantações maiores e mais complexas.

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [como o Gerenciador de Tráfego funciona](traffic-manager-how-traffic-manager-works.md).
* Saiba como desenvolver aplicativos de alta disponibilidade usando o [monitoramento do ponto de extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md).
* Saiba mais sobre os [métodos de roteamento do tráfego](traffic-manager-routing-methods.md) suportados pelo Gerenciador de Tráfego.
* [Criar um perfil do Gerenciador de Tráfego](traffic-manager-manage-profiles.md).



<!--HONumber=Nov16_HO3-->


