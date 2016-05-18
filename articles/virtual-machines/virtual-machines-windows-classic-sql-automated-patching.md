<properties
	pageTitle="Aplicação de patch automatizada para VMs do SQL Server (Clássico) | Microsoft Azure"
	description="Explica o recurso de Aplicação de Patch Automatizada para Máquinas Virtuais do SQL Server em execução no Azure e usando o modo de implantação clássico."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="04/08/2016"
	ms.author="jroth" />

# Aplicação de patch automatizada para o SQL Server em Máquinas Virtuais do Azure (Clássico)

Aplicação de patch automatizada estabelece uma janela de manutenção para uma Máquina Virtual do Azure que executa o SQL Server 2012 ou 2014. Atualizações automáticas só podem ser instaladas durante esta janela de manutenção. Para o SQL Server, isso garante que as atualizações do sistema e qualquer reinicialização associada ocorrerão no melhor momento possível para o banco de dados. Depende do Agente IaaS do SQL Server.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.

## Configurar a aplicação de patch automatizada no portal do Azure

Você pode usar o [portal do Azure](http://go.microsoft.com/fwlink/?LinkID=525040&clcid=0x409) para configurar a aplicação de patch automatizada quando criar uma nova Máquina Virtual do SQL Server.

>[AZURE.NOTE] Aplicação de patch automatizada depende do Agente IaaS do SQL Server. Para instalar e configurar o agente, você deve ter o Agente de VM do Azure em execução na máquina virtual de destino. Imagens da galeria de máquinas virtuais mais recentes têm esta opção habilitada por padrão, porém o Agente de VM do Azure pode estar ausente em VMs existentes. Se você estiver usando sua própria imagem de VM, também será necessário instalar o Agente IaaS do SQL Server. Para obter mais informações, consulte [Agente de VM e Extensões](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

A captura de tela do portal do Azure a seguir mostra essas opções em **CONFIGURAÇÃO OPCIONAL** | **PATCHING AUTOMATIZADO DO SQL**.

![Aplicação de Patch Automática do SQL no Portal do Azure](./media/virtual-machines-windows-classic-sql-automated-patching/IC778484.jpg)

Para as máquinas virtuais existentes do SQL Server 2012 ou 2014, selecione as configurações de **Aplicação de patch automática** na seção **Configuração** das propriedades da máquina virtual. Na janela de **Aplicação de patch automatizada**, você pode habilitar o recurso, definir o agendamento de manutenção e a hora de início e escolher a duração da janela de manutenção. Isso é mostrado na captura de tela a seguir.

![Configuração de Aplicação de Patch Automatizada no Portal do Azure](./media/virtual-machines-windows-classic-sql-automated-patching/IC792132.jpg)

>[AZURE.NOTE] Quando você habilita a Aplicação de Patch Automatizada pela primeira vez, o Azure configura o Agente IaaS do SQL Server em segundo plano. Durante esse tempo, o portal do Azure não mostrará que a Aplicação de Patch Automatizada está configurada. Aguarde alguns minutos para que o agente seja instalado e configurado. Depois disso, o portal do Azure refletirá as novas configurações.

## Configurar a aplicação de patch automatizada com o PowerShell

Você também pode usar o PowerShell para configurar a aplicação de patch automatizada.

No exemplo a seguir, o PowerShell é usado para configurar a Aplicação de Patch Automatizada em uma VM existente do SQL Server. O comando **New-AzureVMSqlServerAutoPatchingConfig** configura uma nova janela de manutenção para atualizações automáticas.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Com base neste exemplo, a tabela a seguir descreve o efeito prático sobre a VM do Azure de destino:

|Parâmetro|Efeito|
|---|---|
|**DayOfWeek**|Patches instalados toda quinta-feira.|
|**MaintenanceWindowStartingHour**|Inicia as atualizações às 11h.|
|**MaintenanceWindowsDuration**|Os patches devem ser instalados dentro de 120 minutos. Com base na hora de início, eles devem estar concluídos até 13h.|
|**PatchCategory**|A única configuração possível para esse parâmetro é "Important".|

Pode demorar vários minutos para instalar e configurar o Agente IaaS do SQL Server.

Para desabilitar a Aplicação de Patch Automatizada, execute o mesmo script sem o parâmetro -Enable para New-AzureVMSqlServerAutoPatchingConfig. Assim como acontece com a instalação, pode demorar vários minutos para desabilitar a Aplicação de Patch Automatizada.

## Desabilitando e desinstalando o Agente IaaS do SQL Server

Se você quiser desabilitar o Agente IaaS do SQL Server para Backup e Aplicação de Patch Automatizados, use o seguinte comando:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -Disable | Update-AzureVM

Para desinstalar o Agente IaaS do SQL Server, use a seguinte sintaxe:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension –Uninstall | Update-AzureVM

Você também pode desinstalar a extensão usando o comando **Remove-AzureVMSqlServerExtension**:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Remove-AzureVMSqlServerExtension | Update-AzureVM

## Compatibilidade

Os produtos a seguir são compatíveis com os recursos do Agente IaaS do SQL Server para Aplicação de Patch Automatizada:

- Windows Server 2012

- Windows Server 2012 R2

- SQL Server 2012

- SQL Server 2014

## Próximas etapas

Um recurso relacionado para VMs do SQL Server no Azure é o [Backup Automatizado para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-classic-sql-automated-backup.md).

Examine outros [recursos para executar o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0413_2016-->