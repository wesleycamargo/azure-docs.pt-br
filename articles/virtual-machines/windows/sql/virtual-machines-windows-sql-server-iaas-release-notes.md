---
title: SQL Server nas notas de versão VM do Azure | Microsoft Docs
description: Aprenda sobre os novos recursos e aprimoramentos do SQL Server em uma VM do Azure
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2018
ms.author: mathoma
ms.openlocfilehash: 7c854759e9a0ba13b698dd07dabb25100895bc85
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766870"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Notas de versão do SQL Server no Virtual Machine do Azure

O Azure permite implantar uma máquina virtual com uma imagem do SQL Server incorporada. Este artigo lista os novos recursos e aprimoramentos que você pode esperar na versão mais recente do SQL Server implantada em uma máquina virtual do Azure. 

## <a name="december-2018"></a>Dezembro de 2018

| **Alteração** | Detalhes |
| --- | --- |
| **Novo provedor de recursos de grupo de clusters do SQL** | Há um novo provedor de recursos (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) que define os metadados sobre o Cluster de Failover do Windows. Ingressar em uma VM do SQL Server para o *SqlVirtualMachineGroups* inicializa o serviço de Cluster de Failover do Windows e ingressa a VM no cluster.  |
|**Automatizar a configuração de uma implantação de grupo de disponibilidade com modelos de Início Rápido do Azure** |Agora é possível criar o Cluster de Failover do Windows, ingressar VMs do SQL Server a ele, criar o ouvinte e configurar o Internal Load Balancer com dois modelos de Início Rápido do Azure. Para obter mais informações, confira [Criar o WSFC, o ouvinte e configurar o ILB para um grupo de disponibilidade Always On em uma VM do SQL Server com os modelos de Início Rápido do Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Registro automático do provedor de recursos de VM do SQL** | VMs do SQL Server implantadas depois deste mês são automaticamente registradas com o novo provedor de recursos do SQL Server. VMs do SQL Server implantadas antes deste mês ainda precisarão ser registradas manualmente. Para obter mais informações, confira [Registrar a VM do SQL existente com o novo provedor de recursos](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>Novembro de 2018

| **Alteração** | Detalhes |
| --- | --- |
| **Novo provedor de recursos de VM do SQL** |  Há um novo provedor de recursos para VMs do SQL Server (Microsoft.SqlVirtualMachine) que permite um melhor gerenciamento da VM do SQL Server. Para obter mais informações sobre como registrar sua VM, consulte [Registrar a VM do SQL existente com o novo provedor de recursos](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider). |
|**Alternar o modelo de licenciamento** |Agora você pode alternar entre o modelo de pagamento por uso e o de trazer sua própria licença para sua VM do SQL usando a CLI do Azure ou o PowerShell do Azure. Para saber mais, confira [Como alterar o modelo de licenciamento de uma VM do SQL](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Recursos adicionais

**VMs do Windows**:

* [Visão geral do SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Provisionar uma VM do Windows do SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migração de um banco de dados para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md)
* [Alta disponibilidade e recuperação de desastres para SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Práticas recomendadas relacionadas ao desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md)
* [Estratégias de Desenvolvimento e Padrões de Aplicativo para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**VMs Linux**:

* [Visão geral do SQL Server em uma VM Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Provisionar uma VM Linux do SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Perguntas Frequentes (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server na documentação do Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
