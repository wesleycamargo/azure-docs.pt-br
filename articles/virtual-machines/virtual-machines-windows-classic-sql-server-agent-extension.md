<properties
	pageTitle="Extensão do Agente IaaS do SQL Server (Clássico) | Microsoft Azure"
	description="Este tópico descreve a extensão do agente do SQL Server, que habilita uma VM executando o SQL Server no Azure a usar recursos de automação. Este tópico usa o modo de implantação clássico."
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
	ms.date="04/08/2016"
	ms.author="jroth"/>

# Extensão do agente IaaS do SQL Server (Clássico)

Essa extensão permite que o SQL Server em máquinas virtuais do Azure usem determinados serviços, listados neste artigo, que só podem ser usados com essa extensão instalada. Essa extensão é instalada automaticamente para imagens da Galeria de Imagens do SQL Server no portal do Azure. Ela pode ser instalada em qualquer VM do SQL Server no Azure que tenha o Agente convidado da VM Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


## Pré-requisitos
Requisitos para uso de cmdlets do Powershell:

- Azure PowerShell mais recente [disponível aqui](../powershell-install-configure.md)

Requisitos para uso da extensão em sua VM:

- Agente convidado da VM Azure
- Windows Server 2012, Windows Server 2012 R2 ou posterior
- SQL Server 2012, SQL Server 2014 ou posterior

## Serviços disponíveis com a extensão

- **Backup Automatizado SQL**: este serviço automatiza o agendamento de backups para todos os bancos de dados da instância padrão do SQL Server na VM. Para obter mais informações, veja [Backup Automatizado para o SQL Server em Máquinas Virtuais do Azure (Clássico)](virtual-machines-windows-classic-sql-automated-backup.md).
- **Aplicação de Patch Automatizada de SQL**: este serviço permite que você configure um período de manutenção durante o qual as atualizações para sua VM podem ocorrer, para que você possa evitar atualizações durante horários de pico em sua carga de trabalho. Para obter mais informações, confira [Aplicação de patch automatizada para SQL Server em máquinas virtuais do Azure (Clássico)](virtual-machines-windows-classic-sql-automated-patching.md).
- **Integração do Cofre de Chaves do Azure**: este serviço permite a você instalar e configurar automaticamente o cofre de chaves do Azure na sua VM do SQL Server. Para saber mais, confira [Configurar a Integração do Cofre de Chaves do Azure para o SQL Server em VMs do Azure (Clássico)](virtual-machines-windows-classic-ps-sql-keyvault.md).

## Adicionar a extensão com o Powershell
Se você provisionar sua VM do SQL Server usando o [portal do Azure](virtual-machines-windows-portal-sql-server-provision.md), a extensão será instalada automaticamente. Para VMs do SQL Server configuradas com o portal clássico do Azure, ou para VMs para as quais você traz sua própria licença do SQL, é possível adicionar essa extensão usando o cmdlet **Set-AzureVMSqlServerExtension** do Azure PowerShell.

### Sintaxe

Set-AzureVMSqlServerExtension [[-ReferenceName] [String]] [-VM] IPersistentVM [[-Version] [String]] [[-AutoPatchingSettings] [AutoPatchingSettings]] [-AutoBackupSettings[AutoBackupSettings]] [-Profile [AzureProfile]] [CommonParameters]

> [AZURE.NOTE] Recomendamos a omissão do parâmetro –Version. Sem ele, o padrão é a versão mais recente da extensão.

### Exemplo
O exemplo a seguir define as configurações de backup automático usando uma configuração definida em $abs (não mostrada aqui). O serviceName é o nome de serviço de nuvem que hospeda a máquina virtual. Para um exemplo completo, veja [Backup Automatizado para o SQL Server em Máquinas Virtuais do Azure (Clássico)](virtual-machines-windows-classic-sql-automated-backup.md).

	Get-AzureVM –ServiceName "serviceName" –Name "vmName" | Set-AzureVMSqlServerExtension –AutoBackupSettings $abs | Update-AzureVM**

## Verificar o status da extensão
Se você quiser verificar o status dessa extensão e os serviços associados a ela, use um dos portais. Nos detalhes de sua VM existente, localize **Extensões** em **Configurações**.

Você também pode usar o cmdlet **Get-AzureVMSqlServerExtension** do Azure PowerShell.

### Sintaxe

Get-AzureVMSqlServerExtension [[-VM] [IPersistentVM]] [-Profile [AzureProfile]] [CommonParameters]

### Exemplo
	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Remover a extensão com o Powershell   
Se você quiser remover essa extensão de sua VM, use o cmdlet **Remove-AzureVMSqlServerExtension** do Azure PowerShell.

### Sintaxe

Remove-AzureVMSqlServerExtension [-Profile [AzureProfile]] -VM IPersistentVM [CommonParameters]

<!---HONumber=AcomDC_0413_2016-->