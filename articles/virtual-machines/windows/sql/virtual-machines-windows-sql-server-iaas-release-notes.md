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
ms.openlocfilehash: 52036d8f5d25fc4a4f2d1b602428e9cba4762b7f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993149"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Notas de versão do SQL Server no Virtual Machine do Azure

O Azure permite implantar uma máquina virtual com uma imagem do SQL Server incorporada. Este artigo lista os novos recursos e aprimoramentos que você pode esperar na versão mais recente do SQL Server implantada em uma máquina virtual do Azure. 


## <a name="november-2018"></a>Novembro de 2018
- **Implantar provedor de recursos do SQL**: Há um novo provedor de recursos para VMs SQL que permite um melhor gerenciamento de sua VM. Para obter mais informações sobre como registrar sua VM, consulte [Registrar a VM do SQL existente com o novo provedor de recursos](virtual-machines-windows-sql-ahb.md#register-existing-sql-vm-with-new-resource-provider).
- **Alternar o modelo de licenciamento**: Agora você pode alternar entre o modelo de licença de pagamento por uso e o de trazer sua própria para sua VM SQL usando a CLI do Azure ou o PowerShell do Azure. Para obter mais informações, consulte [como alterar o modelo de licenciamento de uma VM do SQL](virtual-machines-windows-sql-ahb.md)



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
