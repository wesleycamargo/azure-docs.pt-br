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
	ms.date="09/26/2016"
	ms.author="jroth"/>

# Extensão do SQL Server Agent para VMs do SQL Server (Clássico)

> [AZURE.SELECTOR]
- [Gerenciador de Recursos](virtual-machines-windows-sql-server-agent-extension.md)
- [Clássico](virtual-machines-windows-classic-sql-server-agent-extension.md)

A Extensão de Agente IaaS do SQL Server (SQLIaaSAgent) é executada em máquinas virtuais do Azure para automatizar tarefas de administração. Este tópico fornece uma visão geral dos serviços compatível com a extensão, bem como instruções de instalação, status e remoção.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
Para exibir a versão do Gerenciador de Recursos deste artigo, veja [Extensão do SQL Server Agent para o Gerenciador de Recursos de VMs do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## Serviços com suporte

A Extensão do Agente IaaS do SQL Server dá suporte às seguintes tarefas de administração:

| Recurso de administração | Descrição |
|---------------------|-------------------------------|
| **Backup Automatizado do SQL** | Automatiza o agendamento de backups para todos os bancos de dados da instância padrão do SQL Server na VM. Para saber mais, veja [Backup automatizado para o SQL Server em Máquinas Virtuais do Azure (Clássico)](virtual-machines-windows-classic-sql-automated-backup.md).|
| **Aplicação de patch automatizada do SQL** | Configura um período de manutenção durante o qual as atualizações para sua VM podem ocorrer, evitando atualizações durante horários de pico de sua carga de trabalho. Para obter mais informações, confira [Aplicação de patch automatizada para SQL Server em máquinas virtuais do Azure (Clássico)](virtual-machines-windows-classic-sql-automated-patching.md).|
| **Integração do Cofre da Chave do Azure** | Permite a instalação e configuração automática do Cofre de Chaves do Azure em sua VM do SQL Server. Para saber mais, confira [Configurar a Integração do Cofre de Chaves do Azure para o SQL Server em VMs do Azure (Clássico)](virtual-machines-windows-classic-ps-sql-keyvault.md).|

## Pré-requisitos

Requisitos para uso da extensão SQL Server IaaS Agent em sua VM:

### Sistema operacional:

- Windows Server 2012
- Windows Server 2012 R2

### Versões do SQL Server:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

### PowerShell do Azure:

[Baixe e configure os comandos mais recentes do Azure PowerShell](../powershell-install-configure.md).

Em seguida, conecte o Windows PowerShell à sua assinatura do Azure usando o comando **Add-AzureAccount**.

	Add-AzureAccount

Se você tiver várias assinaturas, deverá usar **Select-AzureSubscription** para selecionar a assinatura contendo o destino de VM clássico.

	Select-AzureSubscription -SubscriptionName <subscriptionname>

Neste ponto você pode, por meio do comando **Get-AzureVM**, obter uma lista de máquinas virtuais clássicas e seus nomes de serviço associados.

	Get-AzureVM

## Instalação

Para VMs clássicas, você deve usar o PowerShell para instalar a extensão do agente do SQL Server IaaS e configurar seus serviços associados. Use o cmdlet **Set-AzureVMSqlServerExtension** do PowerShell para instalar a extensão. Por exemplo, o comando a seguir instala a extensão em uma VM do Windows Server (clássico) e fornece o nome "SQLIaaSExtension".

	Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Se você atualizar para a versão mais recente da Extensão do Agente IaaS SQL, deverá reiniciar a máquina virtual depois de atualizar a extensão.

>[AZURE.NOTE] Máquinas virtuais clássicas não têm uma opção para instalar e configurar a extensão do agente SQL IaaS através do portal.

## Status

Uma maneira de verificar se a extensão está instalada é exibir o status do agente no Portal do Azure. Escolha **Todas as configurações** na folha da máquina virtual e clique em **Extensões**. Você deverá ver a extensão **SQLIaaSAgent** na lista.

![Extensão do Agente IaaS do SQL Server no Portal do Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Você também pode usar o cmdlet **Get-AzureVMSqlServerExtension** do Azure PowerShell.

	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Remoção   

No Portal do Azure, você pode desinstalar a extensão clicando nas reticências na folha **Extensões** das propriedades de sua máquina virtual. Em seguida, clique em **Excluir**.

![Desinstalar a Extensão do Agente IaaS do SQL Server no Portal do Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Você também pode usar o cmdlet **Remove-AzureVMSqlServerExtension** do Powershell.

	Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## Próximas etapas

Comece a usar um dos serviços com suporte da extensão. Para obter mais detalhes, confira os tópicos citados na seção [Serviços com suporte](#supported-services) deste artigo.

Para obter mais informações sobre como executar o SQL Server em Máquinas Virtuais do Azure, veja [Visão geral do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0928_2016-->