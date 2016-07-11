<properties
   pageTitle="Gerenciar suas máquinas virtuais usando o Azure PowerShell | Microsoft Azure"
   description="Aprenda os comandos que você pode usar para automatizar tarefas de gerenciamento de suas máquinas virtuais."
   services="virtual-machines-windows"
   documentationCenter="windows"
   authors="singhkay"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

   <tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/31/2016"
   ms.author="kasing"/>

# Gerenciar suas máquinas virtuais usando o Azure PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Muitas tarefas realizadas diariamente para gerenciar suas VMs podem ser automatizadas usando cmdlets do Azure PowerShell. Este artigo fornece comandos de exemplo para tarefas mais simples e links para artigos que mostram os comandos para tarefas mais complexas.

>[AZURE.NOTE] Se você ainda não instalou e configurou o Azure PowerShell, você pode obter instruções no artigo [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

## Como usar os comandos de exemplo
Você precisará substituir parte do texto nos comandos por texto apropriado para seu ambiente. O < e > símbolos indicam o texto que você deve substituir. Ao substituir o texto, remova os símbolos, mas mantenha as aspas.

## Obter uma VM
Essa é uma tarefa básica que você usará com frequência. Use-a para obter informações sobre uma VM, executar tarefas em uma VM ou obter a saída para armazenar em uma variável.

Para obter informações sobre a VM, execute este comando, substituindo tudo entre aspas, incluindo os caracteres < e >:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Para armazenar a saída em uma variável $vm, execute:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Faça logon em uma VM baseada em Windows

Execute estes comandos:

>[AZURE.NOTE] Você pode obter o nome do serviço de nuvem e de máquina virtual na exibição do comando **Get-AzureVM**.
>
	$svcName = "<cloud service name>"
	$vmName = "<virtual machine name>"
	$localPath = "<drive and folder location to store the downloaded RDP file, example: c:\temp >"
	$localFile = $localPath + "" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## Parar uma VM

Execute este comando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT] Use esse parâmetro para manter o VIP (IP virtual) do serviço de nuvem, caso essa seja a última VM no serviço de nuvem. <br><br> Se usar o parâmetro StayProvisioned, você ainda será cobrado pela VM.

## Iniciar uma VM

Execute este comando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Anexar um disco de dados
Essa tarefa requer algumas etapas. Primeiro, use o cmdlet ****Add-AzureDataDisk**** para adicionar o disco ao objeto $vm. Em seguida, use o cmdlet **Update-AzureVM** para atualizar a configuração da VM.

Você também precisará decidir se deseja anexar um novo disco ou um que contenha dados. Para um novo disco, o comando cria o arquivo .vhd e o anexa.

Para anexar um novo disco, execute este comando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Para anexar um disco de dados existente, execute este comando:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Para anexar discos de dados de um arquivo .vhd existente no armazenamento de blob, execute este comando:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## Criar uma VM baseada no Windows

Para criar uma nova máquina virtual baseada no Windows no Azure, use as instruções em [Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Windows](virtual-machines-windows-classic-create-powershell.md). Este tópico o orienta durante a criação de um conjunto de comandos do Azure PowerShell que cria uma VM baseada em Windows que pode ser pré-configurada:

- Com associação de domínio do Active Directory.
- Com discos adicionais.
- Como membro de um conjunto de balanceamento de carga existente.
- Com um endereço IP estático.

<!---HONumber=AcomDC_0629_2016-->