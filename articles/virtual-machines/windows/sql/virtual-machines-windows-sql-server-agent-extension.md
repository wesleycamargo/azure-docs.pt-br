---
title: Automatizar tarefas de gerenciamento Em VMs do SQL (Resource Manager) | Microsoft Docs
description: "Este tópico descreve como gerenciar a extensão do agente do SQL Server, que automatiza tarefas de administração específicas do SQL Server. Entre elas estão o Backup Automatizado, a Aplicação de Patch Automatizada e a Integração do Cofre de Chaves do Azure. Este tópico usa o modelo de implantação do Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/09/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 56e3b3cd94a1fb7b4d05c06bc652b37d8a57c9e2
ms.lasthandoff: 03/25/2017

---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Automatizar tarefas de gerenciamento em máquinas virtuais do Azure com a Extensão do Agente do SQL Server (Resource Manager)
> [!div class="op_single_selector"]
> * [Gerenciador de Recursos](virtual-machines-windows-sql-server-agent-extension.md)
> * [Clássico](../classic/sql-server-agent-extension.md)
> 
> 

A extensão do SQL Server IaaS Agent (SQLIaaSExtension) é executada em máquinas virtuais do Azure para automatizar tarefas de administração. Este tópico fornece uma visão geral dos serviços compatível com a extensão, bem como instruções de instalação, status e remoção.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para exibir a versão clássica deste artigo, confira [Extensão do SQL Server Agent para VMs do SQL Server (Clássico)](../classic/sql-server-agent-extension.md).

## <a name="supported-services"></a>Serviços com suporte
A Extensão do Agente IaaS do SQL Server dá suporte às seguintes tarefas de administração:

| Recurso de administração | Descrição |
| --- | --- |
| **Backup Automatizado do SQL** |Automatiza o agendamento de backups para todos os bancos de dados da instância padrão do SQL Server na VM. Para saber mais, veja [Backup automatizado para SQL Server em máquinas virtuais do Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Aplicação de patch automatizada do SQL** |Configura um período de manutenção durante o qual as atualizações para sua VM podem ocorrer, evitando atualizações durante horários de pico de sua carga de trabalho. Para saber mais, veja [Aplicação de patch automatizada para o SQL Server em Máquinas Virtuais do Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Integração do Cofre da Chave do Azure** |Permite a instalação e configuração automática do Cofre de Chaves do Azure em sua VM do SQL Server. Para saber mais, confira [Configurar a integração do Cofre de Chaves do Azure para SQL Server em VMs do Azure (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Pré-requisitos
Requisitos para uso da extensão SQL Server IaaS Agent em sua VM:

**Sistema operacional**:

* Windows Server 2012
* Windows Server 2012 R2

**Versões do SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Baixar e configurar os comandos mais recentes do Azure PowerShell](/powershell/azureps-cmdlets-docs)

## <a name="installation"></a>Instalação
A Extensão do Agente IaaS do SQL Server é instalada automaticamente quando você provisiona uma das imagens da galeria de máquinas virtuais do SQL Server.

Se você criar uma máquina virtual somente do sistema operacional Windows Server, poderá instalar a extensão manualmente usando o cmdlet do PowerShell **Set-AzureVMSqlServerExtension** . Por exemplo, o comando a seguir instala a extensão em uma VM somente do sistema operacional Windows Server e fornece o nome "SQLIaaSExtension".

    Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2" -Location "East US 2"

Se você atualizar para a versão mais recente da Extensão do Agente IaaS SQL, deverá reiniciar a máquina virtual depois de atualizar a extensão.

> [!NOTE]
> Se instalar a Extensão do Agente IaaS do SQL Server manualmente em uma VM do Windows Server, você deverá usar e gerenciar seus recursos usando comandos do PowerShell. A interface do portal está disponível apenas para imagens da galeria do SQL Server.
> 
> 

## <a name="status"></a>Status
Uma maneira de verificar se a extensão está instalada é exibir o status do agente no Portal do Azure. Escolha **Todas as configurações** na folha da máquina virtual e clique em **Extensões**. Você deverá ver a extensão **SQLIaaSExtension** na lista.

![Extensão do Agente IaaS do SQL Server no Portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Você também pode usar o cmdlet **Get-AzureVMSqlServerExtension** do Azure PowerShell.

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

O comando anterior confirma se o agente está instalado e fornece informações gerais sobre o status. Você também pode obter informações específicas de status do Backup automatizado e aplicação de patch com os comandos a seguir.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Remoção
No Portal do Azure, você pode desinstalar a extensão clicando nas reticências na folha **Extensões** das propriedades de sua máquina virtual. Em seguida, clique em **Excluir**.

![Desinstalar a Extensão do Agente IaaS do SQL Server no Portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Você também pode usar o cmdlet do Powershell **Remove-AzureRmVMSqlServerExtension** .

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>Próximas etapas
Comece a usar um dos serviços com suporte da extensão. Para obter mais detalhes, confira os tópicos citados na seção [Serviços com suporte](#supported-services) deste artigo.

Para obter mais informações sobre como executar o SQL Server em Máquinas Virtuais do Azure, veja [Visão geral do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).


