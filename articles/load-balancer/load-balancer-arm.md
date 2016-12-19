---
title: Suporte do Azure Resource Manager para o Balanceador de Carga | Microsoft Docs
description: Usando o powershell do Load Balancer com o Azure Resource Manager. Usando modelos de balanceador de carga
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: tysonn
ms.assetid: d0394f11-ee5a-4407-9d86-79c936297265
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: b0046511f1a56cd72423bbbcfb5bf1e17e60ce5b
ms.openlocfilehash: 15147a201fa053e72a840294ade4ccb6197c78ac

---

# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Usando o suporte do Azure Resource Manager com o Azure Load Balancer

O Azure Resource Manager é a estrutura de gerenciamento preferida dos serviços no Azure. O Azure Load Balancer pode ser gerenciado usando ferramentas e APIs baseadas no Azure Resource Manager.

## <a name="concepts"></a>Conceitos

Com Resource Manager, o Azure Load Balancer contém os recursos filho a seguir:

* Configuração de IP front-end – um balanceador de carga pode incluir um ou mais endereços IP front-end, também conhecidos como VIPs (IPs virtuais). Esses endereços IP servem como entrada para o tráfego.
* Pool de endereços back-end – são endereços IP associados a NIC (Placa de Interface de Rede) de máquina virtual nos quais a carga será distribuída.
* Regras de balanceamento de carga – uma propriedade de regra mapeia determinada combinação de porta e IP front-end para um conjunto de endereços IP back-end e combinação de portas. Um balanceador de carga único pode ter várias regras de balanceamento de carga. Cada regra é uma combinação de um IP de front-end e IP de porta e back-end e porta associada a VMs.
* Investigações – as investigações permitem que você controle a integridade das instâncias VM. Se um teste de integridade falhar, a instância VM será retirada automaticamente do rodízio.
* Regras NAT de entrada – regras NAT definem o tráfego de entrada que flui pelo IP front-end e é distribuído para o IP back-end.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Modelos de início rápido

O Gerenciador de Recursos do Azure permite que você provisione seus aplicativos usando um modelo declarativo. Em um modelo único, você pode implantar vários serviços, juntamente com suas dependências. Use o mesmo modelo para implantar repetidamente seu aplicativo durante cada estágio do ciclo de vida do aplicativo.

Modelos podem incluir definições para máquinas virtuais, redes virtuais, conjuntos de disponibilidade, NICs (Interfaces de Rede), contas de armazenamento, balanceadores de carga, grupos de segurança de rede e IPs públicos. Com os modelos, você pode criar tudo o que precisa para um aplicativo complexo. O arquivo de modelo pode ser inserido no sistema de gerenciamento de conteúdo para controle de versão e colaboração.

[Saiba mais sobre modelos](../resource-manager-template-walkthrough.md)

[Saiba mais sobre recursos de rede](../virtual-network/resource-groups-networking.md)

Modelos de início rápido que usam o Azure Load Balancer podem ser encontrados em um [repositório GitHub](https://github.com/Azure/azure-quickstart-templates) que hospeda um conjunto de modelos gerados pela comunidade.

Exemplos de modelos:

* [2 VMs em um balanceador de carga e regras de balanceamento de carga](http://go.microsoft.com/fwlink/?LinkId=544799)
* [2 VMs em uma VNET com as regras de um balanceador de carga interno e regras do balanceador de carga](http://go.microsoft.com/fwlink/?LinkId=544800)
* [2 VMs em um balanceador de carga e regras NAT configuradas no balanceador de carga](http://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Configurando o balanceador de carga do Azure com PowerShell ou CLI

Introdução aos cmdlets do Azure Resource Manager, ferramentas de linha de comando e APIs REST

* [Cmdlets de rede do Azure](https://msdn.microsoft.com/library/azure/mt163510.aspx) podem ser usados para criar um balanceador de carga.
* [Como criar um balanceador de carga usando o gerenciador de recursos do Azure](load-balancer-get-started-ilb-arm-ps.md)
* [Usando a CLI do Azure com o gerenciamento de recursos do Azure](../xplat-cli-azure-resource-manager.md)
* [APIs REST do balanceador de carga](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Próximas etapas

Também é possível [começar a criar um balanceador de carga para a Internet](load-balancer-get-started-internet-arm-ps.md) e configurar que tipo de [modo de distribuição](load-balancer-distribution-mode.md) para um comportamento específico de tráfego de rede do balanceador de carga.

Saiba como gerenciar [tempo limite de TCP ocioso para um balanceador de carga](load-balancer-tcp-idle-timeout.md). Isso será importante quando seu aplicativo precisar manter conexões ativas para servidores atrás de um balanceador de carga.



<!--HONumber=Nov16_HO3-->


