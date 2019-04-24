---
title: Grupos de Disponibilidade do SQL Server - máquinas virtuais do Azure - visão geral | Microsoft Docs
description: Este artigo apresenta os Grupos de Disponibilidade do SQL Server em máquinas virtuais do Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: b9977965dc076ec36aa90680a1732b6640b1e41a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60325793"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Introdução aos grupos de disponibilidade do AlwaysOn do SQL Server em máquinas virtuais do Azure #

Este artigo apresenta os grupos de disponibilidade do SQL Server em máquinas virtuais do Azure. 

Os grupos de disponibilidade Always On em máquinas virtuais do Azure são semelhantes aos grupos de disponibilidade Always On locais. Para obter mais informações, confira [Grupos de disponibilidade Always On (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

O diagrama ilustra as partes de um Grupo de Disponibilidade do SQL Server completo em Máquinas Virtuais do Azure.

![Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

A principal diferença para um Grupo de Disponibilidade em máquinas virtuais do Azure é que as máquinas virtuais do Azure exigem um [balanceador de carga](../../../load-balancer/load-balancer-overview.md). O balanceador de carga contém o endereço IP do ouvinte do grupo de disponibilidade. Se você tiver mais de um grupo de disponibilidade cada grupo exige um ouvinte. Um balanceador de carga pode dar suporte a vários ouvintes.

Além disso, em um cluster de failover de convidado de VM IaaS do Azure, recomendamos uma única NIC por servidor (nó de cluster) e uma única sub-rede. A rede do Azure tem redundância física, o que torna desnecessários os adaptadores de rede e as sub-redes adicionais em um cluster de convidado de uma VM de IaaS do Azure. Embora o relatório de validação de cluster emita um aviso de que os nós só são acessíveis em uma única rede, esse aviso pode ser ignorado com segurança em clusters de failover de convidado de uma VM de IaaS do Azure. 

|  | Versão do Windows Server | Versão do SQL Server | Edição do SQL Server | Configuração de Quorum WSFC | Recuperação de Desastre com várias regiões | Suporte a várias sub-redes | Suporte para um anúncio existente | Recuperação de Desastre com várias zona mesma região | Suporte de dist AG com nenhum domínio do AD | Suporte a AG dist com nenhum cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [CLI DE VM DO SQL](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | ENT | Testemunha da nuvem | Não  | sim | sim | sim | Não | Não  |
| [Modelos de início rápido](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | ENT | Testemunha da nuvem | Não  | sim | sim | sim | Não | Não  |
| [Modelo do Portal](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | ENT | Compartilhamento de arquivo | Não  | Não | Não | Não | Não | Não  |
| [Manual](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Todos | Todos | Todos | Todos | Sim | sim | sim | sim | sim | Sim |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Quando estiver pronto para criar um grupo de disponibilidade do SQL Server em Máquinas Virtuais do Azure, consulte estes tutoriais.

## <a name="manually-with-azure-cli"></a>Manualmente com a CLI do Azure
Usando a CLI do Azure para configurar e implantar um grupo de disponibilidade é a opção recomendada, pois é o melhor em termos de simplicidade e velocidade de implantação. Com a CLI do Azure, a criação de Cluster de Failover do Windows, ingressar VMs do SQL Server para o cluster, bem como a criação do ouvinte e balanceador de carga interno podem todos ser obtidos em menos de 30 minutos. Essa opção ainda exige a criação de um manual do grupo de disponibilidade, mas automatiza todas as outras etapas de configuração necessárias. 

Para obter mais informações, consulte [usar CLI de VM de SQL do Azure para configurar o grupo de disponibilidade Always On do SQL Server em uma VM do Azure](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automaticamente com os modelos de início rápido do Azure
Os modelos de início rápido do Azure utilizam o provedor de recursos de VM do SQL para implantar o Cluster de Failover do Windows, ingressar VMs do SQL Server a ele, crie o ouvinte e configurar o balanceador de carga interno. Essa opção ainda exige a criação de um manual do grupo de disponibilidade e balanceador de carga interno (ILB), mas automatiza e simplifica a todas as outras etapas de configuração necessárias (incluindo a configuração do ILB). 

Para obter mais informações, consulte [usar modelo de início rápido do Azure para configurar o grupo de disponibilidade Always On do SQL Server em uma VM do Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automaticamente com um modelo de Portal do Azure

[Configurar automaticamente o grupo de disponibilidade Always On na VM do Azure - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Manualmente no portal do Azure

Você também pode criar as máquinas virtuais sem o modelo. Primeiro, complete os pré-requisitos e criar o grupo de disponibilidade. Confira os seguintes tópicos: 

- [Configurar os pré-requisitos para grupos de disponibilidade do SQL Server Always On nas máquinas virtuais do Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Criar Grupo de Disponibilidade Always On para melhorar a disponibilidade e a recuperação de desastre](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Próximas etapas

[Configurar um Grupo de Disponibilidade Always On do SQL Server em Máquinas Virtuais do Azure em diferentes regiões](virtual-machines-windows-portal-sql-availability-group-dr.md)
