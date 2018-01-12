---
title: "Visão geral de portas de alta disponibilidade no Azure | Microsoft Docs"
description: Saiba mais sobre o balanceamento de carga de portas de alta disponibilidade em um balanceador de carga interno.
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: 46e284d1636988390f3533d93bfd07399f45dc92
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2017
---
# <a name="high-availability-ports-overview"></a>Visão geral de portas de alta disponibilidade

O Azure Load Balancer Standard ajuda você a balancear cargas de fluxos TCP e UDP em todas as portas simultaneamente quando estiver usando um Load Balancer interno. 

>[!NOTE]
> O recurso de Portas de alta disponibilidade (HA) está disponível com o Load Balancer Standard e, atualmente, está na versão prévia. Durante a versão prévia, o recurso pode não ter o mesmo nível de disponibilidade e confiabilidade que os recursos que estão na versão de disponibilidade geral. Para obter mais informações, consulte [Termos de Uso Complementares do Microsoft Azure para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Inscreva-se para a Versão prévia do Load Balancer Standard para usar portas de alta disponibilidade com recursos do Load Balancer Standard. Siga as instruções para inscrever-se na versão prévia do Load Balancer [Standard](https://aka.ms/lbpreview#preview-sign-up).

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

Também é possível usar portas de alta disponibilidade para aplicativos que exigem o balanceamento de carga de grandes quantidades de portas. Você pode simplificar esses cenários usando um [Load Balancer Standard](https://aka.ms/lbpreview) interno com portas de alta disponibilidade. Uma única regra de balanceamento de carga substitui várias regras individuais de balanceamento de carga, uma para cada porta.

## <a name="region-availability"></a>Disponibilidade de região

O recurso de portas de alta disponibilidade está disponível [nas mesmas regiões que o Load Balancer Standard](https://aka.ms/lbpreview#region-availability).  

## <a name="preview-sign-up"></a>Inscrição na versão prévia

Para participar da versão prévia do recurso de portas de alta disponibilidade no Load Balancer Standard, registre sua assinatura da [versão prévia do Load Balancer Standard](https://aka.ms/lbpreview#preview-sign-up). Você pode se registrar usando a CLI do Azure 2.0 ou o PowerShell.

>[!NOTE]
>O registro pode levar até uma hora.

## <a name="limitations"></a>Limitações

A seguir, estão as configurações com suporte ou exceções para o recurso de portas de alta disponibilidade:

- Uma configuração de IP de front-end pode ter uma única regra de balanceador de carga de DSR (DSR - Floating IP in Azure) com portas de alta disponibilidade, ou pode ter uma única regra de balanceador de carga não DSR com portas de alta disponibilidade. Ele não pode ter ambos.
- Uma única configuração de IP de adaptador de rede pode ter apenas uma regra de balanceador de carga não DSR com portas de alta disponibilidade. Não é possível configurar todas as outras regras para este ipconfig.
- Uma mesma configuração de IP de adaptador de rede pode ter uma ou mais regras de balanceador de carga DSR com portas de alta disponibilidade, desde que todas as respectivas configurações de IP de front-end sejam exclusivas.
- Se todas as regras de balanceamento de carga forem de portas de alta disponibilidade (somente DSR), duas (ou mais) regras de Load Balancer apontando para o mesmo pool de back-end poderão coexistir. O mesmo acontecerá se todas as regras não forem de portas de alta disponibilidade (DSR e não DSR). Se houver uma combinação de regras de portas de alta disponibilidade e portas que não são de alta disponibilidade, essas duas regras de balanceamento de carga não poderão coexistir.
- O recurso de portas de alta disponibilidade não está disponível para IPv6.
- A simetria de fluxo para cenários NVA é compatível somente com uma única NIC. Veja a descrição e o diagrama para [Soluções de virtualização de rede](#nva). 



## <a name="next-steps"></a>Próximas etapas

- [Configurar portas de alta disponibilidade em um Load Balancer Standard interno](load-balancer-configure-ha-ports.md)
- [Saiba mais sobre a versão prévia do Load Balancer Standard](https://aka.ms/lbpreview)

