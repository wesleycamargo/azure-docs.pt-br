<properties
	pageTitle="Extensão do SQL Server Agent para VMs do SQL Server (Clássico) | Microsoft Azure"
	description="Este tópico descreve como gerenciar a extensão do agente do SQL Server, que automatiza tarefas de administração específicas do SQL Server. Entre elas estão o Backup Automatizado, a Aplicação de Patch Automatizada e a Integração do Cofre de Chaves do Azure. Este tópico usa o modo de implantação clássico."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/16/2016"
	ms.author="jroth"/>

# Extensão do SQL Server Agent para VMs do SQL Server (Clássico)

> [AZURE.SELECTOR]
- [Gerenciador de Recursos](virtual-machines-windows-sql-server-agent-extension.md)
- [Clássico](virtual-machines-windows-classic-sql-server-agent-extension.md)

A Extensão de Agente IaaS do SQL Server (SQLIaaSAgent) é executada em máquinas virtuais do Azure para automatizar tarefas de administração. Este tópico fornece uma visão geral dos serviços que recebem suporte da extensão, bem como instruções de instalação, status e remoção.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos. Para exibir a versão do Gerenciador de Recursos deste artigo, veja [Extensão do SQL Server Agent para o Gerenciador de Recursos de VMs do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## Serviços com suporte

A Extensão do Agente IaaS do SQL Server dá suporte às seguintes tarefas de administração:

| Recurso de administração | Descrição |
|---------------------|-------------------------------|
| **Backup Automatizado do SQL** | Automatiza o agendamento de backups para todos os bancos de dados da instância padrão do SQL Server na VM. Para saber mais, veja [Backup automatizado para o SQL Server em Máquinas Virtuais do Azure (Clássico)](virtual-machines-windows-classic-sql-automated-backup.md).|
| **Aplicação de patch automatizada do SQL** | Configura um período de manutenção durante o qual as atualizações para sua VM podem ocorrer, evitando atualizações durante horários de pico de sua carga de trabalho. Para obter mais informações, confira [Aplicação de patch automatizada para SQL Server em máquinas virtuais do Azure (Clássico)](virtual-machines-windows-classic-sql-automated-patching.md).|
| **Integração do Cofre da Chave do Azure** | Permite a instalação e configuração automática do Cofre de Chaves do Azure em sua VM do SQL Server. Para saber mais, confira [Configurar a Integração do Cofre de Chaves do Azure para o SQL Server em VMs do Azure (Clássico)](virtual-machines-windows-classic-ps-sql-keyvault.md).|

## Pré-requisitos

Requisitos para uso da extensão SQL Server IaaS Agent em sua VM:

- Agente Convidado de VM do Azure (extensão BGInfo instalada automaticamente em novas VMs do Azure).
- Windows Server 2012, Windows Server 2012 R2 ou posterior.
- SQL Server 2012, SQL Server 2014 ou posterior.

Requisitos para uso de cmdlets do Powershell:

- Azure PowerShell mais recente [disponível aqui](../powershell-install-configure.md).

## Instalação

A Extensão do Agente IaaS do SQL Server é instalada automaticamente quando você provisiona uma das imagens da galeria de máquinas virtuais do SQL Server.

Se você criar uma máquina virtual somente do sistema operacional Windows Server, poderá instalar a extensão manualmente usando o cmdlet do PowerShell **Set-AzureVMSqlServerExtension**. Use o comando para configurar um dos serviços do agente, como a Aplicação de Patch Automatizada. A VM instalará o agente se ele não estiver instalado.

>[AZURE.NOTE] Para obter instruções sobre o uso do cmdlet **Set-AzureVMSqlServerExtension** do PowerShell, veja os tópicos individuais na seção [Serviços com suporte](#supported-services) deste artigo.

## Status

Uma maneira de verificar se a extensão está instalada é exibir o status do agente no Portal do Azure. Escolha **Todas as configurações** na folha da máquina virtual e clique em **Extensões**. Você deverá ver a extensão **SQLIaaSAgent** na lista.

![Extensão do Agente IaaS do SQL Server no Portal do Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Você também pode usar o cmdlet **Get-AzureVMSqlServerExtension** do Azure Powershell.

	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Remoção   

No Portal do Azure, você pode desinstalar a extensão clicando nas reticências na folha **Extensões** das propriedades de sua máquina virtual. Em seguida, clique em **Excluir**.

![Desinstalar a Extensão do Agente IaaS do SQL Server no Portal do Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Você também pode usar o cmdlet **Remove-AzureVMSqlServerExtension** do Powershell.

	Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## Próximas etapas

Comece a usar um dos serviços com suporte da extensão. Para obter mais detalhes, confira os tópicos citados na seção [Serviços com suporte](#supported-services) deste artigo.

Para obter mais informações sobre como executar o SQL Server em Máquinas Virtuais do Azure, veja [Visão geral do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0518_2016-->