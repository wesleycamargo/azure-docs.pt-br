<properties 
	pageTitle="SQL Server nas Máquinas Virtuais do Azure" 
	description="Descreve a extensão do agente do SQL Server, que permite que máquinas virtuais executando o SQL Server na nuvem no Azure usem recursos de automação, e como instalar o agente, se ele não tiver sido instalado automaticamente." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="jeffgoll" 
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="06/17/2015"
	ms.author="jeffreyg"/>

# Extensão do agente IaaS do SQL Server

Essa extensão permite que o SQL Server em máquinas virtuais do Azure usem determinados serviços, listados neste artigo, que só podem ser usados com essa extensão instalada. Essa extensão é instalada automaticamente para imagens da Galeria de Imagens do SQL Server no Portal de Visualização do Azure. Ela pode ser instalada em qualquer VM do SQL Server no Azure que tenha o Agente convidado da VM Azure.
 
## Pré-requisitos
Requisitos para uso de cmdlets do Powershell:

- SDK de linha de comando do Azure mais recente, [disponível aqui](http://azure.microsoft.com/downloads/)

Requisitos para uso da extensão em sua VM:

- Agente convidado da VM Azure
- Windows Server 2012, Windows Server 2012 R2 ou posterior
- SQL Server 2012, SQL Server 2014 ou posterior
 
## Serviços disponíveis com a extensão

- **Backup automatizado SQL**: este serviço automatiza o agendamento de backups para todos os bancos de dados da instância padrão do SQL Server na VM. Para saber mais sobre esse serviço, consulte [Backup automatizado para o SQL Server em máquinas virtuais Azure](https://msdn.microsoft.com/library/azure/dn906091.aspx).
- **Correção automatizada de SQL**: este serviço permite que você configure um período de manutenção durante o qual as atualizações para sua VM podem ocorrer, para que você possa evitar atualizações durante horários de pico em sua carga de trabalho. Para ver mais informações sobre este serviço, consulte [Correção automatizada para SQL Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/dn961166.aspx).

## Adicionar a extensão com o Powershell
Se você provisionar sua VM do SQL Server usando o [Portal de Visualização do Azure](https://portal.azure.com/), a extensão será instalada automaticamente. Para VMs do SQL Server configuradas com o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), ou para VMs as quais você traz sua própria licença do SQL, é possível adicionar essa extensão a uma VM existente usando o seguinte cmdlet do PowerShell do Azure.

**Set-AzureVMSqlServerExtension**

### Sintaxe

Set-AzureVMSqlServerExtension [-VM] <IPersistentVM> [[-Version] <string>] [-AutoBackupSettings <AutoBackupSettings>] [-AutoPatchingSetttings <AutoPatchingSetttings>] [-Confirm] [-WhatIf] [<CommonParameters>]

> [AZURE.NOTE]Recomendamos a omissão do parâmetro –Version. Sem ele, o padrão é a versão mais recente da extensão.

### Exemplo
	Get-AzureVM –ServiceName serviceName –Name vmName | Set-AzureVMSqlServerExtension –AutoBackupSettings $abs | Update-AzureVM**

## Verificar o status da extensão
Se você quiser verificar o status dessa extensão e os serviços associados a ela, use um dos portais. Nos detalhes de sua VM existente, localize **Extensões** em **Configurações**.

Você também pode usar o seguinte cmdlet do Powershell do Azure:

**Get-AzureVMSqlServerExtension**

### Sintaxe

Get-AzureVMSqlServerExtension [[-VM] <IPersistentVM>] [[-Version] <string>] [<CommonParameters>]

> [AZURE.NOTE]Você pode omitir o parâmetro –Version. Sem ele, o padrão é a versão mais recente da extensão.

### Exemplo
	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Remover a extensão com o Powershell   
Se você quiser remover essa extensão de sua VM, usar o seguinte cmdlet do Powershell do Azure.

**Remove-AzureVMSqlServerExtension**

### Sintaxe
Remove-AzureVMSqlServerExtension -VM <IPersistentVM> [<CommonParameters>]

<!---HONumber=62-->