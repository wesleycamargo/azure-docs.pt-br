---
title: Automatizar tarefas de gerenciamento Em VMs do SQL (Resource Manager) | Microsoft Docs
description: Este artigo descreve como gerenciar a extensão do agente do SQL Server, que automatiza tarefas de administração específicas do SQL Server. Entre elas estão o Backup Automatizado, a Aplicação de Patch Automatizada e a Integração do Cofre de Chaves do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: jroth
ms.openlocfilehash: c663aec02d4d1808426a9f05a6674d5504563a63
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39012254"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Automatizar tarefas de gerenciamento em máquinas virtuais do Azure com a Extensão do Agente do SQL Server (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Clássico](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

A extensão do SQL Server IaaS Agent (SQLIaaSExtension) é executada em máquinas virtuais do Azure para automatizar tarefas de administração. Este artigo fornece uma visão geral dos serviços compatíveis com a extensão, bem como instruções de instalação, status e remoção.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para exibir a versão clássica deste artigo, confira [Extensão do SQL Server Agent para VMs do SQL Server (Clássico)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="supported-services"></a>Serviços com suporte
A Extensão do Agente IaaS do SQL Server dá suporte às seguintes tarefas de administração:

| Recurso de administração | DESCRIÇÃO |
| --- | --- |
| **Backup Automatizado do SQL** |Automatiza o agendamento de backups para todos os bancos de dados da instância padrão do SQL Server na VM. Para saber mais, veja [Backup automatizado para SQL Server em máquinas virtuais do Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Aplicação de patch automatizada do SQL** |Configura uma janela de manutenção durante a qual as atualizações importantes do Windows para a VM podem ocorrer, evitando atualizações da carga de trabalho durante horários de pico. Para saber mais, veja [Aplicação de patch automatizada para o SQL Server em Máquinas Virtuais do Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Integração do Cofre da Chave do Azure** |Permite a instalação e configuração automática do Cofre de Chaves do Azure em sua VM do SQL Server. Para saber mais, confira [Configurar a integração do Cofre de Chaves do Azure para SQL Server em VMs do Azure (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Uma vez instalada e em execução, a Extensão do Agente IaaS do SQL Server disponibiliza esses recursos de administração no painel do SQL Server da máquina virtual no portal do Azure e através do Azure PowerShell para as imagens do marketplace do SQL Server e, através do Azure PowerShell para as instalações manuais da extensão. 

## <a name="prerequisites"></a>Pré-requisitos
Requisitos para uso da extensão SQL Server IaaS Agent em sua VM:

**Sistema operacional**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versões do SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Baixar e configurar os comandos mais recentes do Azure PowerShell](/powershell/azure/overview)

> [!IMPORTANT]
> Neste momento, a [extensão do SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md) não é compatível com a FCI do SQL Server no Azure. É recomendável que você desinstale a extensão de VMs que participam de um FCI. Os recursos compatíveis com a extensão não estão disponíveis para as VMs do SQL depois que o agente é desinstalado.

## <a name="installation"></a>Instalação
A Extensão do Agente IaaS do SQL Server é instalada automaticamente quando você provisiona uma das imagens da galeria de máquinas virtuais do SQL Server. Se você precisar reinstalar manualmente a extensão em uma dessas VMs do SQL Server, use o seguinte comando do PowerShell:

```powershell
Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2" -Location "East US 2"
```

> [!IMPORTANT]
> Se a extensão já não estiver instalada, a instalação da extensão reiniciará o serviço do SQL Server.

> [!NOTE]
> A extensão do SQL Server IaaS Agent só é compatível com [imagens da galeria de VM do SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (paga conforme o uso ou traga sua própria licença). Ela não será compatível se você instalar manualmente o SQL Server em uma máquina virtual do Windows Server somente de sistema operacional ou se você implantar um VHD de VM do SQL Server personalizado. Nesses casos, talvez seja possível instalar e gerenciar a extensão manualmente usando o PowerShell, mas você não obterá as definições de configuração do SQL Server no portal do Azure. No entanto, é altamente recomendável, em vez disso, instalar uma imagem da galeria de VM do SQL Server e, em seguida, personalizá-la.

## <a name="status"></a>Status
Uma maneira de verificar se a extensão está instalada é exibir o status do agente no portal do Azure. Escolha **Todas as configurações** na janela da máquina virtual e clique em **Extensões**. Você deverá ver a extensão **SQLIaaSExtension** na lista.

![Extensão do Agente IaaS do SQL Server no portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Você também pode usar o cmdlet **Get-AzureVMSqlServerExtension** do Azure PowerShell.

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

O comando anterior confirma se o agente está instalado e fornece informações gerais sobre o status. Você também pode obter informações específicas de status do Backup automatizado e aplicação de patch com os comandos a seguir.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Remoção
No Portal do Azure, você pode desinstalar a extensão clicando nas reticências na janela **Extensões** das propriedades de sua máquina virtual. Em seguida, clique em **Excluir**.

![Desinstalar a Extensão do Agente IaaS do SQL Server no portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Você também pode usar o cmdlet do PowerShell **Remove-AzureRmVMSqlServerExtension**.

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>Próximas etapas
Comece a usar um dos serviços com suporte da extensão. Para obter mais detalhes, confira os artigos citados na seção [Serviços com suporte](#supported-services) deste artigo.

Para obter mais informações sobre como executar o SQL Server em Máquinas Virtuais do Azure, veja [Visão geral do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

