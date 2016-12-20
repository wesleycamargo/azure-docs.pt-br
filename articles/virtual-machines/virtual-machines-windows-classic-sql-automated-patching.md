---
title: "Aplicação de patch automatizada para VMs do SQL Server (Clássico) | Microsoft Docs"
description: "Explica o recurso de Aplicação de Patch Automatizada para Máquinas Virtuais do SQL Server em execução no Azure e usando o modo de implantação clássico."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 09/26/2016
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: b10b1a5e50729963a758b2a5ba0c8856df0df848


---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Aplicação de patch automatizada para o SQL Server em Máquinas Virtuais do Azure (Clássico)
> [!div class="op_single_selector"]
> * [Gerenciador de Recursos](virtual-machines-windows-sql-automated-patching.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> * [Clássico](virtual-machines-windows-classic-sql-automated-patching.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> 
> 

A aplicação de patch automatizada estabelece uma janela de manutenção para uma Máquina Virtual do Azure que executa o SQL Server. Atualizações automáticas só podem ser instaladas durante esta janela de manutenção. Para o SQL Server, isso garante que as atualizações do sistema e qualquer reinicialização associada ocorrerão no melhor momento possível para o banco de dados. A aplicação de patch automatizada depende da [Extensão do Agente IaaS do SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Para exibir a versão do Resource Manager deste artigo, consulte [Aplicação de Patch Automatizada para o SQL Server em Máquinas Virtuais do Azure do Resource Manager](virtual-machines-windows-sql-automated-patching.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="prerequisites"></a>Pré-requisitos
Para usar a Aplicação de Patch Automatizada, considere os seguintes pré-requisitos:

**Sistema operacional**:

* Windows Server 2012
* Windows Server 2012 R2

**Versão do SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Instale os comandos mais recentes do Azure PowerShell](../powershell-install-configure.md).

**Extensão IaaS do SQL Server**:

* [Instale a Extensão IaaS do SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="settings"></a>Configurações
A tabela a seguir descreve as opções que podem ser configuradas para Aplicação de Patch Automatizada. Para VMs clássicas, você deve usar o PowerShell para definir essas configurações.

| Configuração | Valores possíveis | Descrição |
| --- | --- | --- |
| **Aplicação de patch automatizada** |Habilitar/desabilitar (Desabilitado) |Habilita ou desabilita a Aplicação de Patch Automatizada para uma máquina virtual do Azure. |
| **Agenda de manutenção** |Todos os dias, segunda-feira, terça-feira, quarta-feira, quinta-feira, sexta-feira, sábado e domingo |A agenda para baixar e instalar atualizações do Windows, do SQL Server e do Microsoft para sua máquina virtual. |
| **Hora de início da manutenção** |0h a 24h |A hora de início local para atualizar a máquina virtual. |
| **Duração da janela de manutenção** |30-180 |O número de minutos permitidos para concluir o download e a instalação de atualizações. |
| **Categoria de patch** |Importante |A categoria de atualizações para baixar e instalar. |

## <a name="configuration-with-powershell"></a>Configuração com o PowerShell
No exemplo a seguir, o PowerShell é usado para configurar a Aplicação de Patch Automatizada em uma VM existente do SQL Server. O comando **New-AzureVMSqlServerAutoPatchingConfig** configura uma nova janela de manutenção para atualizações automáticas.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Com base neste exemplo, a tabela a seguir descreve o efeito prático sobre a VM do Azure de destino:

| Parâmetro | Efeito |
| --- | --- |
| **DayOfWeek** |Patches instalados toda quinta-feira. |
| **MaintenanceWindowStartingHour** |Inicia as atualizações às 11h. |
| **MaintenanceWindowsDuration** |Os patches devem ser instalados dentro de 120 minutos. Com base na hora de início, eles devem estar concluídos até 13h. |
| **PatchCategory** |A única configuração possível para esse parâmetro é "Important". |

Pode demorar vários minutos para instalar e configurar o Agente IaaS do SQL Server.

Para desabilitar a Aplicação de Patch Automatizada, execute o mesmo script sem o parâmetro -Enable para New-AzureVMSqlServerAutoPatchingConfig. Assim como acontece com a instalação, pode demorar vários minutos para desabilitar a Aplicação de Patch Automatizada.

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre outras tarefas de automação disponíveis, consulte [Extensão do agente IaaS do SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, consulte [Visão geral do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Nov16_HO3-->


