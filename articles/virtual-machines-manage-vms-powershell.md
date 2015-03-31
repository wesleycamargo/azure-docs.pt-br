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
   ms.date="02/20/2015"
   ms.author="kasing"/>

# Gerenciar suas máquinas virtuais usando o PowerShell do Azure

Antes de começar, você precisará certificar-se de que ter instalado o PowerShell do Azure. Para fazer isso, visite [Como instalar e configurar o PowerShell do Azure](../install-configure-powershell/)

## Obter uma imagem

Antes de criar uma máquina virtual, você precisará decidir **qual imagem usar**. Você poderá obter uma lista de imagens usando o seguinte cmdlet

      Get-AzureVMImage

Esse cmdlet retornará uma lista de todas as imagens disponíveis no Azure. Essa é uma lista muito longa e pode ser difícil encontrar a imagem exata que você deseja usar. No exemplo abaixo, estou usando outros cmdlets do PowerShell para reduzir a lista de imagens retornadas apenas para aqueles que contêm com base no **Datacenter do Windows Server 2012 R2**. Além disso, estou escolhendo obter somente a imagem mais recente, especificando [-1] para a matriz de imagens retornada

    $img = (Get-AzureVMImage | Select -Property ImageName, Label | where {$_.Label -like '*Windows Server 2012 R2 Datacenter*'})[-1]

## Criar uma máquina virtual

A criação de uma VM começa com o cmdlet **New-AzureVMConfig**. Aqui você especificará o **nome** da VM, **tamanho** da VM e a **imagem** a ser usada para a máquina virtual. Esse cmdlet cria um objeto VM local **$myVM** que é modificado posteriormente usando outros cmdlets do PowerShell do Azure neste guia.

      $myVM = New-AzureVMConfig -Name "testvm" -InstanceSize "Small" -ImageName $img.ImageName

Em seguida, será necessário escolher o **nome de usuário** e a **senha** para sua VM. Você pode fazer isso usando o cmdlet **Add-AzureProvisioningConfig**. Isso é onde você diz ao Azure qual é o sistema operacional da VM. Não que você ainda esteja fazendo alterações no objeto **$myVM** local.

    $user = "azureuser"
    $pass = "&Azure1^Pass@"
    $myVM = Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass

Finalmente, você está pronto para criar sua VM no Azure. Para fazer isso, você precisará usar o cmdlet **New-AzureVM**

> [AZURE.NOTE] Você precisará configurar o serviço de nuvem antes de criar sua VM. Há duas maneiras de fazer isso.
* Criar o serviço de nuvem usando o cmdlet New-AzureService. Se você escolher esse método, precisará certificar-se de que o local especificado no cmdlet New-AzureVM abaixo corresponde ao local do serviço de nuvem. Caso contrário, o cmdlet de execução New-AzureVM falhará.
* Permitir que o cmdlet New-AzureVM faça isso para você. Você precisará certificar-se de que o nome do serviço é exclusivo. Caso contrário, haverá falha na execução do cmdlet New-AzureVM.

    New-AzureVM -ServiceName "mytestserv" -VMs $myVM -Location "West US"

**OPCIONAL**

Você pode usar outros cmdlets como **Add-AzureDataDisk** e **Add-AzureEndpoint** para configurar opções adicionais para sua VM

## Obter uma VM
Agora que você criou uma máquina virtual no Azure, definitivamente desejará ver como ela está. Você pode fazer isso usando o cmdlet **Get-AzureVM**, conforme mostrado abaixo

    Get-AzureVM -ServiceName "mytestserv" -Name "testvm"


## Próximas etapas
[Conectar-se a uma máquina virtual do Azure com RDP ou SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx)<br>
[Perguntas frequentes sobre máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/dn683781.aspx)

<!--HONumber=47-->
