<properties
   pageTitle="manage-vms-azure-powershell"
   description="Gerenciar suas VMs usando o PowerShell do Azure"
   services="virtual-machines"
   documentationCenter="windows"
   authors="singhkay"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="06/24/2015"
   ms.author="kasing"/>

# Gerenciar suas máquinas virtuais usando o PowerShell do Azure

Muitas tarefas realizadas diariamente para gerenciar suas VMs podem ser automatizadas usando cmdlets do PowerShell do Azure. Este artigo fornece comandos de exemplo para tarefas mais simples e links para artigos que mostram os comandos para tarefas mais complexas.

>[AZURE.NOTE]Se ainda não instalou e configurou o PowerShell do Azure, você pode obter instruções [aqui](../install-configure-powershell.md).

## Como Usar os Comandos de Exemplo
Você precisará substituir parte do texto nos comandos por texto apropriado para seu ambiente. Os símbolos < and > indicam o texto que você precisa substituir. Ao substituir o texto, remova os símbolos, mas mantenha as aspas.

## Obter uma VM
Essa é uma tarefa básica que você usará com frequência. Use-a para obter informações sobre uma VM, executar tarefas em uma VM ou obter a saída para armazenar em uma variável.

Para obter informações sobre a VM, execute este comando, substituindo tudo entre aspas, inclusive os caracteres < and >:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Para armazenar a saída em uma variável $vm, execute:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Faça logon em uma máquina virtual baseada no Windows

Execute estes comandos:

>[AZURE.NOTE]Você pode obter o nome do serviço de nuvem e de máquina virtual na exibição do comando **Get-AzureVM**.
>
	$svcName="<cloud service name>"
	$vmName="<virtual machine name>"
	$localPath="<drive and folder location to store the downloaded RDP file, example: c:\temp >"
	$localFile=$localPath + "" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## Parar uma VM

Execute este comando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Use esse parâmetro para manter o VIP (IP virtual) do serviço de nuvem, caso essa seja a última VM no serviço de nuvem. <br><br> Se usar o parâmetro StayProvisioned, você ainda será cobrado pela VM.

## Iniciar uma VM

Execute este comando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Anexar um Disco de Dados
Essa tarefa requer algumas etapas. Primeiro, use o cmdlet ****Add-AzureDataDisk**** para adicionar o disco ao objeto $vm. Em seguida, use o cmdlet Update-AzureVM para atualizar a configuração da VM.

Você também precisará decidir se deseja anexar um novo disco ou um que contenha dados. Para um novo disco, o comando cria o arquivo .vhd e anexa-o no mesmo comando.

Para anexar um novo disco, execute este comando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM <$vm> `
              | Update-AzureVM

Para anexar discos de dados existentes, execute este comando:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> `
              | Update-AzureVM

Para anexar discos de dados de um arquivo .vhd existente no armazenamento de blob, execute este comando:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> `
              | Update-AzureVM

## Criar uma VM do Windows

Para criar uma nova máquina virtual baseada no Windows no Azure, use as instruções em [Usar o PowerShell do Azure para criar e pré-configurar máquinas virtuais baseadas no Windows](virtual-machines-ps-create-preconfigure-windows-vms.md). Este tópico o orienta durante a criação de um conjunto de comandos do PowerShell que cria uma máquina virtual do Windows que pode ser pré-configurada com:

- Associação de domínio do Active Directory
- Discos adicionais
- Como membro de um conjunto de balanceamento de carga existente
- Um endereço IP estático

<!---HONumber=July15_HO2-->