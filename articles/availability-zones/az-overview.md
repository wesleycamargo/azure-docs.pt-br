---
title: O que são as Zonas de Disponibilidade do Azure? | Microsoft Docs
description: Para criar aplicativos altamente disponíveis e resilientes no Azure, as Zonas de Disponibilidade oferecem locais fisicamente separados que você pode usar para executar seus recursos.
services: ''
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: cynthn
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: 557757fc4d99fe57ad545e9d2eebcce61ddb3a8f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59268714"
---
# <a name="what-are-availability-zones-in-azure"></a>O que são Zonas de Disponibilidade no Azure?
Zonas de Disponibilidade é uma oferta de alta disponibilidade que protege os aplicativos e dados contra falhas do datacenter. As Zonas de Disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. A separação física das Zonas de Disponibilidade dentro de uma região protege os aplicativos e dados contra falhas do datacenter. Serviços com redundância de zona replicam os aplicativos e dados entre Zonas de Disponibilidade para proteger dos pontos únicos de falha. Com Zonas de Disponibilidade, o Azure oferece o melhor SLA de tempo de atividade da VM de 99,99% do setor. O [SLA completo do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explica a disponibilidade garantida do Azure como um todo.

Uma Zona de Disponibilidade em uma região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Por exemplo, se você criar três ou mais VMs em três zonas em uma região do Azure, as VMs serão efetivamente distribuídas em três domínios de falha e três domínios de atualização. A plataforma do Azure reconhece essa distribuição nos domínios de atualização para garantir que as VMs em diferentes zonas não sejam atualizadas ao mesmo tempo.

Compila alta disponibilidade na arquitetura do aplicativo, colocalizando os recursos de computação, armazenamento, rede e dados em uma zona e replicando em outras zonas. Os serviços do Azure que fornecem suporte a Zonas de Disponibilidade enquadram-se em duas categorias:

- **Serviços zonais** – você fixa o recurso em uma zona específica (por exemplo, máquinas virtuais, discos gerenciados, endereços IP) ou
- **Serviços com redundância de zona** – a plataforma replica automaticamente entre zonas (por exemplo, armazenamento com redundância de zona, Banco de Dados SQL).

Para obter uma continuidade de negócios abrangente no Azure, compile a arquitetura do aplicativo usando a combinação de Zonas de Disponibilidade com pares de regiões do Azure. É possível replicar os aplicativos e dados de maneira síncrona usando Zonas de Disponibilidade em uma região do Azure para alta disponibilidade e replicação assíncrona em regiões do Azure para proteção de recuperação de desastre.
 
![exibição conceitual de uma zona reduzindo-se a uma região](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Regiões que oferecem suporte às Zonas de Disponibilidade

- Centro dos EUA
- Leste dos EUA
- Leste dos EUA 2
- França Central
- Norte da Europa
- Sudeste Asiático 
- Sul do Reino Unido&#42;
- Europa Ocidental
- Oeste dos EUA 2



## <a name="services-that-support-availability-zones"></a>Serviços que oferecem suporte às Zonas de Disponibilidade
Os serviços do Azure que oferecem suporte às Zonas de Disponibilidade são:

- Máquinas Virtuais do Linux
- Máquinas Virtuais do Windows
- Conjuntos de Dimensionamento de Máquinas Virtuais
- Managed Disks
- Balanceador de carga padrão&#42;
- Endereço IP público padrão&#42;
- Armazenamento com redundância de zona
- Banco de dados SQL
- Hubs de Eventos
- Barramento de Serviço (somente Camada Premium)
- Gateway de VPN
- ExpressRoute
- Gateway de Aplicativo (Preview)

&#42;Recursos criados no sul do Reino Unido, antes de 25 de março de 2019 em breve serão convertidos para serem com redundância de zona. Recursos criados após o dia 25 de março de 2019 será com redundância de zona imediatamente.

## <a name="services-resiliency"></a>Resiliência de serviços
Todos os serviços de gerenciamento do Azure são projetados para serem resilientes contra falhas de nível de região. O espectro de falhas, uma ou mais falhas de zona de disponibilidade dentro de uma região tem um raio de falha de menor em comparação a uma falha de toda a região. Azure pode recuperar de uma falha de nível de zona de serviços de gerenciamento dentro da região ou de outra região do Azure. Azure executa uma zona de manutenção importantes ao mesmo tempo em uma região, para evitar quaisquer falhas que afetam os recursos de cliente implantados em zonas de disponibilidade dentro de uma região.

## <a name="pricing"></a>Preços
Não há custo adicional para máquinas virtuais implantadas em uma Zona de Disponibilidade. O SLA de 99,99% de tempo de atividade da VM é oferecido quando duas ou mais VMs são implantadas em duas ou mais Zonas de Disponibilidade dentro de uma região do Azure. Haverá encargos de transferência de dados adicionais de VM para VM entre Zona de Disponibilidade. Para obter mais informações, consulte a página [Preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Introdução às Zonas de Disponibilidade
- [Criar uma máquina virtual](../virtual-machines/windows/create-portal-availability-zone.md)
- [Adicionar um disco gerenciado usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Criar um conjunto de dimensionamento de máquinas virtuais com redundância de zona](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [VMs com balanceamento de carga entre zonas usando um balanceador de carga padrão com um front-end com redundância de zona](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [VMs com balanceamento de carga dentro de uma zona usando um balanceador de carga padrão com um front-end zonal](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Armazenamento com redundância de zona](../storage/common/storage-redundancy-zrs.md)
- [Banco de dados SQL](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Recuperação de desastre geográfico dos Hubs de Eventos](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Recuperação de desastre geográfico do Barramento de Serviço](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Criar um gateway de rede virtual com redundância de zona](../vpn-gateway/create-zone-redundant-vnet-gateway.md)


## <a name="next-steps"></a>Próximas etapas
- [Modelos de início rápido](https://aka.ms/azqs)
